---
title: Android UI适配指南
date: 2019-05-22 16:30:36
keywords:
    - Android
    - 屏幕适配
tags:
    - Android屏幕适配
categories:
    - Android
---

# 背景

在Android开发中，由于Android碎片化严重，屏幕分辨率千奇百怪，而想要在各种分辨率的设备上显示基本一致的效果，适配成本越来越高。虽然Android官方提供了dp单位来适配，但其在各种奇怪分辨率下表现却不尽如人意。

<!-- more -->

![](fenbutu.png)

**Android屏幕分辨率分布图**

![](afenbutu.png)

**对比IOS屏幕分辨率分布图**

![](ifenbutu.png)

所以Android的屏幕适配已经为重中之重的话题。

# 概念

屏幕尺寸、屏幕分辨率、屏幕像素密度

屏幕尺寸：屏幕对角线长度，单位是英寸，我们常说的多少多少寸，比如4.7存手机、5.7存手机，指的就是这个。

屏幕分辨率：如 1920×1080，是指在手机屏幕的像素点的个数，单位是px，1px = 1 像素点，一般是纵向像素 × 横向像素，意味着高有 1920 个像素点，宽有 1080 个像素点。

屏幕像素密度：是指每英寸上的像素点数，单位是 dpi（dotper inch）。像素密度和屏幕尺寸和屏幕分辨率有关，它是由对角线的像素点数除以屏幕的大小得到的，关系如下：

![](dpicalc.png)

单一变化条件下，屏幕尺寸越小、分辨率越高，像素密度越大，反之越小。

> 与PPI的概念和计算方式是相同的

- dp：是Android 特有的，意为密度无关像素，Google 发布的 BASELINE（基准线）为 160，以此为基准。

- dip：Density Independent Pixels，同dp一个意思，目前废弃了，一般都写dp。

- dpi：像素密度是屏幕上单位面积内的像素数，称为dpi（每英寸的点数）。 它与分辨率不同，后者是屏幕上像素的总数。

- sp：Scale-IndependentPixels的缩写，可以根据文字大小首选项自动进行缩放。Google推荐我们使用12sp以上的大小，通常可以使用12sp，14sp，18sp，22sp，为避免精度损失，建议最好不要使用奇数和小数。

- px：就是我们常说的像素

- density：就这个单词本身直接翻译的意思而言，其也代表“密度”。但需要注意的是，在Android中，其实并非如此。注意我们这里指的是，通过代码`context.getResources().getDisplayMetrics().density`获取的“density”值。而通过该方法获取到的该值，实际上是等价于“dpi / 160”的一个结果值。

# dp直接适配

dp的概念是谷歌官方提出的适配的一种方式。

在android中的dp在渲染前会将dp转为px，计算公式：

- px = density * dp;
- density = dpi / 160;
- px = dp * (dpi / 160);

而dpi是根据屏幕真实的分辨率和尺寸来计算的，每个设备都可能不一样的。

而因为Android碎片化非常严重的原因就导致了dpi的值非常乱，根本没有规律可循，即使dp适配可以做到80%的适配，但是效果还是差强人意。

我们用案例来看一下对比：

![](avd.png)

这里创建了两个个模拟器，同样的分辨率`480 * 800`两种类别的设备，同样的放一张图片，布局代码

``` xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:id="@+id/iv_adapterimg"
        android:src="@mipmap/img"
        android:scaleType="fitXY"
        android:layout_width="300dp"
        android:layout_height="300dp" />

</FrameLayout>
```

![](p480dpshipei.png)

同样的代码，设置为300dp，但是两台机型却表现得不尽人意。这里就要涉及到上面一些公式的概念进行换算了，因为最终都会转换成px，我们来换算一下：

**480*800 5.1寸机型下**

```txt
dpi = √(480^2 * 800^2)/ 5.1 = 182.93
px = 300 * (183 / 160) = 342
```

其余相同计算方式，对照表格：

|         | 480*800/5.1 | 480*800/4 |
| :-----: | :---------: | :-------: |
|   dpi   |   182.93    |  233.24   |
| density |    1.14     |   1.46    |
|   px    |     342     |    438    |

> 上述计算结果均为保留小数点后两位

但是计算的结果真的是这样吗，我们使用代码来获取一下控件的高和宽

