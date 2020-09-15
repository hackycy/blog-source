---
title: 解决Element-UI el-tree有关子、父节点选中问题
date: 2020-09-15 17:23:07
tags:
    - Element-UI
categories:
    - Vue
---


# 问题1

在使用el-tree组件在获取完数据进行页面回显数据时，因为后端返回的数据中包含父节点的关系，但是子节点并没有全部选中，就把不该选中的子节点也全部勾上了。

## 解决方案

- isLeaf（判断节点是否为叶子节点）

- getNode（获取tree中对应的节点）

- setChecked （设置tree中对应的节点为选中状态）

```javascript
let res = [1,11,23,25,28,37];
res.map((i, n) => {     
     //根据i获取tree中的节点     
     const node = that.$refs.menuListTree.getNode(i);     
     if (node && node.isLeaf) {          
         //设置某个节点的勾选状态          
         that.$refs.menuListTree.setChecked(node, true);      
     }
 });
```

<!-- more -->

# 问题2

在获取选择的节点数据时，子节点未全部选中时，`getCheckedKeys`中没有包含父节点id。

## **解决方案**

- halfCheckedKeys中为半选中的节点（具体可查看官方API）

```javascript
getCheckedKeys() {  
	const childKeys = this.$refs.menuListTree.getCheckedKeys()
  const halfKeys = this.$refs.menuListTree.getHalfCheckedKeys()
  return [...childKeys, ...halfKeys]
},
```

# 文档

https://element.eleme.cn/#/zh-CN/component/tree