# ref

- [Options API vs Composition API](https://vueschool.io/articles/author/charles-allotey)



# options API

- it can become cumbersome to manage a large number of options as the complexity of component increases.  ---- **"option explosion"**
- when it comes to sharing logic between components.  If you want to reuse a piece of logic across multiple components, you need to either copy and paste the code or abstract it into mixin.  ----**Reusability**
- 





# composite API

- programming style
  - functional, reactive programming，函数式、响应式编程
  - more flexible and expressive way of define component behavior
  - interactive
- 

```vue
<script setup>
import {ref, reactive, onMounted} from 'vue'

const profile = reactive({name:'', age:''})
const aboveAge = ref(false)

const verifyUser = ()=>age.value < 18? avoveAge = false: aboveAge = true

const displayProfile = computed(()=>{
  return `My name is ${this.name} and i am ${this.age}`;
})

onMounted (()=>console.log('Application mounted'))
</script>
```



## more flexible and expressive

```js
// mouse.js
import {ref, onMounted, onUnmounted} from 'vue'
export function useMouse(){
  // state encapsulated and managed by the composable
  const x = ref(0)
  const y = ref(0)
  
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }
  
  onMounted(()=>window.addEventListener('mousemove', update))
  onUnmounted(()=>window.removeEventListener('mousemove', update))
  
  // expose managed state as return value
  return {x, y}
}
```

```vue
// usage in component
<script setup>
import {useMouse} from './mouse.js'
const {x,y} = useMouse()
</script>
// this makes it easier to share logic between components and manage code dependencies as the size of a project grows. It also makess it easier to test and debug code, as the reactive declarative compositions are isolated and easier to reason about.
```



## Usage

allows developers to use the full power of JavaScript to define component behavior. This makes it possible to use advanced features such as async/await, Promises, and even third-part libraries like RxJS to build components. This can make it easier to build complex, interactive components.



## Bundle Size

Code written in Composition API is also more efficient and minifialbe than code written in Options API.

- This is because the template in a component is built as a function inlined in the same scope as the code. 
- Unlike property access from this, the produced template code may directly access variables specified inside without the need for an instance proxy. This also improves magnification because all variable names may be securely reduced.