``` java
public class MainActivity extends AppCompatActivity {

    private ImageView iv_adapterimg;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        iv_adapterimg = findViewById(R.id.iv_adapterimg);

        iv_adapterimg.getViewTreeObserver().addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() {
            @Override
            public boolean onPreDraw() {
                Log.e("logImageViewInfo", "Height: " + iv_adapterimg.getHeight() + " / Width: " + iv_adapterimg.getWidth());
                return true;
            }
        });

        logDisplayInfo();

    }

    private void logDisplayInfo(){
        String TAG = "logDisplayInfo";

        //通常我们在使用DisplayMetrics时，都是直接获取内部变量来使用。所以下面直接列出各个内部变量。

        DisplayMetrics dm = new DisplayMetrics();
        getWindowManager().getDefaultDisplay().getMetrics(dm);

        Log.e(TAG, "当前设备的系统dpi: " + dm.densityDpi);
        Log.e(TAG, "当前设备的density: " + dm.density);
        Log.e(TAG, "物理屏幕上 Y 轴方向每英寸的像素: " + dm.ydpi);
        Log.e(TAG, "物理屏幕上 X 轴方向每英寸的像素: " + dm.xdpi);
        Log.e(TAG, "屏幕高度的像素数量: " + dm.heightPixels);
        Log.e(TAG, "屏幕宽度的像素数量: " + dm.widthPixels);
    }
}
```

我们查看一下Log输出：

|                  | 480*800/5.1 | 480*800/4 |
| :--------------: | :---------: | :-------: |
| Imageview height |     300     |    450    |
| imageview width  |     300     |    450    |
|     density      |     1.0     |    1.5    |
|       dpi        |     160     |    240    |
|       ydpi       |    160.0    |   240.0   |
|       xdpi       |    160.0    |   240.0   |
|   heightPixels   |     800     |    800    |
|   widthPixels    |     480     |    480    |

那么这为什么和我们计算的不一样呢，这里就要设计到系统dpi和物理dpi了，我们需要深究到其源码。

``` java
//platform_frameworks_base/core/java/android/util/DisplayMetrics.java
package android.util;

import android.annotation.UnsupportedAppUsage;
import android.os.SystemProperties;

/**
 * A structure describing general information about a display, such as its
 * size, density, and font scaling.
 * <p>To access the DisplayMetrics members, initialize an object like this:</p>
 * <pre> DisplayMetrics metrics = new DisplayMetrics();
 * getWindowManager().getDefaultDisplay().getMetrics(metrics);</pre>
 */
public class DisplayMetrics {
 
  //...
  
  /**
     * The device's current density.
     * <p>
     * This value reflects any changes made to the device density. To obtain
     * the device's stable density, use {@link #DENSITY_DEVICE_STABLE}.
     *
     * @hide This value should not be used.
     * @deprecated Use {@link #DENSITY_DEVICE_STABLE} to obtain the stable
     *             device density or {@link #densityDpi} to obtain the current
     *             density for a specific display.
     */
    @Deprecated
    public static int DENSITY_DEVICE = getDeviceDensity();

    /**
     * The device's stable density.
     * <p>
     * This value is constant at run time and may not reflect the current
     * display density. To obtain the current density for a specific display,
     * use {@link #densityDpi}.
     */
    public static final int DENSITY_DEVICE_STABLE = getDeviceDensity();
  
  	private static int getDeviceDensity() {
        // qemu.sf.lcd_density can be used to override ro.sf.lcd_density
        // when running in the emulator, allowing for dynamic configurations.
        // The reason for this is that ro.sf.lcd_density is write-once and is
        // set by the init process when it parses build.prop before anything else.
        return SystemProperties.getInt("qemu.sf.lcd_density",
                SystemProperties.getInt("ro.sf.lcd_density", DENSITY_DEFAULT));
    }
  
  //...
  
}
```

深究其方法是一个native方法，在代码注释中提到的init的方法，深究源头

``` cpp
void DisplayHardware::init(uint32_t dpy)
{
  ///....省略
  
 	/* Read density from build-specific ro.sf.lcd_density property
     * except if it is overridden by qemu.sf.lcd_density.
     */
    if (property_get("qemu.sf.lcd_density", property, NULL) <= 0) {
        if (property_get("ro.sf.lcd_density", property, NULL) <= 0) {
            LOGW("ro.sf.lcd_density not defined, using 160 dpi by default.");
            strcpy(property, "160");
        }
    } else {
        /* for the emulator case, reset the dpi values too */
        mDpiX = mDpiY = atoi(property);
    }
    mDensity = atoi(property) * (1.0f/160.0f);
  
  //....省略
}
```

看其源码可以看出density的值是通过获取`ro.sf.lcd_density`配置的值，如果没有默认使用`DENSITY_DEFAULT`，其默认值有

