```
Route exact
```

# [React-Route的属性exact](https://www.cnblogs.com/superlizhao/p/9280122.html)

exact是Route下的一个属性，react路由会匹配到所有能匹配到的路由组件，exact能够使得路由的匹配更严格一些。

exact的值为bool型，为true时表示严格匹配，为false时为正常匹配。

例子如下：

```javascript
<Route path='/' component={Home} />
<Route path='/page' component={Page}>
//这种情况下，如果匹配路由path='/page'，那么会把Home也会展示出来。
//既路由path='/page'会匹配路由path='/'和路由path='/page'
```

所以我们常常会添加exact来解决上述问题

```javascript
<Route exact path='/' component={Home} />
<Route path='/page' component={Page} />
//这样匹配路由path='/page'，只会匹配到Page组件
```

**文章来源 ：https://www.cnblogs.com/gwf93/p/11641442.html**





链接: https://pan.baidu.com/s/16__tqwt6rh8yMFmIHXp1Rw 提取码: 5bct