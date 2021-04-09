# react hooks

### chap1

```js
const [ count,setCount ]=useState(0)
//其中useState()中的参数可以是一个函数
//setCount通常也会传入一个函数
```

### chap2

```javascript
useEffect(()=>{
},[deps])
//1. useEffect的内容开始会执行一次，其中这个deps参数如果发生变化的话，useEffect会再次执行，否则不会再执行。可以传入一个函数控制调用的次数
//2. 挂载时执行函数，卸载时执行return函数
//3. useEffect其本身是通过===来判定元素是否相同的
```

###### 补充：from 毅超

> **第二个参数**不同的，结果不同：
>
> - 数组中有值
>
> - - 该值变化时，会触发回调函数
>
> - 空数组
>
> - - 只在**初始化**和**销毁时**才使用。即DidMount和WillUnmount中
>   - 单独的willunmount可以放到return的一个函数中
>
> - 不传值
>
> - - 在DidMount和DidUpdate中都会执行。
>   - 区分可以靠useRef来区分



### chap3

```javascript
//通过将dependences中的改为props的某个值就可以实现对于props那个值的监听了
//deps使用的是浅比较
```

### chap4

```javascript
//ref的用法
forwardRef() 和 useImperativeHandle()
/*
1. 同时使用
2. useImperativeHandle()用于对子组件进行封装，使之暴露一个接口给父组件，用于调用自组件ref的方法，避免直接拿到自组件的ref，不符合面向对象的封装性，也就是我们所说的ref透传。
3. useImperativeHandle()接受两个参数，其中一个参数是ref,代表父组件传进来的参数,另一个参数是一个纯函数，用作暴露给current的函数接口。
*/
```

##### 补充：

###### useRef的常见用法

- 在同一个组件调用获得某一个dom标签

- 跨组件获取子组件方法（fastforward和useImperativeHandle）

- 跨周期保存数据

- 判断DidMount和DidUpdate逻辑

  - ```javascript
    const flag=useRef(flase);
    if(flag.current){
        //---DidUpdate----
    }
    else{
        //---DidMount-----
        flag.current=true
    }
    
    ```

    

### chap5 Advance

```javascript
useEffect()和useLayoutEffect()
/*
1. hooks是根据hook的顺序来确定每次的值的变化；一旦改变了就会发生不可预期的错误。
   所以不要用if语句改变了hook的顺序
2. 后者优先级更高，只要dom发生改变了就一定会先调用。平常情况下用react内置的管理，不要使用这个函数，只有在effect无法更新dom的情况下才需要使用。
3. useMemo和useCallback，前者用于值的缓存，后者用于函数引用的缓存
*/
```

### chap6 Advance

```javascript
//useMemo和useCallback
const expensive=useMemo(()=>{
    //---expensive calculation---
    return num;
},[count])
const callback=useCallBack(()=>{
   //...
},[count])
<Parent>
    <Child callback={callback} />
<Parents>    
/*
1. useMemo和useCallback都会在组件第一次渲染的时候执行，之后会在其依赖的变量发生改变时再次执行；并且这两个hooks都返回缓存的值，useMemo返回缓存的变量，useCallback返回缓存的函数。
2. React.memo仅检查props变更，而且只做浅比较，所以props不能使用引用类型的数据，只能使用基本数据类型的数据。
3. useMemo用于缓存数据，当一些复杂的计算数据只和某个state/prop想关联时，就可以将计算结果缓存下来，只有当相应的state/prop改变了的时候才进行复杂的计算。
4. useCallback用于当父组件更新时，将callback函数作为属性传给子组件，可以避免不必要的子组件渲染。
*/

```



### useReducer

```javascript
const [items,dispatch]=useReducer((state,action)=>{
   switch(action.type){
    case 'add':return [state...,{name:'',value:''}] ;
    case 'remove':return state.filter((_,item)=>item!=action.index)
   }
})
<button onclick={dispatch({'type':'add'})}>submit<button>
```





#### 深入 React Fiber 架构和协调算法

1.  **render**: 首先利用react.createElement创建出所有的React元素（可以看到 React 将属性 `$$typeof` 添加到这些对象中，作为 React 元素的唯一标识。而且有 `type` 、 `key` 、和 `props` 这些属性对 React 元素进行描述。）

2.  **reconciliation**: 每个React Element都要转化为Fiber节点，它的数据都会被合并到Fiber节点树中，`fiber` 节点使用这些属性： `child` 、 `sibling` 和 `return` 通过链接列表的形式连接在一起。每个 fiber 节点都在 **alternate** 字段中保留了在另一棵树上其对应节点的引用。 `current` 树中的一个节点指向 `workInProgress` 树中的节点，反之亦然。

   ![img](D:\前端面试准备\fibernode格式.png)

3. 