# Easydog

Easydog 是一个前端状态管理工具，简化开发体验。适合小程序等项目等的开发。

[查看demo](http://tks-model.pages.oa.com/)

## 快速上手

### 创建一个 module

```js
const app = {
  namespace: 'app',
  state: {
    past: [],
    current: 0,
    future: []
  },
  actions: {}
}
```

添加 actions

```js
const app = {
  namespace: 'app',
  state: {
    past: [],
    current: 0,
    future: []
  },
  actions: {
    change ({ setState }, val) {
      const newPast = [...this.state.past, this.state.current]
      setState({
        past: newPast,
        current: val
      })
    },
    undo ({ setState }) {
      const { past, future, current } = this.state
      if (past.length === 0) { return }
      
      const previous = past.pop()
      const newFuture = [current, ...future]
      setState({
        past: [...past],
        current: previous,
        future: newFuture
      })
    },
    redo ({ setState }) {
      const { past, future, current } = this.state
      if (future.length === 0) {
        return
      }
      const next = future.shift()
      const newPast = [...past, current]
      setState({
        past: newPast,
        current: next,
        future: [...future]
      })
    }
  }
}
```

### 创建 Store

```js
const store = createStore({ modules: app })
```

### 获取对状态读取和修改的方法

```js
const states = store.mapStates({
  val: 'app/current'
})

const actions = store.mapActions({
  change: 'app/change',
  add (dispatch) {
    dispatch('app/change', states.val + 1)
    render('app/change')
  },
  reduce (dispatch) {
    dispatch('app/change', states.val - 1)
    render('app/change')
  },
  undo (dispatch) {
    dispatch('app/undo')
    render('app/undo')
  },
  redo (dispatch) {
    dispatch('app/redo')
    render('app/redo')
  }
})
```

### 调用方法

```js
actions.add()
```
