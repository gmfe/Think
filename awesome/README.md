
## 原则

能用简单的思维做就用简单思维做。如果事情复杂了请停下来，可能走错了，发起讨论。肯定有简单的方案的。

## 打印

window.print 后不能跟 window.close 见 [issue](https://github.com/gmfe/Think/issues/31)

### css类

库提供了很多类，尽量用类来组织UI，特别的可以写 style 或者 自己起 less。

如果发现没有相关类且理应抽出来的，发起讨论看怎么处理。

--TODO 补充戴峰

## setTimeout setInterval

能不用 setInterval 就不用，有性能问题。用 setTimeout 来模拟 setInterval。

模拟有两种。

```js
// 1 在 do something 开始的后 1000ms
function fun(){
    setTimeout(fun, 1000);

    // do something
}

// 2 在 do something 结束后的 1000ms
// 注意，需对 do something 做保护，否则后面的 setTimeout 会不运行
function fun2(){
    try{
    // do something
    }catch(err){}

    setTimeout(fun, 1000);
}
```

## parseInt 和 各种转换

记得带上第二个参数

## Date

不使用原生的，使用库 [moment](http://momentjs.com/)

## 运算

原理问题，运算都可能会出现误差，比如 2 - 1.9 得出 0.10000000000000009。使用 [big.js](http://mikemcl.github.io/big.js/) 来规避

'12.10' => '12.1' 可用 parseFloat(numStr, 10)

## react-router

### this.props => withRouter
不建议直接通过 this.props 来获取路由信息，建议使用 withRouter 来提供。更解耦。

### search => query

history@4.x 只提供 search，不提供 query 了。即

```js
// 要自己拼search
history.push({pathname: 'xx', search: '?adsf=xx&adf=2'})

// 取的时候要自己解开
this.props.location.search // ?sdf=1&aaa=y
```

以上方式很不方便。 估在 history withRouter 和 react-router props 上做了加工，使其支持 query。 具体见项目。

```js
history.push({pathname: 'xx', query: {adsf: 'xx', adf: 2})

// 取的时候要自己解开
this.props.location.query.adf
```

## 请求

如果指是 undefined null ，Request 会自动清理 delete

传给后台只有 key value ,其中 value 不能是 Array 和 Object，需要 JSON.stringify(xxx)