``` java

    public static final int DENSITY_LOW = 120;

    public static final int DENSITY_MEDIUM = 160;

    public static final int DENSITY_TV = 213;

    public static final int DENSITY_HIGH = 240;

    public static final int DENSITY_260 = 260;

    public static final int DENSITY_280 = 280;

    public static final int DENSITY_300 = 300;

    public static final int DENSITY_XHIGH = 320;

    public static final int DENSITY_340 = 340;

    public static final int DENSITY_360 = 360;

    public static final int DENSITY_400 = 400;

    public static final int DENSITY_420 = 420;

    public static final int DENSITY_440 = 440;

    public static final int DENSITY_XXHIGH = 480;

    public static final int DENSITY_560 = 560;

    public static final int DENSITY_600 = 600;

    public static final int DENSITY_XXXHIGH = 640;

    public static final int DENSITY_DEFAULT = DENSITY_MEDIUM;

    public static final float DENSITY_DEFAULT_SCALE = 1.0f / DENSITY_DEFAULT;
```

那么问题来了，`ro.sf.lcd_density`的值在哪里找到，其配置文件路径在手机的`/system/build.prop`文件中。

可以使用adb命令来将文件进行导出。但是要注意的是，avd模拟器下该文件没有`ro.sf.lcd_density`该配置项。但是可以在`emulator根目录下/config.ini`中的`hw.lcd.density`可以找到配置的值。

4寸模拟器下`config.ini`的`hw.lcd.density`值

``` ini
hw.lcd.density=240
```

我们将4寸的模拟器的配置文件修改成160后查看打印日志:

![](hw_log.png)

可以查看到日志的输出和上面原来的输出发生了改变，改成了自己配置的值。但是该选项只是avd模拟器环境下，真机或者一些游戏模拟器环境下都是在`/system/build.prop`配置文件中`ro.sf.lcd_density`的值。一般该值都是出厂时就编译好的。

``` properties
ro.sf.lcd_density=240
```

> 这是MUMU中读取`/system/build.prop`文件的读取的值，这里没有root的真机，无法演示真机环境，但原理相同。但是可以测试一下真机环境下，DPI是根据配置读取的，而非真实通过物理分辨率求出来的从而验证上述的结论。这里以三星s8手机为例，主屏分辨率2960*1440，尺寸5.8，求出dpi约为3.5，而依靠上述代码输出的值为4.5。

所以dp都是使用系统定义的dpi来进行换算的。而非是说单纯的使用物理分辨率和尺寸来计算的。但依然如此，Android的碎片化还是让dp直接适配还是无法让人满意，尽管dp适配可以解决小部分的适配问题。

# 宽高限定符适配

为了高效的实现UI开发，出现了新的适配方案，我把它称作宽高限定符适配。简单说，就是穷举市面上所有的Android手机的宽高像素值：

![](genvalue.png)

然后我们根据一个基准，为基准的意思就是,比如设计图的尺寸为`480 * 800`的分辨率，有个300*300px的ImageView，则

- 宽度为480，将任何分辨率的宽度分为480份，每一份1px，取值为x1-x480。
- 高度为800，将任何分辨率的高度分为800份，每一份1px，取值为y1-y800。

则对于540 * 860的分辨率来说

![](layxcalc.png)

可以看到x1 = 540 / 基准 = 540 / 480 = 1.12 ;而其他分辨率的计算方式相同。

看一下使用该方式适配的对比结果，同样适用dp适配所使用的布局

``` xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:src="@mipmap/img"
        android:scaleType="fitXY"
        android:layout_width="@dimen/x300"
        android:layout_height="@dimen/y300" />

</FrameLayout>
```

修改了ImageView的宽和高，适配结果为下图

![](valueshipei.png)

再看看不同机型分辨率下的表现

![](valueshipei2.png)

可以看到对比于使用dp方案来适配的结果要完美上许多。通过dimens引用去寻找该分辨率的文件夹下面对应的值。这样基本可以解决我们的适配问题。

那么重点来了，既然可以适配，但为什么很少人使用该方案呢，这就涉及到该方案的一个致命的缺点：那就是需要精准命中才能适配。如果values限定符下的分辨率没有对应上手机，则就只能用默认的values下的dimens文件了。如果使用默认尺寸，而又不同于设计稿的尺寸，就可以会发生UI变形。简单的说容错率太低了。

> 生成的values文件夹下以哪个为基准也需要同样的拷贝一份基准值去默认values文件夹下作为默认值。

**那么如何生成上述所说的文件夹呢，这里使用鸿洋大神给出的一份自动生成代码：**

