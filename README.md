> New Idea, New the World. 🔥🔥🔥

<pre>
技术也是时尚驱动的，我们常常臣服于时尚,面对快速的变化常常让我们局促不安,
开始焦虑，唯恐错过了些什么,怎么打破这种焦虑？
需要在快速变化得世界里保持清醒，保持独立的思考和认知。
让我们回归到技术的本质, 因为解决了真实的问题，技术才变得有价值。
<strong>真正牛\*的技术，都是静悄悄的跑在线上...</strong>
</pre>

### plume2 🚀🚀🚀

light weight framework React for mobile web

[![NPM](https://nodei.co/npm/plume2.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/plume2)

Reactive and Predictable state container for React or ReactNative.

* Thanks Immutable.js
* Thanks MapReduce
* Thanks Functional Reactive Programming.

### iflux

很早很早之前，我们爱上了 React and immutable，所以就有了很简单的 iflux。

_[iflux](https://github.com/QianmiOpen/iflux) = immutable.js + react.js_

[![NPM](https://nodei.co/npm/iflux.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/iflux)

保持简单

```
+-----------------------+
|       WebApi          |
+-----------------------+
          |  
         \|/
+-----------------------+
|   Store（immutable）   |<-----+
+-----------------------+      |
           | //es5 style       |
           | StoreMixin        | msg(EventEmitter)
          \|/                  |
+------------------------+     |
|     React App          |-----|
+------------------------+
|      <Layout>          |
|        <SearchForm/>   |
|        <Toolbar/>      |
|        <DataGrid/>     |
|       </Layout>        |
+------------------------+
```

优点：

* 简单直接，几乎没有什么规则
* 单根数据源(single data source)
* Immutable fronted UI
* High Performance

但是随着业务的不断的发展，数据层的复杂度也在上升。这时候 iflux 就会暴露出很多的缺点

* Big Store, Store 中大量的数据和业务的处理，代码膨胀的厉害
* Store 是单例，不销毁，有共享的问题
* store 的数据通过 props 不断的透传，代码写的很费劲
* 大量的数据之间的依赖关系，需要手动的保证和处理

### 怎么解决?

* 使用 MapReduce 的理念解决 big Store
* 使用@Relax 自动注入 store 中的数据和事件
* Store 不再是单例
* 使用 FRP 的理念, 简单的实现反应式数据，抽象源数据和派生数据

这就是我们的 plume2

```text
+------------------+
|     BFF-API      |
+------------------+
        ||
        \/
+------------------+
|     WebApi       |
+------------------+
        ||
        \/
+------------------+
|     Store        | ====> [Actor1, Actor2, Actor3]
+------------------+
        ||
        \/
+------------------+
|  @StoreProvider  |
+------------------+
        ||
        \/
+------------------+
|     @Relax       |
+------------------+
        ||
        \/
+------------------+
|     QL/DQL       |
+------------------+
```

# Getting Started

```sh
# add dependencies
yarn add plume2 # npm install plume2

# web
yarn add react react-dom # yarn add preact preact-compat
```

# quick demo

```js
//四大领域对象
//Actor, Store, StoreProvider, Relax

class HelloActor extends Actor {
  defaultState() {
    return { text: 'hello world' };
  }
}

class AppStore extends Store {
  bindActor() {
    return [new HelloActor()];
  }
}

@Relax
class Text extends React.Component {
  static relaxProps = {
    text: 'text'
  };

  render() {
    const { text } = this.props.relaxProps;
    return <div>{text}</div>;
  }
}

@StoreProvider(AppStore)
class HelloApp extends React.Component {
  render() {
    return <Text />;
  }
}

ReactDOM.render(<HelloApp />, document.getElementById('app'));
```

## contributes

**thanks all(pr, issue)**

```text
plume2 git/master  
❯ git summary

 project  : plume2
 repo age : 8 months
 active   : 42 days
 commits  : 164
 files    : 191
 authors  :
   149	hufeng  90.9%
    14	胡锋    8.5%
     1	blsery  0.6%
```

## document

[plume2](https://hufeng.github.io/plume2/)

FAQ

1. TypeError: Class constructor Store can not be invoked without 'new'?

![err](https://raw.githubusercontent.com/hufeng/plume2/master/docs/screenshot/err.png)

默认我们的 plume2 发布的模块级别是 es6，为了让我们调试方便，没有编译到 es5 的 level。这样在和 webpack 的配合的时候，webpack 一般在配置 babel-loader 的时候，会忽略 node_modules
这样会导致我们业务代码编译级别是 es5,plume2 是 es6，就会报这个错误。

解决，我们现在发布也是带了 es5 的模块，只不过默认的仍然是 es6.你可以直接用

```js
import {Store ...}  from 'plume2/es5'
```

当然这并不是最好的解决方案

还是通过 babel 和 webpack 配置来解决问题比较好,比如：

```javascript
{
  test: /\.js$/,
  include: [
    path.resolve(__dirname, 'node_modules/plume2'),
  ],
  loader: 'babel-loader'
}
```

2. ReactNative can not find react-dom module

这是什么原因呢？因为我们的 store 模块依赖了 react-dom，在 react-native 的环境是没有 react-dom 这个模块，所以请使用我们的一个自定义的 babel 插件来解决问题。

```sh
yarn add babel-plugin-transform-rn-react-dom
```

```js
//.babelrc
{
  "plugins": [
    "transform-rn-react-dom"
  ]
}
```
