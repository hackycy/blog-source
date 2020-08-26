---
title: egg-core源码分析
date: 2020-08-26 11:17:20
tags:
    - Nodejs
    - Egg
categories:
    - Nodejs
---

# 前言

最近在翻查[egg-cool-router](https://www.npmjs.com/package/egg-cool-router)源码查看如何使用装饰器来注册egg路由，看到了一些很牛逼的操作，想知道为什么就查了一下关于源码讲解的文章，解惑了所以摘抄了下来。

<!-- more -->

# egg-core是什么

## 应用、框架、插件之间的关系

在学习egg-core是什么之前，我们先了解一下关于Egg框架中应用，框架，插件这三个概念及其之间的关系：

- 一个应用必须指定一个框架才能运行起来，根据需要我们可以给一个应用配置多个不同的插件
- 插件只完成特定独立的功能，实现即插即拔的效果
- 框架是一个启动器，必须有它才能运行起来。框架还是一个封装器，它可以在已有框架的基础上进行封装，框架也可以配置插件，其中Egg，EggCore都是框架
- 在框架的基础上还可以扩展出新的框架，也就是说框架是可以无限级继承的，有点像类的继承
- 框架/应用/插件的关于service/controler/config/middleware的目录结构配置基本相同，称之为加载单元（loadUnit），包括后面源码分析中的getLoadUnits都是为了获取这个结构

```
# 加载单元的目录结构如下图，其中插件和框架没有controller和router.js
# 这个目录结构很重要，后面所有的load方法都是针对这个目录结构进行的
        loadUnit
        ├── package.json
        ├── app
        │   ├── extend
        │   |   ├── helper.js
        │   |   ├── request.js
        │   |   ├── response.js
        │   |   ├── context.js
        │   |   ├── application.js
        │   |   └── agent.js
        │   ├── service
        |   ├── controller
        │   ├── middleware
        │   └── router.js
        └── config
            ├── config.default.js
            ├── config.prod.js
            ├── config.test.js
            ├── config.local.js
            └── config.unittest.js
```

## eggCore的主要工作

egg.js的大部分核心代码实现都在[egg-core库](https://github.com/eggjs/egg-core)中，egg-core主要export四个对象:

- EggCore类：继承于Koa，做一些初始化工作，EggCore中最主要的一个属性是loader，也就是egg-core的导出的第二个类EggLoader的实例
- EggLoader类：整个框架目录结构（controller，service，middleware，extend，route.js）的加载和初始化工作都在该类中实现的，主要提供了几个load函数(loadPlugin,loadConfig,loadMiddleware,loadService,loadController,loadRouter等)，这些函数会根据指定目录结构下文件输出形式不同进行适配，最终挂载输出内容。
- BaseContextClass类：这个类主要是为了我们在使用框架开发时，在controller和service作为基类使用，只有继承了该类，我们才可以通过this.ctx获取到当前请求的上下文对象
- utils对象：几个主要的函数，包括转换成中间件函数middleware，根据不同类型文件获取文件导出内容函数loadFile等

所以egg-core做的主要事情就是根据loadUnit的目录结构规范，将目录结构中的config，controller，service，middleware，plugin，router等文件load到app或者context上，开发人员只要按照这套约定规范，就可以很方便进行开发，以下是EggCore的exports对象源码：

```javascript
//egg-core源码 -> index文件导出的数据结构
const EggCore = require('./lib/egg');
const EggLoader = require('./lib/loader/egg_loader');
const BaseContextClass = require('./lib/utils/base_context_class');
const utils = require('./lib/utils');

module.exports = {
  EggCore,
  EggLoader,
  BaseContextClass,
  utils,
};
```

### EggLoader的具体实现源码学习

#### EggCore类源码学习

EggCore类是算是上文提到的框架范畴，它从Koa类继承而来，并做了一些初始化工作，其中有三个主要属性是：

- loader：这个对象是EggLoader的实例，定义了多个load函数，用于对loadUnit目录下的文件进行加载，后面后专门讲这个类的是实现
- router：是EggRouter类的实例，从[koa-router](https://github.com/alexmingoia/koa-router)继承而来，用于egg框架的路由管理和分发，这个类的实现在后面的loadRouter函数会有说明
- lifecycle：这个属性用于app的生命周期管理，由于和整个文件加载逻辑关系不大，所以这里不作说明

```javascript
//egg-core源码 -> EggCore类的部分实现

const KoaApplication = require('koa');
const EGG_LOADER = Symbol.for('egg#loader');

class EggCore extends KoaApplication {
    constructor(options = {}) {
        super();
        const Loader = this[EGG_LOADER];
        //初始化loader对象
        this.loader = new Loader({
            baseDir: options.baseDir,          //项目启动的根目录
            app: this,                         //EggCore实例本身
            plugins: options.plugins,          //自定义插件配置信息，设置插件配置信息有多种方式，后面我们会讲
            logger: this.console,             
            serverScope: options.serverScope, 
        });
    }
    get [EGG_LOADER]() {
        return require('./loader/egg_loader');
    }
    //router对象
    get router() {
        if (this[ROUTER]) {
          return this[ROUTER];
        }
        const router = this[ROUTER] = new Router({ sensitive: true }, this);
        // register router middleware
        this.beforeStart(() => {
          this.use(router.middleware());
        });
        return router;
    }
    //生命周期对象初始化
    this.lifecycle = new Lifecycle({
        baseDir: options.baseDir,
        app: this,
        logger: this.console,
    });
}
```

#### EggLoader类源码学习

如果说eggCore是egg框架的精华所在，那么eggLoader可以说是eggCore的精华所在，下面我们主要从EggLoader的实现细节开始学习eggCore这个库：

EggLoader首先对app中的一些基本信息（pkg/eggPaths/serverEnv/appInfo/serverScope/baseDir等）进行整理，并且定义一些基础共用函数(getEggPaths/getTypeFiles/getLoadUnits/loadFile)，所有的这些基础准备都是为了后面介绍的几个load函数作准备，我们下面看一下其基础部分的实现：

```javascript
//egg-core源码 -> EggLoader中基本属性和基本函数的实现

class EggLoader {
    constructor(options) {
        this.options = options;
        this.app = this.options.app;
        //pkg是根目录的package.json输出对象
        this.pkg = utility.readJSONSync(path.join(this.options.baseDir, 'package.json'));
        //eggPaths是所有框架目录的集合体，虽然我们上面提到一个应用只有一个框架，但是框架可以在框架的基础上实现多级继承，所以是多个eggPath
        //在实现框架类的时候，必须指定属性Symbol.for('egg#eggPath')，这样才能找到框架的目录结构
        //下面有关于getEggPaths函数的实现分析
        this.eggPaths = this.getEggPaths();
        this.serverEnv = this.getServerEnv();
        //获取app的一些基本配置信息(name,baseDir,env,scope,pkg等)
        this.appInfo = this.getAppInfo();
        this.serverScope = options.serverScope !== undefined
            ? options.serverScope
            : this.getServerScope();
    }
    //递归获取继承链上所有eggPath
    getEggPaths() {
        const EggCore = require('../egg');
        const eggPaths = [];
        let proto = this.app;
        //循环递归的获取原型链上的框架Symbol.for('egg#eggPath')属性
        while (proto) {
            proto = Object.getPrototypeOf(proto);
            //直到proto属性等于EggCore本身，说明到了最上层的框架类，停止循环
            if (proto === Object.prototype || proto === EggCore.prototype) {
                break;
            }
            const eggPath = proto[Symbol.for('egg#eggPath')];
            const realpath = fs.realpathSync(eggPath);
            if (!eggPaths.includes(realpath)) {
                eggPaths.unshift(realpath);
            }
        }
        return eggPaths;
    }
    
    //函数输入：config或者plugin；函数输出：当前环境下的所有配置文件
    //该函数会根据serverScope，serverEnv的配置信息，返回当前环境对应filename的所有配置文件
    //比如我们的serverEnv=prod，serverScope=online，那么返回的config配置文件是['config.default', 'config.prod', 'config.online_prod']
    //这几个文件加载顺序非常重要，因为最终获取到的config信息会进行深度的覆盖，后面的文件信息会覆盖前面的文件信息
    getTypeFiles(filename) {
        const files = [ `${filename}.default` ];
        if (this.serverScope) files.push(`${filename}.${this.serverScope}`);
        if (this.serverEnv === 'default') return files;

        files.push(`${filename}.${this.serverEnv}`);
        if (this.serverScope) files.push(`${filename}.${this.serverScope}_${this.serverEnv}`);
        return files;
    }
    
    //获取框架、应用、插件的loadUnits目录集合，上文有关于loadUnits的说明
    //这个函数在下文中介绍的loadSerivce,loadMiddleware,loadConfig,loadExtend中都会用到，因为plugin，framework，app中都会有关系这些信息的配置
    getLoadUnits() {
        if (this.dirs) {
            return this.dirs;
        }
        const dirs = this.dirs = [];
        //插件目录，关于orderPlugins会在后面的loadPlugin函数中讲到
        if (this.orderPlugins) {
            for (const plugin of this.orderPlugins) {
                dirs.push({
                    path: plugin.path,
                    type: 'plugin',
                });
            }
        }
        //框架目录
        for (const eggPath of this.eggPaths) {
            dirs.push({
                path: eggPath,
                type: 'framework',
            });
        }
        //应用目录
        dirs.push({
            path: this.options.baseDir,
            type: 'app',
        });
        return dirs;
    }

    //这个函数用于读取某个LoadUnit下的文件具体内容，包括js文件，json文件及其它普通文件
    loadFile(filepath, ...inject) {
        if (!filepath || !fs.existsSync(filepath)) {
            return null;
        }
        if (inject.length === 0) inject = [ this.app ];
        let ret = this.requireFile(filepath);
        //这里要注意，如果某个js文件导出的是一个函数，且不是一个Class，那么Egg认为这个函数的格式是：app => {},输入是EggCore实例，输出是真正需要的信息
        if (is.function(ret) && !is.class(ret)) {
            ret = ret(...inject);
        }
        return ret;
    }
}
```

### 各种loader函数的实现源码分析

上文中只是介绍了EggLoader中的一些基本属性和函数，那么如何将LoadUnits中的不同类型的文件分别加载进来呢，egg-core中每一种类型（service/controller等）的文件加载都在一个独立的文件里实现。比如我们加载controller文件可以通过’./mixin/controller’目录下的loadController完成，加载service文件可以通过’./mixin/service’下的loadService函数完成，然后将这些方法挂载EggLoader的原型上，这样就可以直接在EggLoader的实例上使用

```javascript
//egg-core源码 -> 混入不同目录文件的加载方法到EggLoader的原型上

const loaders = [
  require('./mixin/plugin'),            //loadPlugin方法
  require('./mixin/config'),            //loadConfig方法
  require('./mixin/extend'),            //loadExtend方法
  require('./mixin/custom'),            //loadCustomApp和loadCustomAgent方法
  require('./mixin/service'),           //loadService方法
  require('./mixin/middleware'),        //loadMiddleware方法
  require('./mixin/controller'),        //loadController方法
  require('./mixin/router'),            //loadRouter方法
];

for (const loader of loaders) {
  Object.assign(EggLoader.prototype, loader);
}
```

我们按照上述loaders中定义的元素顺序，对各个load函数的源码实现进行一一分析：

#### loadPlugin函数

插件是一个迷你的应用，没有包含router.js和controller文件夹，我们上文也提到，应用和框架里都可以包含插件，而且还可以通过环境变量和初始化参数传入，关于插件初始化的几个参数：

- enable： 是否开启插件
- env： 选择插件在哪些环境运行
- path： 插件的所在路径
- package： 和path只能设置其中一个，根据package名称去node_modules里查询plugin，后面源码里有详细说明

```javascript
//egg-core源码 -> loadPlugin函数部分源码

loadPlugin() {
    //加载应用目录下的plugins
    //readPluginConfigs这个函数会先调用我们上文提到的getTypeFiles获取到app目录下所有的plugin文件名，然后按照文件顺序进行加载并合并，并规范plugin的数据结构
    const appPlugins = this.readPluginConfigs(path.join(this.options.baseDir, 'config/plugin.default'));

    //加载框架目录下的plugins
    const eggPluginConfigPaths = this.eggPaths.map(eggPath => path.join(eggPath, 'config/plugin.default'));
    const eggPlugins = this.readPluginConfigs(eggPluginConfigPaths);

    //可以通过环境变量EGG_PLUGINS对配置plugins，从环境变量加载plugins
    let customPlugins;
    if (process.env.EGG_PLUGINS) {
      try {
        customPlugins = JSON.parse(process.env.EGG_PLUGINS);
      } catch (e) {
        debug('parse EGG_PLUGINS failed, %s', e);
      }
    }

    //从启动参数options里加载plugins
    //启动参数的plugins和环境变量的plugins都是自定义的plugins，可以对默认的应用和框架plugin进行覆盖
    if (this.options.plugins) {
      customPlugins = Object.assign({}, customPlugins, this.options.plugins);
    }

    this.allPlugins = {};
    this.appPlugins = appPlugins;
    this.customPlugins = customPlugins;
    this.eggPlugins = eggPlugins;

    //按照顺序对plugin进行合并及覆盖
    //_extendPlugins在合并的过程中，对相同name的plugin中的属性进行覆盖，有一个特殊处理的地方，如果某个属性的值是空数组，那么不会覆盖前者
    this._extendPlugins(this.allPlugins, eggPlugins);
    this._extendPlugins(this.allPlugins, appPlugins);
    this._extendPlugins(this.allPlugins, customPlugins);

    const enabledPluginNames = []; // enabled plugins that configured explicitly
    const plugins = {};
    const env = this.serverEnv;
    for (const name in this.allPlugins) {
      const plugin = this.allPlugins[name];
      //plugin的path可能是直接指定的，也有可能指定了一个package的name，然后从node_modules中查找
      //从node_modules中查找的顺序是：{APP_PATH}/node_modules -> {EGG_PATH}/node_modules -> $CWD/node_modules
      plugin.path = this.getPluginPath(plugin, this.options.baseDir);
      //这个函数会读取每个plugin.path路径下的package.json,获取plugin的version，并会使用package.json中的dependencies，optionalDependencies, env变量作覆盖
      this.mergePluginConfig(plugin);
      // 有些plugin只有在某些环境（serverEnv）下才能使用，否则改成enable=false
      if (env && plugin.env.length && !plugin.env.includes(env)) {
        plugin.enable = false;
        continue;
      }
      //获取enable=true的所有pluginnName
      plugins[name] = plugin;
      if (plugin.enable) {
        enabledPluginNames.push(name);
      }
    }

    //这个函数会检查插件的依赖关系，插件的依赖关系在dependencies中定义，最后返回所有需要的插件
    //如果enable=true的插件依赖的插件不在已有的插件中，或者插件的依赖关系存在循环引用，则会抛出异常
    //如果enable=true的依赖插件为enable=false，那么该被依赖的插件会被改为enable=true
    this.orderPlugins = this.getOrderPlugins(plugins, enabledPluginNames, appPlugins);

    //最后我们以对象的方式将enable=true的插件挂载在this对象上
    const enablePlugins = {};
    for (const plugin of this.orderPlugins) {
      enablePlugins[plugin.name] = plugin;
    }
    this.plugins = enablePlugins;
}
```

#### loadConfig函数

配置信息的管理对于一个应用来说非常重要，我们需要对不同的部署环境的配置进行管理，Egg就是针对环境加载不同的配置文件，然后将配置挂载在app上，

加载config的逻辑相对简单，就是按照顺序加载所有loadUnit目录下的config文件内容，进行合并，最后将config信息挂载在this对象上，整个加载函数请看下面源码：

```javascript
//egg-core源码 -> loadConfig函数分析

loadConfig() {
    this.configMeta = {};
    const target = {};
    //这里之所以先加载app相关的config，是因为在加载plugin和framework的config时会使用到app的config
    const appConfig = this._preloadAppConfig();
    
    //config的加载顺序为：plugin config.default -> framework config.default -> app config.default -> plugin config.{env} -> framework config.{env} -> app config.{env}
    for (const filename of this.getTypeFiles('config')) {
    // getLoadUnits函数前面有介绍，获取loadUnit目录集合
      for (const unit of this.getLoadUnits()) {
        const isApp = unit.type === 'app';
        //如果是加载插件和框架下面的config，那么会将appConfig当作参数传入
        //这里appConfig已经加载了一遍了，又重复加载了，不知道处于什么原因，下面会有_loadConfig函数源码分析
        const config = this._loadConfig(unit.path, filename, isApp ? undefined : appConfig, unit.type);
        if (!config) {
          continue;
        }
        //config进行覆盖
        extend(true, target, config);
      }
    }
    this.config = target;
}

_loadConfig(dirpath, filename, extraInject, type) {
    const isPlugin = type === 'plugin';
    const isApp = type === 'app';

    let filepath = this.resolveModule(path.join(dirpath, 'config', filename));
    //如果没有config.default文件，则用config.js文件替代，隐藏逻辑
    if (filename === 'config.default' && !filepath) {
      filepath = this.resolveModule(path.join(dirpath, 'config/config'));
    }
    //loadFile函数我们在EggLoader中讲到过，如果config导出的是一个函数会先执行这个函数，将函数的返回结果导出，函数的参数也就是[this.appInfo extraInject]
    const config = this.loadFile(filepath, this.appInfo, extraInject);
    if (!config) return null;

    //框架使用哪些中间件也是在config里作配置的，后面关于loadMiddleware函数实现中有说明
    //coreMiddleware只能在框架里使用
    if (isPlugin || isApp) {
      assert(!config.coreMiddleware, 'Can not define coreMiddleware in app or plugin');
    }
    //middleware只能在应用里定义
    if (!isApp) {
      assert(!config.middleware, 'Can not define middleware in ' + filepath);
    }
    //这里是为了设置configMeta，表示每个配置项是从哪里来的
    this[SET_CONFIG_META](config, filepath);
    return config;
  }
```

#### loadExtend相关函数

这里的loadExtend是一个笼统的概念，其实是针对koa中的app.response，app.respond，app.context以及app本身进行扩展，同样是根据所有loadUnits下的配置顺序进行加载

下面看一下loadExtend这个函数的实现，一个通用的加载函数

```javascript
//egg-core -> loadExtend函数实现

//name输入是"response"/"respond"/"context"/"app"中的一个，proto是被扩展的对象
loadExtend(name, proto) {
    //获取指定name所有loadUnits下的配置文件路径
    const filepaths = this.getExtendFilePaths(name);
    const isAddUnittest = 'EGG_MOCK_SERVER_ENV' in process.env && this.serverEnv !== 'unittest';
    for (let i = 0, l = filepaths.length; i < l; i++) {
      const filepath = filepaths[i];
      filepaths.push(filepath + `.${this.serverEnv}`);
      if (isAddUnittest) filepaths.push(filepath + '.unittest');
    }

    //这里并没有对属性的直接覆盖，而是对原先的PropertyDescriptor的get和set进行合并
    const mergeRecord = new Map();
    for (let filepath of filepaths) {
      filepath = this.resolveModule(filepath);
      const ext = this.requireFile(filepath);

      const properties = Object.getOwnPropertyNames(ext)
        .concat(Object.getOwnPropertySymbols(ext));
      for (const property of properties) {
        let descriptor = Object.getOwnPropertyDescriptor(ext, property);
        let originalDescriptor = Object.getOwnPropertyDescriptor(proto, property);
        if (!originalDescriptor) {
          const originalProto = originalPrototypes[name];
          if (originalProto) {
            originalDescriptor = Object.getOwnPropertyDescriptor(originalProto, property);
          }
        }
        //如果原始对象上已经存在相关属性的Descriptor，那么对其set和get方法进行合并
        if (originalDescriptor) {
          // don't override descriptor
          descriptor = Object.assign({}, descriptor);
          if (!descriptor.set && originalDescriptor.set) {
            descriptor.set = originalDescriptor.set;
          }
          if (!descriptor.get && originalDescriptor.get) {
            descriptor.get = originalDescriptor.get;
          }
        }
        //否则直接覆盖
        Object.defineProperty(proto, property, descriptor);
        mergeRecord.set(property, filepath);
      }
    }
  }
```

#### loadService函数

##### 如何在egg框架中使用service

loadService函数的实现是所有load函数中最复杂的一个，我们不着急看源码，先看一下service在egg框架中如何使用

```javascript
//egg-core源码 -> 如何在egg框架中使用service

//方式1：app/service/user1.js
//这个是最标准的做法，导出一个class，这个class继承了require('egg').Service，其实也就是我们上文提到的eggCore导出的BaseContextClass
//最终我们在业务逻辑中获取到的是这个class的一个实例，在load的时候是将app.context当作新建实例的参数
//在controller中调用方式：this.ctx.service.user1.find(1)
const Service = require('egg').Service;
class UserService extends Service {
  async find(uid) {
    //此时我们可以通过this.ctx,this.app,this.config,this.service获取到有用的信息，尤其是this.ctx非常重要，每个请求对应一个ctx，我们可以查询到当前请求的所有信息
    const user = await this.ctx.db.query('select * from user where uid = ?', uid);
    return user;
  }
}
module.exports = UserService;

//方式2：app/service/user2.js
//这个做法是我模拟了一个BaseContextClass，当然也就可以实现方法1的目的，但是不推荐
class UserService {
  constructor(ctx) {
    this.ctx = ctx;
    this.app = ctx.app;
    this.config = ctx.app.config;
    this.service = ctx.service;
  }
  async find(uid) {
    const user = await this.ctx.db.query('select * from user where uid = ?', uid);
    return user;
  }
}
module.exports = UserService;

//方式3：app/service/user3.js
//service中也可以export函数，在load的时候会主动调用这个函数，把appInfo参数传入，最终获取到的是函数返回结果
//在controller中调用方式：this.ctx.service.user3.getAppName(1)，这个时候在service中获取不到当前请求的上下文ctx
module.exports = (appInfo) => {
    return {
        async getAppName(uid){
            return appInfo.name;
        }
    }
};

//方式4：app/service/user4.js
//service也可以直接export普通的原生对象，load的时候会将该普通对象返回，同样获取不到当前请求的上下文ctx
//在controller中调用方式：this.ctx.service.user4.getAppName(1)
module.exports = {
    async getAppName(uid){
        return appInfo.name;
    }
};
```

我们上面列举了service下的js文件的四种写法，都是从每次请求的上下文this.ctx获取到service对象，然后就可以使用到每个service文件导出的对象了，这里主要有两个地方需要注意：

1. 为什么我们可以从每个请求的this.ctx上获取到service对象呢：

   看过koa源码的同学知道，this.ctx其实是从app.context继承而来，所以我们只要把service绑定到app.context上，那么当前请求的上下文ctx自然可以拿到service对象，eggLoader也是这样做的

2. 针对上述四种使用场景，具体导出实例是怎么处理的呢？

   - 如果导出的是一个类，EggLoader会主动以ctx对象去初始化这个实例并导出，所以我们就可以直接在该类中使用this.ctx获取当前请求的上下文了
   - 如果导出的是一个函数，那么EggLoader会以app作为参数运行这个函数并将结果导出
   - 如果是一个普通的对象，直接导出

##### FileLoader类的实现分析

在实现loadService函数时，有一个基础类就是FileLoader，它同时也是loadMiddleware，loadController实现的基础，这个类提供一个load函数根据目录结构和文件内容进行解析，返回一个target对象，我们可以根据文件名以及子文件名以及函数名称获取到service里导出的内容，target结构类似这样：

```json
{
    "file1": {
        "file11": {
            "function1": a => a
        }
    },
    "file2": {
        "function2": a => a
    }
}
```

下面我们先看一下fileLoader这个类的实现

```javascript
//egg-core源码 -> FileLoader实现

class FileLoader {
  constructor(options) {
    /*options里几个重要参数的含义:
    1.directory: 需要加载文件的所有目录
    2.target: 最终加载成功后的目标对象
    3.initializer：一个初始化函数，对文件导出内容进行初始化，这个在loadController实现时会用到
    4.inject：如果某个文件的导出对象是一个函数，那么将该值传入函数并执行导出，一般都是this.app
    */
    this.options = Object.assign({}, defaults, options);
  }
  load() {
    //解析directory下的文件，下面有parse函数的部分实现
    const items = this.parse();
    const target = this.options.target;
    //item1 = { properties: [ 'a', 'b', 'c'], exports1 },item2 = { properties: [ 'a', 'b', 'd'], exports2 }
    // => target = {a: {b: {c: exports1, d: exports2}}}
    //根据文件路径名称递归生成一个大的对象target，我们通过target.file1.file2就可以获取到对应的导出内容
    for (const item of items) {
      item.properties.reduce((target, property, index) => {
        let obj;
        const properties = item.properties.slice(0, index + 1).join('.');
        if (index === item.properties.length - 1) {
          obj = item.exports;
          if (obj && !is.primitive(obj)) {
            //这步骤很重要，确定这个target是不是一个exports，有可能只是一个路径而已
            obj[FULLPATH] = item.fullpath;
            obj[EXPORTS] = true;
          }
        } else {
          obj = target[property] || {};
        }
        target[property] = obj;
        return obj;
      }, target);
    }
    return target;
  }
  
  //最终生成[{ properties: [ 'a', 'b', 'c'], exports，fullpath}]形式，properties文件路径名称的数组，exports是导出对象，fullpath是文件的绝对路径
  parse() {
    //文件目录转换为数组
    let directories = this.options.directory;
    if (!Array.isArray(directories)) {
      directories = [ directories ];
    }
    //遍历所有文件路径
    const items = [];
    for (const directory of directories) {
      //每个文件目录下面可能还会有子文件夹，所以globby.sync函数是获取所有文件包括子文件下的文件的路径
      const filepaths = globby.sync(files, { cwd: directory });
      for (const filepath of filepaths) {
        const fullpath = path.join(directory, filepath);
        if (!fs.statSync(fullpath).isFile()) continue;
        //获取文件路径上的以"/"分割的所有文件名，foo/bar.js => [ 'foo', 'bar' ]，这个函数会对propertie同一格式，默认为驼峰
        const properties = getProperties(filepath, this.options);
        //app/service/foo/bar.js => service.foo.bar
        const pathName = directory.split(/[/\\]/).slice(-1) + '.' + properties.join('.');
        //getExports函数获取文件内容，并将结果做一些处理，看下面实现
        const exports = getExports(fullpath, this.options, pathName);
        //如果导出的是class，会设置一些属性，这个属性下文中对于class的特殊处理地方会用到
        if (is.class(exports)) {
          exports.prototype.pathName = pathName;
          exports.prototype.fullPath = fullpath;
        }
        items.push({ fullpath, properties, exports });
      }
    }
    return items;
  }
}

//根据指定路径获取导出对象并作预处理
function getExports(fullpath, { initializer, call, inject }, pathName) {
  let exports = utils.loadFile(fullpath);
  //用initializer函数对exports结果做预处理
  if (initializer) {
    exports = initializer(exports, { path: fullpath, pathName });
  }
  //如果exports是class，generatorFunction，asyncFunction则直接返回    
  if (is.class(exports) || is.generatorFunction(exports) || is.asyncFunction(exports)) {
    return exports;
  }
  //如果导出的是一个普通函数，并且设置了call=true，默认是true，会将inject传入并调用该函数，上文中提到过好几次，就是在这里实现的
  if (call && is.function(exports)) {
    exports = exports(inject);
    if (exports != null) {
      return exports;
    }
  }
  //其它情况直接返回
  return exports;
}
```

##### ContextLoader类的实现分析

上文中说道loadService函数其实最终把service对象挂载在了app.context上，所以为此提供了ContextLoader这个类，继承了FileLoader类，用于将FileLoader解析出来的target挂载在app.context上，下面是其实现：

```javascript
//egg-core -> ContextLoader类的源码实现

class ContextLoader extends FileLoader {
  constructor(options) {
    const target = options.target = {};
    super(options);
    //FileLoader已经讲过inject就是app
    const app = this.options.inject;
    //property就是要挂载的属性，比如"service"
    const property = options.property;
    //将service属性挂载在app.context上
    Object.defineProperty(app.context, property, {
      get() {
        //做缓存，由于不同的请求ctx不一样，这里是针对同一个请求的内容进行缓存
        if (!this[CLASSLOADER]) {
          this[CLASSLOADER] = new Map();
        }
        const classLoader = this[CLASSLOADER];
        //获取导出实例，这里就是上文用例中获取this.ctx.service.file1.fun1的实现，这里的实例就是this.ctx.service,实现逻辑请看下面的getInstance的实现
        let instance = classLoader.get(property);
        if (!instance) {
          //这里传入的this就是为了初始化require('egg').Service实例时当作参数传入
          //this会根据调用者的不同而改变，比如是app.context的实例调用那么就是app.context，如果是app.context子类的实例调用，那么就是其子类的实例
          //就是因为这个this，我们service里继承require('egg').Service，才可以通过this.ctx获取到当前请求的上下文
          instance = getInstance(target, this);
          classLoader.set(property, instance);
        }
        return instance;
      },
    });
  }
}

//values是FileLoader/load函数生成target对象
function getInstance(values, ctx) {
  //上文FileLoader里实现中我们讲过，target对象是一个由路径和exports组装成的一个大对象，这里Class是为了确定其是不是一个exports，有可能是一个路径名
  const Class = values[EXPORTS] ? values : null;
  let instance;
  if (Class) {
    if (is.class(Class)) {
        //这一步很重要，如果是类，就用ctx进行初始化获取实例
      instance = new Class(ctx);
    } else {
      //普通对象直接导出，这里要注意的是如果是exports函数，在FileLoader实现中已经将其执行并转换为了对象
      //function和class分别在子类和父类的处理的原因是，function的处理逻辑loadMiddleware,loadService,loadController公用，而class的处理逻辑loadService使用
      instance = Class;
    }
  } else if (is.primitive(values)) {
    //原生类型直接导出
    instance = values;
  } else {
    //如果目前的target部分是一个路径，那么会新建一个ClassLoader实例，这个ClassLoader中又会递归的调用getInstance
    //这里之所以新建一个类，一是为了做缓存，二是为了在每个节点获取到的都是一个类的实例
    instance = new ClassLoader({ ctx, properties: values });
  }
  return instance;
}
```

##### loadService的实现

有了ContextLoader类，那实现loadService函数就非常容易了，如下：

```javascript
//egg-core -> loadService函数实现源码
//loadService函数调用loadToContext函数
loadService(opt) {
    opt = Object.assign({
      call: true,
      caseStyle: 'lower',
      fieldClass: 'serviceClasses',
      directory: this.getLoadUnits().map(unit => path.join(unit.path, 'app/service')), //所有加载单元目录下的service
    }, opt);
    const servicePaths = opt.directory;
    this.loadToContext(servicePaths, 'service', opt);
}
//loadToContext函数直接新建ContextLoader实例，调用load函数实现加载
loadToContext(directory, property, opt) {
    opt = Object.assign({}, {
      directory,
      property,
      inject: this.app,
    }, opt);
    new ContextLoader(opt).load();
}
```

#### loadMiddleware函数

中间件是koa框架中很重要的一个环节，通过app.use引入中间件，使用洋葱圈模型，所以中间件加载的顺序很重要。

- 如果在上文中的config中配置的中间件，系统会自动用app.use函数使用该中间件
- 所有的中间件我们都可以在app.middleware中通过中间件name获取到，便于在业务中动态使用

```javascript
//egg-core -> loadMiddleware函数实现源码

loadMiddleware(opt) {
    const app = this.app;
    // load middleware to app.middleware
    opt = Object.assign({
      call: false,   //call=false表示如果中间件导出是函数，不会主动调用函数做转换
      override: true,
      caseStyle: 'lower',
      directory: this.getLoadUnits().map(unit => join(unit.path, 'app/middleware')) //所有加载单元目录下的middleware
    }, opt);
    const middlewarePaths = opt.directory;
    //将所有中间件middlewares挂载在app上，这个函数在loadController实现中也用到了，看下文的实现
    this.loadToApp(middlewarePaths, 'middlewares', opt);
    //将app.middlewares中的每个中间件重新绑定在app.middleware上，每个中间件的属性不可配置，不可枚举
    for (const name in app.middlewares) {
      Object.defineProperty(app.middleware, name, {
        get() {
          return app.middlewares[name];
        },
        enumerable: false,
        configurable: false,
      });
    }
    //只有在config中配置了appMiddleware和coreMiddleware才会直接在app.use中使用，其它中间件只是挂载在app上，开发人员可以动态使用
    const middlewareNames = this.config.coreMiddleware.concat(this.config.appMiddleware);
    const middlewaresMap = new Map();
    for (const name of middlewareNames) {
      //如果config中定义middleware在app.middlewares中找不到或者重复定义，都会报错
      if (!app.middlewares[name]) {
        throw new TypeError(`Middleware ${name} not found`);
      }
      if (middlewaresMap.has(name)) {
        throw new TypeError(`Middleware ${name} redefined`);
      }
      middlewaresMap.set(name, true);
      const options = this.config[name] || {};
      let mw = app.middlewares[name];
      //中间件的文件定义必须exports一个普通function，并且接受两个参数：
      //options: 中间件的配置项，框架会将 app.config[${middlewareName}] 传递进来, app: 当前应用 Application 的实例
      //执行exports的函数，生成最终要的中间件
      mw = mw(options, app);
      mw._name = name;
      //包装中间件，最终转换成async function(ctx, next)形式
      mw = wrapMiddleware(mw, options);
      if (mw) {
        app.use(mw);
        this.options.logger.info('[egg:loader] Use middleware: %s', name);
      } else {
        this.options.logger.info('[egg:loader] Disable middleware: %s', name);
      }
    }
}

//通过FileLoader实例加载指定属性的所有文件并导出，然后将该属性挂载在app上
loadToApp(directory, property, opt) {
    const target = this.app[property] = {};
    opt = Object.assign({}, {
      directory,
      target,
      inject: this.app,
    }, opt);
    new FileLoader(opt).load();
}
```

#### loadController函数

controller中生成的函数最终还是在router.js中当作一个中间件使用，所以我们需要将controller中内容转换为中间件形式async function(ctx, next)，其中initializer这个函数就是用来针对不同的情况将controller中的内容转换为中间件的，下面是loadController的实现逻辑：

```javascript
//egg-core源码 -> loadController函数实现源码

loadController(opt) {
    opt = Object.assign({
      caseStyle: 'lower',
      directory: path.join(this.options.baseDir, 'app/controller'),
      //这个配置，上文有提到，是为了对导出对象做预处理的函数
      initializer: (obj, opt) => {
        //如果是普通函数，依然直接调用它生成新的对象
        if (is.function(obj) && !is.generatorFunction(obj) && !is.class(obj) && !is.asyncFunction(obj)) {
          obj = obj(this.app);
        }
        if (is.class(obj)) {
          obj.prototype.pathName = opt.pathName;
          obj.prototype.fullPath = opt.path;
          //如果是一个class，class中的函数转换成async function(ctx, next)中间件形式，并用ctx去初始化该class，所以在controller里我们也可以使用this.ctx.xxx形式
          return wrapClass(obj);
        }
        if (is.object(obj)) {
          //如果是一个Object，会递归的将该Object中每个属性对应的函数转换成async function(ctx, next)中间件形式形式
          return wrapObject(obj, opt.path);
        }
        // support generatorFunction for forward compatbility
        if (is.generatorFunction(obj) || is.asyncFunction(obj)) {
          return wrapObject({ 'module.exports': obj }, opt.path)['module.exports'];
        }
        return obj;
      },
    }, opt);
    //loadController函数同样是通过loadToApp函数将其导出对象挂载在app下，controller里的内容在loadRouter时会将其载入
    const controllerBase = opt.directory;
    this.loadToApp(controllerBase, 'controller', opt);
  },
```

#### loadRouter函数

loadRouter函数特别简单，只是require加载一下app/router目录下的文件而已，而所有的事情都交给了EggCore类上的router属性去实现

而router又是Router类的实例，Router类是基于koa-router实现的

```javascript
//egg-core源码 -> loadRouter函数源码实现

loadRouter() {
    this.loadFile(this.resolveModule(path.join(this.options.baseDir, 'app/router')));
}

//设置router属性的get方法
get router() {
    //缓存设置
    if (this[ROUTER]) {
      return this[ROUTER];
    }
    //新建Router实例，其中Router类是继承koa-router实现的
    const router = this[ROUTER] = new Router({ sensitive: true }, this);
    //在启动前将router中间件载入引用
    this.beforeStart(() => {
      this.use(router.middleware());
    });
    return router;
}
  
//将router上所有的method函数代理到EggCore上，这样我们就可以通过app.get('/async', ...asyncMiddlewares, 'subController.subHome.async1')的方式配置路由
utils.methods.concat([ 'all', 'resources', 'register', 'redirect' ]).forEach(method => {
  EggCore.prototype[method] = function(...args) {
    this.router[method](...args);
    return this;
  };
})  
```

Router类继承了KoaRouter类，并对其的method相关函数做了扩展，解析controller的写法，同时提供了resources方法，为了兼容restAPI的方式

关于restAPI的使用方式和实现源码我们这里就不介绍了，可以看官方文档，有具体的格式要求，下面看一下Router类的部分实现逻辑：

```javascript
//egg-core源码 -> Router类实现源码

class Router extends KoaRouter {
  constructor(opts, app) {
    super(opts);
    this.app = app;
    //对method方法进行扩展
    this.patchRouterMethod();
  }
  
  patchRouterMethod() {
    //为了支持generator函数类型，以及获取controller类中导出的中间件
    methods.concat([ 'all' ]).forEach(method => {
      this[method] = (...args) => {
        //spliteAndResolveRouterParams主要是为了拆分router.js中的路由规则，将其拆分成普通中间件和controller生成的中间件部分，请看下文源码
        const splited = spliteAndResolveRouterParams({ args, app: this.app });
        args = splited.prefix.concat(splited.middlewares);
        return super[method](...args);
      };
    });
  }
  
  //返回router里每个路由规则的前缀和中间件部分
  function spliteAndResolveRouterParams({ args, app }) {
    let prefix;
    let middlewares;
    if (args.length >= 3 && (is.string(args[1]) || is.regExp(args[1]))) {
        // app.get(name, url, [...middleware], controller)的形式
        prefix = args.slice(0, 2);
        middlewares = args.slice(2);
      } else {
        // app.get(url, [...middleware], controller)的形式
        prefix = args.slice(0, 1);
        middlewares = args.slice(1);
      }
      //controller部分肯定是最后一个
      const controller = middlewares.pop();
      //resolveController函数主要是为了处理router.js中关于controller的两种写法：
      //写法1：app.get('/async', ...asyncMiddlewares, 'subController.subHome.async1')
      //写法2：app.get('/async', ...asyncMiddlewares, subController.subHome.async1)
      //最终从app.controller上获取到真正的controller中间件，resolveController具体函数实现就不介绍了
      middlewares.push(resolveController(controller, app));
      return { prefix, middlewares };
  }
```

# 总结

以上便是对EggCore的大部分源码的实现的学习总结，其中关于源码中一些debug代码以及timing运行时间记录的代码都删掉了，关于app的生命周期管理的那部分代码和loadUnits加载逻辑关系不大，所以没有讲到。EggCore的核心在于EggLoader，也就是plugin，config, extend, service, middleware, controller, router的加载函数，而这几个内容加载必须按照顺序进行加载，存在依赖关系，比如：

- 加载middleware时会用到config关于应用中间件的配置
- 加载router时会用到关于controller的配置
- 而config，extend，service，middleware，controller的加载都必须依赖于plugin，通过plugin配置获取插件目录
- service，middleware，controller，router的加载又必须依赖于extend（对app进行扩展），因为如果exports是函数的情况下，会将app作为参数执行函数

# 参考资料

- https://www.npmjs.com/package/egg-cool-router
- 摘抄自：https://cnodejs.org/topic/5bc933129545eaf107b9cc84