``` java
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.PrintWriter;

public class GenerateValueFiles {

    private int baseW;
    private int baseH;

    private String dirStr = "./res";

    private final static String WTemplate = "<dimen name=\"x{0}\">{1}px</dimen>\n";
    private final static String HTemplate = "<dimen name=\"y{0}\">{1}px</dimen>\n";

    /**
     * {0}-HEIGHT
     */
    private final static String VALUE_TEMPLATE = "values-{0}x{1}";

  	/**
  	 * 需要适配的分辨率,格式为width,height;
  	*/
    private static final String SUPPORT_DIMESION = "320,480;480,800;480,854;540,960;600,1024;720,1184;720,1196;720,1280;768,1024;800,1280;1080,1812;1080,1920;1440,2560;";

    private String supportStr = SUPPORT_DIMESION;

    public GenerateValueFiles(int baseX, int baseY, String supportStr) {
        this.baseW = baseX;
        this.baseH = baseY;

        if (!this.supportStr.contains(baseX + "," + baseY)) {
            this.supportStr += baseX + "," + baseY + ";";
        }

        this.supportStr += validateInput(supportStr);

        System.out.println(supportStr);

        File dir = new File(dirStr);
        if (!dir.exists()) {
            dir.mkdir();

        }
        System.out.println(dir.getAbsoluteFile());

    }

    /**
     * @param supportStr
     *            w,h_...w,h;
     * @return
     */
    private String validateInput(String supportStr) {
        StringBuffer sb = new StringBuffer();
        String[] vals = supportStr.split("_");
        int w = -1;
        int h = -1;
        String[] wh;
        for (String val : vals) {
            try {
                if (val == null || val.trim().length() == 0)
                    continue;

                wh = val.split(",");
                w = Integer.parseInt(wh[0]);
                h = Integer.parseInt(wh[1]);
            } catch (Exception e) {
                System.out.println("skip invalidate params : w,h = " + val);
                continue;
            }
            sb.append(w + "," + h + ";");
        }

        return sb.toString();
    }

    public void generate() {
        String[] vals = supportStr.split(";");
        for (String val : vals) {
            String[] wh = val.split(",");
            generateXmlFile(Integer.parseInt(wh[0]), Integer.parseInt(wh[1]));
        }

    }

    private void generateXmlFile(int w, int h) {

        StringBuffer sbForWidth = new StringBuffer();
        sbForWidth.append("<?xml version=\"1.0\" encoding=\"utf-8\"?>\n");
        sbForWidth.append("<resources>");
        float cellw = w * 1.0f / baseW;

        System.out.println("width : " + w + "," + baseW + "," + cellw);
        for (int i = 1; i < baseW; i++) {
            sbForWidth.append(WTemplate.replace("{0}", i + "").replace("{1}",
                    change(cellw * i) + ""));
        }
        sbForWidth.append(WTemplate.replace("{0}", baseW + "").replace("{1}",
                w + ""));
        sbForWidth.append("</resources>");

        StringBuffer sbForHeight = new StringBuffer();
        sbForHeight.append("<?xml version=\"1.0\" encoding=\"utf-8\"?>\n");
        sbForHeight.append("<resources>");
        float cellh = h *1.0f/ baseH;
        System.out.println("height : "+ h + "," + baseH + "," + cellh);
        for (int i = 1; i < baseH; i++) {
            sbForHeight.append(HTemplate.replace("{0}", i + "").replace("{1}",
                    change(cellh * i) + ""));
        }
        sbForHeight.append(HTemplate.replace("{0}", baseH + "").replace("{1}",
                h + ""));
        sbForHeight.append("</resources>");

        File fileDir = new File(dirStr + File.separator
                + VALUE_TEMPLATE.replace("{0}", h + "")//
                .replace("{1}", w + ""));
        fileDir.mkdir();

        File layxFile = new File(fileDir.getAbsolutePath(), "lay_x.xml");
        File layyFile = new File(fileDir.getAbsolutePath(), "lay_y.xml");
        try {
            PrintWriter pw = new PrintWriter(new FileOutputStream(layxFile));
            pw.print(sbForWidth.toString());
            pw.close();
            pw = new PrintWriter(new FileOutputStream(layyFile));
            pw.print(sbForHeight.toString());
            pw.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static float change(float a) {
        int temp = (int) (a * 100);
        return temp / 100f;
    }

    public static void main(String[] args) {
				//基准分辨率
        int baseW = 480;
        int baseH = 800;
        String addition = "";
        try {
            if (args.length >= 3) {
                baseW = Integer.parseInt(args[0]);
                baseH = Integer.parseInt(args[1]);
                addition = args[2];
            } else if (args.length >= 2) {
                baseW = Integer.parseInt(args[0]);
                baseH = Integer.parseInt(args[1]);
            } else if (args.length >= 1) {
                addition = args[0];
            }
        } catch (NumberFormatException e) {

            System.err
                    .println("right input params : java -jar xxx.jar width height w,h_w,h_..._w,h;");
            e.printStackTrace();
            System.exit(-1);
        }

        new GenerateValueFiles(baseW, baseH, addition).generate();
    }

}
```

