## redux 数据流动

正常的开发流程是 UI->派发action=》 通过reducer改变数据返回到store里面=> 从store里面取state数据反映到UI

1. 先写type类型, 
2. 写action 


```javascript

const UPDATE_USERINFO = 'UPDATE_USER_INFO'
//  type payload 所谓的payload实际上就是state, 用现在的新的state去更新劳的state
function updateUserInfoAction(type, payload) {
  return {
    type: 'UPDATE_USERINFO',
    payload,
  }
}
```

3. 写reducer

reducer其实就是一个返回state的函数 接收了两个参数(其中第一个是原来的state， 第二个是action) 然后根据action的type进行state的合并
```javascript
function reducer(state={}, action) {
  switch(action.type) {
    case UPDATE_USERINFO: {
      return  {...state, ...action.payload}
    }
    default: break
  }
}
```
4. 更新数据
把payload传入到reducer中的action中去进行数据的合并

```javascript

dispatch(updateUserInfo(payload)) 

```

```jsx
import React,{Fragment,useReducer} from 'react';


import ReactDOM from 'react-dom';
// reducer,跟redux中reducer一样
const INCREMENT = "INCREMENT";
const DECREMENT = "DECREMENT";
function reducer(state,action){
    switch (action.type) {
        case INCREMENT:
            return {number:state.number+1};
        case DECREMENT:
            return {number:state.number-1};
        default:
            return state;
    }
}
//初始值
let initalArg = 0;
// 返回初始值的函数
function init(initalArg) {
    return {number:initalArg};
}

export default ()=> {
    // state = {number:0}
    const [state,dispatch] = useReducer(reducer,initalArg,init);
    return (
        <Fragment>
            <p>{state.number}</p>
            <button onClick={()=>dispatch({type:INCREMENT})}>+</button>
            <button onClick={()=>dispatch({type:DECREMENT})}>-</button>
        </Fragment>
    )
}
// function render() {
//     ReactDOM.render(<Counter />,document.getElementById('root'));
// }
// render();

```

## redux简单实现版本


```jsx
let memoizedState;

/***
init： 初始化state的回调函数
*/
function useReducer(reducer, initialArg, init) {
  let initState;

  // init  
  if(typeof init != 'undefined') {

    initState = init(initialArg)
  } else {
    initState = initialArg
  }
  // 初始化
  memoizedState = memoizedState || initialState

  function dispatch(action) {
    memoizedState = reducer(memoizedState, action)
    render()
  }
  return [memoizedState, dispatch]
}

```


### ant-design pro 中是怎么处理的
1、 ant-design pro继承redux dva umi -> 数据流动用的是redux saga
2、 使用redux sage的就是为了处理异步的场景

3、umi中的数据全部放在模型中



```javascript
// state
// namespace
// 
// 定义一个reducer的各种action

export function getUserInfo() {
  return fetch(`${url.USER_INFO}`)
}

const dvaModel = {
  namespace: '@@dva',
  state: {
    userList: []
  },
  effects: {
  *effect({payload: 'xx'}, {call, put}) {
    // 暂停一个函数
    yield call(getUserInfo)
    yield put({
      type: 'UPDATE'
      payload:1
    })
  }
  }

  reducers: {
    GET_USERINFO(state, action) {
      return {
        ...state,
        userList: ...action.payload
      }}
  },
};

```

### 视图当中使用

```javascript
export default connect(({ namespace }) => ({

}))(Component)

dispatch({
  type: 'namespace/effect'
})
dispatch({
  type: 'namespace/reducer'
})

```


### async await  react hook






