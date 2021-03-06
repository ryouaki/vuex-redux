# vue-with-redux
[En version](readme.md)<br/>
这是一个用于帮助Vue使用Redux管理状态的插件。Redux是一个非常流行的状态管理工具。vue-with-redux为大家提供一个可以在Vue环境下使用Redux的途径。这回带来不同的开发体验。

# 开始

首先你需要通过如下命令安装vue-with-redux

```shell
  npm install -save vue-with-redux
```

# 运行Demo

```shell
  git clone https://github.com/ryouaki/vue-with-redux.git
  npm install
  npm run serve
```

# Usage

需要像下面这样改造你的入口文件：
```js
  // 有可能是你的entry.js文件
  ... // 这里是你引入的其它包
  import VuexRedux from 'vue-with-redux';
  import { makeReduxStore } from 'vue-with-redux';
  import reduces from 'YOUR-REDUCERS';
  import middlewares from 'REDUX-MIDDLEWARES';

  Vue.use(VuexRedux);

  let store = makeReduxStore(reduces, [middlewares]);

  new Vue({
    store,
    render: h => h(App)
  }).$mount('#app')
```

下面是一个actionCreate函数:
```js
  export function test() {
    return {
      type: 'TEST'
    }
  }

  export function asyncTest() {
    return (dispatch, getState) => {
      setTimeout( () => {
        console.log('New:', getState());
        dispatch({type: 'TEST'});
        console.log('Old', getState());
      }, 100);
    }
  }
```

_**Note**: 你并不需要使用redux-thunk，因为vue-with-redux已经提供了对异步处理的支持._

这是一个reducer的例子: 
```js
  function reduce (state = { count: 0 }, action) {
    switch(action.type) {
      case 'TEST':
        state.count++;
        return state;
      default:
        return state;
    }
  }

  export default {
    reduce
  };
```

Vue的组件例子:
```js
  <template>
    <div>
      <button @click="clickHandler1">Action Object</button>
      <button @click="clickHandler2">Sync Action</button>
      <button @click="clickHandler3">Async Action</button>
      <p>{{reduce.count}}</p>
    </div>
  </template>

  <script>
  import { test, asyncTest } from './../actions';

  export default {
    name: 'HelloWorld',
    props: {
      msg: String
    },
    // 你必须在这里预先定义你订阅的Redux中的状态。否则编译模版会报错。
    data() {
      return {
        reduce: {}
      }
    },
    methods: {
      clickHandler1() {
        this.dispatch({type: 'TEST'});
      },
      clickHandler2() {
        this.dispatch(test());
      },
      clickHandler3() {
        this.dispatch(asyncTest());
      },
      // 你必须实现一个mapReduxState函数，用于告诉vue-with-redux你需要订阅哪些redux中的状态
      //  [ state ] 参数就是redux状态树的根。
      mapReduxState(state) { 
        return {
          reduce: state.reduce
        }
      },
    }
  }
  </script>
```