> 对于主流的分辨率我已经集成到了我们的程序中，当然对于特殊的，你可以通过参数指定。关于屏幕分辨率信息，可以通过该网站查询：[http://screensiz.es/phone](http://screensiz.es/phone)

# AndroidAutoLayout库适配

[鸿洋大佬的适配方案](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2FhongyangAndroid%2FAndroidAutoLayout)的项目也来自于宽高限定符方案的启发。虽然该框架已经停止维护，但是许多老项目也在使用该方案。因为集成简单，并且不需要使用dp单位，而是定义好设计稿的尺寸后使用px单位即可完成适配。

**使用方法：**

- Android Studio

``` gradle
dependencies {
    compile 'com.zhy:autolayout:1.4.5'
}
```

- AndroidManifest注册`设计稿`尺寸

``` xml
<meta-data android:name="design_width" android:value="768">
</meta-data>
<meta-data android:name="design_height" android:value="1280">
</meta-data>
```

- 集成`AutoLayoutActivity`

然后就可以在布局文件按照设计稿的尺寸来使用具体的像素值了。比如，设计稿上是96*96,那么我们可以直接写96px，APP运行时，框架会帮助我们根据不同手机的具体尺寸按比例伸缩。这是比宽高限定符更好的方案，因为解决了宽高限定符的容错率问题。

但是框架要在运行时会在onMeasure里面做变换，自定义的控件可能会被影响或限制，可能有些特定的控件，需要单独适配，这里面可能存在的暗坑是不可预见的。因为这是由框架来完成，并非系统完成。并且该库作者已经放弃维护了。

# smallestWidth适配

smallestWidth适配也叫做sw限定符适配。值得是Android会识别屏幕可用宽度和高度的最小尺寸的dp值，然后再根据识别的结果去资源文件中寻找对应的限定符的文件夹下的资源文件。

这种机制上和上文提到的宽高限定符适配原理上是一样的。都是通过系统特定的规则选择对应的文件。

![](genswvalue.png)

例如，比如一台手机的dpi为480，横向分辨率为1080px，根据公式px = dp(dpi/160)，横向的dp值是360dp。则系统就会自动去寻找`value-sw360dp`的文件夹以及对应的资源文件。

> 理论条件下物理dp等于系统dp

而该方案对比与宽高限定符适配方案最大的区别也是优点就是，该方案有更好的容错率。比如上述例子中，如果系统找不到`value-sw350dp`文件夹，则系统会向下寻找，比如找到离一个360最近的`value-sw320dp`文件夹。那么系统就会选择该文件下的资源文件。

例如设计稿同样为`480 * 800`,同样有一个`300 * 300`px的ImageView，例如在values-sw360dp文件夹下的dimen应该如何编写呢？360dp则意味着手机最小宽度为360dp，我们将360dp分成480份，每一个设计稿中的像素大概代表着手机的0.75dp。那么一个`300 * 300`px对应的dimen引用则为

``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<dimen name="base_dpi">360dp</dimen>
  //....
	<dimen name="qb_px_0">0.00dp</dimen>
	<dimen name="qb_px_1">0.75dp</dimen>
	<dimen name="qb_px_300">225.00dp</dimen>
  //...
</resources>
```

而这种dimens引用，在不同的`values-sw<N>`dp文件夹下的数值是不同的，比如values-sw400dp和values-sw420dp

``` xml
//400dp
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<dimen name="base_dpi">400dp</dimen>
	<dimen name="qb_px_0">0.00dp</dimen>
	<dimen name="qb_px_1">0.83dp</dimen>
	<dimen name="qb_px_2">1.67dp</dimen>
	<dimen name="qb_px_3">2.50dp</dimen>
	<dimen name="qb_px_4">3.33dp</dimen>
<resources>
  
//420dp
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<dimen name="base_dpi">420dp</dimen>
	<dimen name="qb_px_0">0.00dp</dimen>
	<dimen name="qb_px_1">0.88dp</dimen>
	<dimen name="qb_px_2">1.75dp</dimen>
	<dimen name="qb_px_3">2.63dp</dimen>
	<dimen name="qb_px_4">3.50dp</dimen>
<resources>
```

计算完后，那么对应的布局文件编写代码：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:id="@+id/iv_adapterimg"
        android:src="@mipmap/img"
        android:scaleType="fitXY"
        android:layout_width="@dimen/qb_px_300"
        android:layout_height="@dimen/qb_px_300" />

</FrameLayout>
```

运行一下来看看适配的效果：

![](swvalueshipei.png)

smallestWidth的适配机制由系统保证，我们只需要针对这套规则生成对应的资源文件即可，不会出现什么难以解决的问题，也根本不会影响我们的业务逻辑代码，而且只要我们生成的资源文件分布合理，，即使对应的smallestWidth值没有找到完全对应的资源文件，它也能向下兼容，寻找最接近的资源文件。

当然该方案也有他的缺点，生成的文件夹越多，也就意味着生成的dimens文件的覆盖范围和尺寸范围越大，apk的安装包也会增加，宽高限定符适配方案也同样有着该缺点。

>smallestWidth适配方案有一个小问题，那就是它是在Android 3.2 以后引入的，Google的本意是用它来适配平板的布局文件（但是实际上显然用于diemns适配的效果更好），不过目前所有的项目应该最低支持版本应该都是4.0了（糗事百科这么老的项目最低都是4.0哦），所以，这问题其实也不重要了。

当然，计算的方式肯定也不会是自己一点计算再编写， 附上生成的代码文件。[代码链接](https://github.com/ladingwu/dimens_sw)

``` java
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.math.BigDecimal;

public class GenerateSWValueFiles {

    /**
     * 适配手机dp列表
     */
    public enum DimenTypes {

        //适配Android 3.2以上   大部分手机的sw值集中在  300-460之间
        DP_sw__300(300),  // values-sw300
        DP_sw__310(310),
        DP_sw__320(320),
        DP_sw__330(330),
        DP_sw__340(340),
        DP_sw__350(350),
        DP_sw__360(360),
        DP_sw__370(370),
        DP_sw__380(380),
        DP_sw__390(390),
        DP_sw__410(410),
        DP_sw__420(420),
        DP_sw__430(430),
        DP_sw__440(440),
        DP_sw__450(450),
        DP_sw__460(460),
        DP_sw__470(470),
        DP_sw__480(480),
        DP_sw__490(490),

        DP_sw__400(400);
        // 想生成多少自己以此类推


        /**
         * 屏幕最小宽度
         */
        private int swWidthDp;


        DimenTypes(int swWidthDp) {

            this.swWidthDp = swWidthDp;
        }

        public int getSwWidthDp() {
            return swWidthDp;
        }

        public void setSwWidthDp(int swWidthDp) {
            this.swWidthDp = swWidthDp;
        }

    }



    /**
     * 生成SW工具类
     */
    public static class MakeUtils {

        private static final String XML_HEADER = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n";
        private static final String XML_RESOURCE_START = "<resources>\r\n";
        private static final String XML_RESOURCE_END = "</resources>\r\n";
        private static final String XML_DIMEN_TEMPLETE = "<dimen name=\"qb_%1$spx_%2$d\">%3$.2fdp</dimen>\r\n";


        private static final String XML_BASE_DPI = "<dimen name=\"base_dpi\">%ddp</dimen>\r\n";
        private  static final int MAX_SIZE = 720;

        /**
         * 生成的文件名
         */
        private static final String XML_NAME = "lay_sw.xml";


        public static float px2dip(float pxValue, int sw,int designWidth) {
            float dpValue =   (pxValue/(float)designWidth) * sw;
            BigDecimal bigDecimal = new BigDecimal(dpValue);
            float finDp = bigDecimal.setScale(2, BigDecimal.ROUND_HALF_UP).floatValue();
            return finDp;
        }


        /**
         * 生成所有的尺寸数据
         *
         * @param type
         * @return
         */
        private static String makeAllDimens(DimenTypes type, int designWidth) {
            float dpValue;
            String temp;
            StringBuilder sb = new StringBuilder();
            try {
                sb.append(XML_HEADER);
                sb.append(XML_RESOURCE_START);
                //备份生成的相关信息
                temp = String.format(XML_BASE_DPI, type.getSwWidthDp());
                sb.append(temp);
                for (int i = 0; i <= MAX_SIZE; i++) {

                    dpValue = px2dip((float) i,type.getSwWidthDp(),designWidth);
                    temp = String.format(XML_DIMEN_TEMPLETE,"", i, dpValue);
                    sb.append(temp);
                }


                sb.append(XML_RESOURCE_END);
            } catch (Exception e) {
                e.printStackTrace();
            }
            return sb.toString();
        }



        /**
         * 生成的目标文件夹
         * 只需传宽进来就行
         *
         * @param type 枚举类型
         * @param buildDir 生成的目标文件夹
         */
        public static void makeAll(int designWidth, DimenTypes type, String buildDir) {
            try {
                //生成规则
                final String folderName;
                if (type.getSwWidthDp() > 0) {
                    //适配Android 3.2+
                    folderName = "values-sw" + type.getSwWidthDp() + "dp";
                }else {
                    return;
                }

                //生成目标目录
                File file = new File(buildDir + File.separator + folderName);
                if (!file.exists()) {
                    file.mkdirs();
                }

                //生成values文件
                FileOutputStream fos = new FileOutputStream(file.getAbsolutePath() + File.separator + XML_NAME);
                fos.write(makeAllDimens(type,designWidth).getBytes());
                fos.flush();
                fos.close();

            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }

    /**
     * 设计稿尺寸(将自己设计师的设计稿的宽度填入)
     */
    private static final int DESIGN_WIDTH = 480;

    /**
     * 设计稿的高度  （将自己设计师的设计稿的高度填入）
     */
    private static final int DESIGN_HEIGHT = 800;


    //generater
    public static void main(String[] args) {
        int smallest = DESIGN_WIDTH > DESIGN_HEIGHT ? DESIGN_HEIGHT : DESIGN_WIDTH;  //     求得最小宽度
        DimenTypes[] values = DimenTypes.values();
        for (DimenTypes value : values) {
            File file = new File("dimens"); //当前项目路径
            MakeUtils.makeAll(smallest, value, file.getAbsolutePath());
        }
    }

}
```

> 主流dp也可以查询相关网站

# 今日头条适配方案

[文章链接](https://link.juejin.im/?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2Fd9QCoBP6kV9VSWvVldVVwA)

该方案的思想来源就是修改density的值，强行把所有不同分辨率的手机的宽度改成一个统一的值。

上文提到dp适配的`DisplayMetrics`中的相关变量：

- DisplayMetrics#density 就是上述的density
- DisplayMetrics#densityDpi 就是上述的dpi
- DisplayMetrics#scaledDensity 字体的缩放因子，正常情况下和density相等，但是调节系统字体大小后会改变这个值

**那么是不是所有的dp和px的转换都是通过 DisplayMetrics 中相关的值来计算的呢？**

首先来看看布局文件中dp的转换，最终都是调用 `TypedValue#applyDimension(int unit, float value, DisplayMetrics metrics) `来进行转换:

``` java
public static float applyDimension(int unit, float value,
                                       DisplayMetrics metrics)
    {
        switch (unit) {
        case COMPLEX_UNIT_PX:
            return value;
        case COMPLEX_UNIT_DIP:
            return value * metrics.density;
        case COMPLEX_UNIT_SP:
            return value * metrics.scaledDensity;
        case COMPLEX_UNIT_PT:
            return value * metrics.xdpi * (1.0f/72);
        case COMPLEX_UNIT_IN:
            return value * metrics.xdpi;
        case COMPLEX_UNIT_MM:
            return value * metrics.xdpi * (1.0f/25.4f);
        }
        return 0;
    } 
```

这里用到的DisplayMetrics正是从Resources中获得的。

再看看图片的decode，`BitmapFactory#decodeResourceStream`方法:

``` java
public static Bitmap decodeResourceStream(@Nullable Resources res, @Nullable TypedValue value,
            @Nullable InputStream is, @Nullable Rect pad, @Nullable Options opts) {
        validate(opts);
        if (opts == null) {
            opts = new Options();
        }

        if (opts.inDensity == 0 && value != null) {
            final int density = value.density;
            if (density == TypedValue.DENSITY_DEFAULT) {
                opts.inDensity = DisplayMetrics.DENSITY_DEFAULT;
            } else if (density != TypedValue.DENSITY_NONE) {
                opts.inDensity = density;
            }
        }
        
        if (opts.inTargetDensity == 0 && res != null) {
            opts.inTargetDensity = res.getDisplayMetrics().densityDpi;
        }
        
        return decodeStream(is, pad, opts);
    }
```

当然还有些其他dp转换的场景，基本都是通过 DisplayMetrics 来计算的，这里不再详述。因此，想要满足上述需求，我们只需要修改 DisplayMetrics 中和 dp 转换相关的变量即可。

通过该原理得到的适配方案：

比如，设计稿的宽度是480px，那么开发代码时会把目标dp值设置为480dp，在不同设备中，动态修改density的值，从而保证手机像素宽度/density这个值始终是360dp。这样来保证UI在不同设备上表现一致。

今日头条屏幕适配方案的核心原理在于，根据以下公式算出 **density**

**当前设备屏幕总宽度（单位为像素）/ 设计图总宽度（单位为 dp) = density**

今日头条方案代码实现：

``` java
import android.app.Activity;
import android.app.Application;
import android.content.ComponentCallbacks;
import android.content.res.Configuration;
import android.support.annotation.NonNull;
import android.util.DisplayMetrics;

public class App extends Application {

    private static float sNoncompatDensity;

    private static float sNoncompatScaledDensity;

    public static void setCustomDensity(@NonNull Activity activity, @NonNull final Application application){
        final DisplayMetrics appDisplayMetrics = application.getResources().getDisplayMetrics();

        if( sNoncompatDensity == 0) {
            sNoncompatDensity = appDisplayMetrics.density;
            sNoncompatScaledDensity = appDisplayMetrics.scaledDensity;
            application.registerComponentCallbacks(new ComponentCallbacks() {
                @Override
                public void onConfigurationChanged(Configuration newConfig) {
                    if(newConfig != null && newConfig.fontScale > 0) {
                        sNoncompatScaledDensity = application.getResources().getDisplayMetrics().scaledDensity;
                    }
                }

                @Override
                public void onLowMemory() {

                }
            });
        }

        final float targetDensity = (float) (appDisplayMetrics.widthPixels / 480.0);
        final int targetDensityDPI = (int) (targetDensity * 160);
        final float targetScaledDensity = targetDensity * (sNoncompatScaledDensity / sNoncompatDensity);

        appDisplayMetrics.density  = targetDensity;
        appDisplayMetrics.densityDpi = targetDensityDPI;
        appDisplayMetrics.scaledDensity = targetScaledDensity;

        final DisplayMetrics activityDisplayMetrics = activity.getResources().getDisplayMetrics();
        activityDisplayMetrics.density  = targetDensity;
        activityDisplayMetrics.densityDpi = targetDensityDPI;
        activityDisplayMetrics.scaledDensity = targetScaledDensity;

    }

}

```

然后在activity#onCreate方法中调用即可，在setContentView之前。运行看看适配的效果：

![](toutiaoshipei.png)

> 以设计图宽480dp去适配的，如果要以高维度适配，可以再扩展下代码即可

## 优点

1. 使用成本非常低，操作非常简单，使用该方案后在页面布局时不需要额外的代码和操作，这点可以说完虐其他屏幕适配方案
2. 侵入性非常低，该方案和项目完全解耦，在项目布局时不会依赖哪怕一行该方案的代码，而且使用的还是 **Android** 官方的 **API**，意味着当你遇到什么问题无法解决，想切换为其他屏幕适配方案时，基本不需要更改之前的代码，整个切换过程几乎在瞬间完成，会少很多麻烦，节约很多时间，试错成本接近于 0
3. 可适配三方库的控件和系统的控件(不止是是 **Activity** 和 **Fragment**，**Dialog**、**Toast** 等所有系统控件都可以适配)，由于修改的 **density** 在整个项目中是全局的，所以只要一次修改，项目中的所有地方都会受益
4. 不会有任何性能的损耗

## 缺点

暂时没发现其他什么很明显的缺点，已知的缺点有一个，那就是第三个优点，它既是这个方案的优点也同样是缺点，但是就这一个缺点也是非常致命的

只需要修改一次 **density**，项目中的所有地方都会自动适配，这个看似解放了双手，减少了很多操作，但是实际上反应了一个缺点，那就是只能一刀切的将整个项目进行适配，但适配范围是不可控的

这样不是很好吗？这样本来是很好的，但是应用到这个方案是就不好了，因为我上面的原理也分析了，这个方案依赖于设计图尺寸，但是项目中的系统控件、三方库控件、等非我们项目自身设计的控件，它们的设计图尺寸并不会和我们项目自身的设计图尺寸一样

当这个适配方案不分类型，将所有控件都强行使用我们项目自身的设计图尺寸进行适配时，这时就会出现问题，**当某个系统控件或三方库控件的设计图尺寸和和我们项目自身的设计图尺寸差距非常大时，这个问题就越严重**

> 这里是JessYan总结的优缺点，个人很赞同。

# AndroidAutoSIze

一个基于今日头条方案的开源库，一个极低成本的 Android 屏幕适配方案.

如果项目没有什么特殊要求，两个步骤即可完成适配：

**添加依赖**

``` gradle
 implementation 'me.jessyan:autosize:1.1.2'
```

**请在 AndroidManifest 中填写全局设计图尺寸 (单位 dp)**

``` xml
<manifest>
    <application>            
        <meta-data
            android:name="design_width_in_dp"
            android:value="360"/>
        <meta-data
            android:name="design_height_in_dp"
            android:value="640"/>           
     </application>           
</manifest>
```

> [Github](https://github.com/JessYanCoding/AndroidAutoSize/blob/master/README-zh.md)，更多详细集成文档建议查看github链接。github中有很详细的用法以及使用的问题。

# 总结

适配就是根据设计图来达到某一个维度上显示一致，不能够说使用适配就可以不使用wrap_content等，比如一个页面时上下滑动的，我们只需要保持设备在宽的维度上保持显示一致即可。而如果一个不支持上下滑动的页面，只需要保持设备在高的维度上保持显示一致。

如何适配，如何选择适配的方案还是要结合自己业务的需求。因为开发就是要追求高效和稳定。

# 参考资料

https://blog.csdn.net/ghost_Programmer/article/details/50042805

https://juejin.im/post/5ae9cc3a5188253dc612842b

https://blog.csdn.net/lmj623565791/article/details/45460089

https://juejin.im/post/5b7a29736fb9a019d53e7ee2

https://mp.weixin.qq.com/s/SDHL26XgIjjlK-RLd_SSCw



