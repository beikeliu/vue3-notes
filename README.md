# vue3-notes

### ref
接收一个值，返回该值的响应式对象。通过.value进行读写。
```js
const count = ref(0)
count.value++
console.log(count.value) // 1
```

### reactive
接收一个对象，返回该对象的响应式代理。
```js
const obj = reactive({ count: 0 })
obj.count++
console.log(count.value) // 1
```

### ref与reactive比较
1. ref适用于基本数据类型，reactive适合对象类型。
2. ref需要.value读写，reactive直接.属性读写。
3. 如果在ref中定义对象类型，其实Vue也是会通过转换为reactive进行深层次响应对象的。

### 响应式原理
ES6 Proxy API 拦截get收集依赖，拦截set触发更新渲染。
对比defineProperty： Proxy 返回的是一个新对象,我们可以操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性修改。

### computed
计算属性，接受一个 getter 函数，返回一个只读的响应式 ref 对象。
计算属性值会基于其响应式依赖被缓存。一个计算属性仅会在其响应式依赖更新时才重新计算。
```js
// 示例1：基础用法
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) // 2
// 示例2：需要设置set的用法
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0
```

### watch
侦听属性，侦听一个或多个响应式数据源，并在数据源变化时调用所给的回调函数。  
可以接受三个参数，第一个为要监听的值，第二个为回调函数，第三个是可选参数，为选项配置。  
先说第一个，要侦听的值，可以是一个响应值，或者是一个函数(函数返回一个响应值)，再或者是一个响应值数组。  
```js
// 示例1 监听一个ref
const a = ref(0)
watch(a, (oldValue, newValue) => { })
// 示例2 监听一个reactive中的一个属性 (**这里需要注意的是如果直接监听整个reactive，回调函数中旧值也会变成新值**)
const b = reactive({ a: 0 } )
watch(() => b.a, (oldValue, newValue) => { })
// 示例3 监听多个值
watch([a, () => b.a], ([aOld,aNew],[bOld,bNew]) => { })
// 示例4 如何停止侦听器
const stop = watch(source, callback)
stop() // 停止
```
第二个，回调函数，就是侦听到变化了，要执行的函数，一般我们使用前两个参数，第一个为旧值，第二个为新值，如果侦听的是数组，那么拿到的也是数组参数，参考上面示例3。  
扩展知识：然而其实，还有第三个参数，第三个又是一个回调函数，用来注册副作用清理，可能针对一些异步场景会有用到，我们后面再研究。  
第三个，选项配置，对象结构，支持以下属性：  
1. immediate：在侦听器创建时立即触发回调。第一次调用时旧值是 undefined。
2. deep：如果源是对象，强制深度遍历，以便在深层级变更时触发回调。
3. flush：调整回调函数的刷新时机。参考回调的刷新时机及 watchEffect()。
4. onTrack / onTrigger：调试侦听器的依赖。

### watchEffect
立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行。

### readonly
只读的ref
