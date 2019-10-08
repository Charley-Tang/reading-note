# React阅读笔记

>  作者：唐才林
>
> 请蘸取[蓝莓酱](https://github.com/KieSun/react-interpretation)配合食用

### hello world - 从何开始阅读？

从你使用的第一个语法糖，如JSX语法

```react
<div className="m-container">Hello React</div>
```

这个会被编译成

```react
React.createElement('div', { className: 'm-container', 'Hello React'})
```

所以我们从`createElement`开始也是比较合适的

### Let us go

`createElement`方法内部的一些关键点

* `ref`和`key`会被特殊处理

* `key`会被自动转成字符串，所以我们传key不需要关心类型，保证唯一就好

```react
if (hasValidKey(config)) {
  key = '' + config.key;
}
```

* `createElement`最终调用`ReactElement`函数，此函数返回一个元素实例，实例有`$$typeof`标志位识别为`React Component`

接下说`class App extends React.Component`的这个Component里的东西

```react
Component.prototype.setState = function(partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
```

可以看到代码是平时调用的`this.setState({ field: value})`，定义的参数叫部分状态和回调，如果`partialState`是个`function`就要返回一个对象，检查过后把状态数据和回调加入到队列中去，这就是`setState`后不能从`this.state`上读取到最新数据的原因

`PureComponent`和`Component`的类定义是一样的，区别在`PureComponent`是`Component`的子类，`PureComponent`的原型上多了一个标志位`isPureReactComponent`

### children相关

`traverseAllChildrenImpl`内部会对props的children进行判断，null和undefined、Boolean值都不会渲染到页面上，因为undefined和Boolean值判定后children被赋值null，所以一下的渲染不必担心会在页面上看到false

```react
<div>
	{ list && list.map(({a, b}) => <p>{a + b}</p>) }
</div>
```

### 调度与diff

`componentWillRecieveProps`不安全，因为使用了`getDeivedStateFromProps`和`getSnapshotBeforeUpdate`后就不触发了，且，调度阶段会打断，所以会重复执行，因此，调度阶段被打断的函数都是不建议使用的

`diff`三步。

第一步。通过组件render返回的Fiber和旧的对比，对索引一直的节点判定key复用（基础类型直接复用） ，不能复用则中断

第二步。看新节点先遍历完还是旧几点遍历完，新的完了，剩余老的删掉；旧的完了的话，对剩余新的创建节点

第三步。所有剩余的老节点放map里，在第二步创建的新节点中，对比map找出可复用的，移动到新节点下，结束后删掉map中的节点。





