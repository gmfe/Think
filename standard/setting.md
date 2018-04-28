导航菜单是一个网站的灵魂，用户依赖导航在各个页面中进行跳转。我们的项目一般为顶部导航和侧边导航结合模式，顶部导航提供全局性的类目和功能，侧边导航提供多级结构来收纳和排列网站架构。


### 为什么

* 统一： 方便迭代和维护   
* 面包屑： framework

### 怎么做

* 三级（四级的时候如何处理）
* navigation
* link
* router

​	因为ma业务比较简单，所以最开始在ma上接入`framework`的面包屑。期间整理了一下ma的navigation和router，向着更合理的方向修改。

- 三级

​在业务上，我们提供三级导航菜单。
​若业务需要四级、五级、、、面包屑的时候，参考ma项目的js/finance/payment/imports:
​
```js
​componentDidMount() {
   actions.app_set_breadcrumbs(['导入']);  // 参数为一个数组
}

componentWillUnmount() {
   actions.app_set_breadcrumbs();
}

```

- ​navigation

刚开始在ma上实验的时候，定的规则是：

​![image](https://user-images.githubusercontent.com/19571458/36825295-8ae4bc90-1d41-11e8-9d47-c327fc860808.png)
​
​但整理station的navigation后，发现，没那么简单，station业务类型比较复杂：

   - 会由于某个`global`属性而隐藏还是显示 某个一级菜单栏 or 二级菜单栏

   - 会判断该账户有没有某个权限而隐藏还是显示 某个一级菜单栏 or 二级菜单栏
   
   - 会根据某个`global`属性而显示特定的菜单栏

   - 并不一定都有`auth`

所以综合上诉情况考虑，定下的navigation配置规则是：

![image](https://user-images.githubusercontent.com/19571458/36831353-653e5d68-1d62-11e8-8eba-c1a1e99af25f.png)

提供`privilege`： 可选字段，用于判断这一整个菜单栏是否显示
提供`auth`: 可选字段，用于判断这一个页面是否显示

对于特殊的`global`判别，用`if~else~`补充判断：

![image](https://user-images.githubusercontent.com/19571458/36830394-6df87d3e-1d5e-11e8-8ac8-ab0a09ffff28.png)

综上，`navigation`提供该用户有权访问的所有菜单栏，一个数组，每个一级菜单为一些object，每个object里面有`link`、`name`、`sub`属性，`sub`是二级菜单，里面也是一些object，每个object里面有`link`、`name`属性。

* link

* router

逻辑是： 点击`运营数据`菜单栏，跳到该一级菜单下最常用的一个页面，如果没有权限则，则顺着找下去。
用ma的`运营数据`为例子：

![image](https://user-images.githubusercontent.com/19571458/36830755-f20a60e6-1d5f-11e8-9427-2905f2eba78c.png)

其中核心代码是`renderTo`：

![image](https://user-images.githubusercontent.com/19571458/36830793-162e1094-1d60-11e8-8c63-916ce92605b0.png)



​	



### 解决了什么问题
待续。。。。。



