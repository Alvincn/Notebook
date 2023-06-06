# 创建vite项目

创建vite项目：`npm init vite@latest`

创建 vue 项目：`npm init vue@latest`

# setup

setup 就是一个函数

组件中所有的数据、方法、生命周期钩子之类的都要配置在 setup 中，

需要有返回值，

- 返回是一个对象，包含数据、方法、生命周期等。常用
- 返回一个渲染函数

当然，也可以在 script 中配置 setup，他在 beforeCreate 之前调用

接收两个参数：

- props：接收到父组件传的数据，可以使用props接收，并且其是响应式的

- context：上下文对象

  ```js
  {
      "attrs": Proxy(Object),
      "emit": (event, ...args) => instance.emit(event, ...args),
      "expose": (exposed) => {…},
      "slots": Proxy(Object)
  }
  ```

  对应的：

  - attrs：值为对象，组件外部传递过来的，但是没有在 props 中声明的属性，相当于 this.$attrs
  - slots：收到的插槽内容，相当于 this.$slots
  - emit： 分发自定义事件的函数，相当于 this.$emit

# 响应式

## ref 

ref：把一个变量变成一个响应式数据，支持所有的类型，

基本数据类型使用的是 `Object.defineProperty`中的 get 和 set 完成的

对象类型的数据：内部使用了 `reactive`函数，使用  proxy 代理

其中包括：

- `ref`：将变量变成响应式数据：

  ```js
  import { ref } from 'vue';
  let str = ref('chenshibo');
  str.value = '123';
  ```

  获取 DOM 元素：

  ```js
  <template>
  	<div ref="container"></div>
  </template>
  <script setup lang="ts">
      import { ref, onMounted } from 'vue'
      const container = ref() // 注意：此处的变量名必须和标签上的属性名一致
      console.log(container.value)
  </script>
  ```

- `shallowRef`

  用于创建一个“浅层”的响应式对象

  如果传入的是基本数据类型，那么和 ref 没有区别

  如果传入的是对象类型，那就没用了，不处理对象类型的响应式

- `triggerRef`

  强制更新 DOM。

  在我们需要手动更新 count 的位置使用，使用 triggerRef 来触发视图更新。

- `customRef`：

  在 Vue 3 中，提供了一个 customRef 函数，用于创建一个自定义的、可响应的引用对象。与 ref 和 shallowRef 不同的是，customRef 可以自定义 get 和 set 方法的实现逻辑，从而实现更加灵活的响应式行为。

  使用 customRef 函数创建的引用对象与 ref 对象类似，也具有 value 属性，当读取这个属性时，会触发 get 方法的执行；当修改这个属性时，会触发 set 方法的执行，并且会触发相应的依赖更新。与 ref 对象不同的是，customRef 函数本身并不会对传入的初始值进行处理，而是将其直接作为 get 方法的返回值，需要自己手动处理。

  ```js
  import { customRef } from 'vue'
  
  const myRef = customRef((track, trigger) => ({
    value: 0,
    get() {
      track()
      return this.value
    },
    set(newValue) {
      this.value = newValue
      trigger()
    }
  }))
  
  console.log(myRef.value) // 输出 0
  
  myRef.value = 1
  console.log(myRef.value) // 输出 1
  
  ```

  可以在 set 中写一些方法，比如写一个防抖，这样就可以在每次点击时调用。

- `isRef`：判断一个属性是否是 ref 对象，通过判断这个对象是否拥有一个特殊属性 IS_REF 来判断，如果这个属性是 true，那么就说明这个对象是一个 ref 对象

## reactive

只支持引用类型，Array，Object，Map，Set，

ref 取值和赋值 都需要添加 `.value`，但是 reactive 不需要

reactive 不能直接赋值，会破坏其响应式对象，如果在网络请求中，可以使用`list.push(...res)`

- `reactive`：响应式对象属性，只支持引用类型
- `readonly`：把一个数据变成只读的   
- `shallowReactive`：浅层响应，只考虑第一层数据的响应式，如果`obj.job.salary`，那么将不会对这个进行监听

## 变成响应式

- `toRef`：创建一个 ref 对象，使其 value 值指向另一个对象的属性

  例子：

  ```js
  setup() {
      let person = reactive({
          name:'张三',
          age:15,
          job: {
              salary: 20
          }
  	})
      return {
          person
      }
  }
  ```

  如果是这样的话，在每次使用person中的数据时，都需要使用：`{{ person.name }} {{ person.age }} {{   person.job.salary }}`，这样就很费劲，但是如果这样：

  ```js
  setup() {
      let person = reactive({
          name:'张三',
          age:15,
          job: {
              salary: 20
          }
  	})
      return {
          name: person.name,
          age: person.age,
          salary: person.job.salary
      }
  }
  ```

  这样传递的只是一个普通数据，无法做到响应式，这时我们就可以使用这个

  ```js
  setup() {
      let person = reactive({
          name:'张三',
          age:15,
          job: {
              salary: 20
          }
  	})
      return {
          name: toRef(person, 'name'),
          age: toRef(person, 'age'),
          salary: toRef(person.job, 'salary')
      }
  }
  ```

  这样即可完成

- `toRefs`：可以把一个对象中所有属性都变成响应式的

  ```js
  setup() {
      let person = reactive({
          name:'张三',
          age:15,
          job: {
              salary: 20
          }
  	})
      return {
          person,
          ...toRefs(x)
      }
  }
  ```

- `toRaw`：把一个由 reactive 生成响应式对象转换成原始对象（ref 不可以）

- `markRaw`：把一个对象标记为永远不要成为响应式对象（提高一定效率）

## 响应式原理

### vue2

在 Vue2 中，对象：

- 增加或者删除一个属性，无法检测
- 根据下边更改，无法检测

在 Vue2 中，想要为一个对象添加一个属性，添加后页面是没有改变的，因为 Vue2 中无法监测到 对象中属性的改变，我们可以这样为其添加：`this.$set(this.person,'sex','男')`

如果我们要删除一个属性，也是无法监听到的，其只能监听到 读取(getter) 以及 设置(setter)，这里我们这样写：`this.$delete(this.person,'name')`

也没法监听到更新，但是可以这样写`this.$set(this.person.hobby[0],'学习')`或者`this.person.hobby.splice(0,1,'学习')`

 响应式原理代码：

```js
let person = {
    name: '张三',
    age: 18
}
let p = {}
Object.defineProperty(p, 'name', {
    configurable: true,
    // 有人读取name时调用
    get() {
        return person.name
    },
    // 有人修改name时调用
    set(val) {
        console.log('有人修改了数据，我要去更新界面');
        person.name = val
    }
})
Object.defineProperty(p, 'age', {
    configurable: true,
    // 有人读取name时调用
    get() {
        return person.age
    },
    // 有人修改name时调用
    set(val) {
        console.log('有人修改了数据，我要去更新界面');
        person.age = val
    }
})
```

### vue3

使用 Proxy 代理对象，Reflect 反射对象

```js
let p = new Proxy(person, {
    // 读取调用
    get(target, key) {
        // target对应的就是这个person对象 key就是对应的属性
        // {name: '张三', age: 18} name
        console.log(target, key);
        return Reflect.get(target, key)
    },
    // 修改属性、追加属性
    set(target, key, value) {
        // target对应的就是这个person对象 key就是对应的属性 value就是要改变的值
        console.log('数据更新了，视图要去改变了');
        Reflect.set(target, key, value)
    },
    // 删除属性
    deleteProperty(target, key) {
        // target对应的就是这个person对象 key就是对应的属性
        console.log('数据删除了，视图要去改变了');
        delete Reflect.deleteProperty(target, key)
    },
})
```

## 计算属性

在 Vue3 中，computed 也是一个函数，并且返回计算后的数据：

```js
setup {
    let person = {
        firstName:'chen',
        lastName:'shibo'
    }
    person.fullName = computed(()=>{
        return person.firstName + '-' + person.lastName
    })
    return {
        person
    }
}
```

这样的话计算出来的数据是只读的

```js
setup {
    let person = {
        firstName:'chen',
        lastName:'shibo'
    }
    person.fullName = computed(()=>{
        get() {
            return person.firstName + '-' + person.lastName
        }
        set() {
            let nameArr = person.fullName.split('-')
            person.firstName = nameArr[0]
            person.lastName = nameArr[1]
        }
    })
    return {
        person
    }
}
```

## watch

```js
setup() {
    let sum = ref(0)
    let count = ref(1)
    // 调用 watch 函数，也可以写成数组
    watch(sum, (newValue, oldValue) => {
        console.log('sum改变了', newValue, oldValue )
    })
    watch(count, (newValue, oldValue) => {
        console.log('sum改变了', newValue, oldValue )
    })
}
```

写成数组：

```js
setup() {
    let sum = ref(0)
    let count = ref(1)
    // 调用 watch 函数，也可以写成数组
    watch([sum, count], (newValue, oldValue) => {
        console.log('sum改变了', newValue, oldValue )
    })
}
```

加上配置项：

```js
setup() {
    let sum = ref(0)
    let count = ref(1)
    // 调用 watch 函数，也可以写成数组
    watch(sum, (newValue, oldValue) => {
        console.log('sum改变了', newValue, oldValue )
    },{immediate:true})
}
```

有点问题：

- 监听 reactive 所定义的响应式数据，那么将无法拿到 oldValue 了，oldValue 和 newValue一样

- 不管嵌套的层次有多深，就算不开启 deep深度监视，也会监听到深层的改变（强制开启深度监视，无法关闭，deep配置无效）

  - 如果监视的是 reactive 定义的，那么将强制开启深度监视，deep配置无效。
  - 如果监视的是 reactive 定义的对象中的每个属性，那么 deep 属性有效。

- 只能监视 ref 定义的值、数组、对象等，不可以监视基本数据类型，如果非要这样：

  ```js
  setup() {
      let person = reactive({
          name:"chenshibo",
          age:18
      })
      // 调用 watch 函数，也可以写成数组
      watch(() => person.age, (newValue, oldValue) => {
          console.log('sum改变了', newValue, oldValue )
      },{immediate:true})
  }
  ```

- ref 对象的基本数据类型，不要加上 .value，如果是 ref 定义的对象，那么就要 .value，但是如果是 reactive 定义的对象，那么就不要加上 .value

## watchEffect

自动开启 immediate：上来就调用一次

只有在函数中使用到的数据，才需要监视，其他的都不需要监视，不需要指明监视哪个属性

![image-20230604212231913](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230604212231913.png)

# 生命周期

在 vue2 中的生命周期钩子，在 vue3 中也可以使用，用选项式 api 和 vue2 的用法相同，但在 组合式API 中有一些不一样，在组合式API中，没有 beforeCreate 和 created，都包含在 setup 中，setup先于所有，每次使用前应当先引入  ：

- `onBeforeMount(()=>{})`
- onMounted
- onBeforeUpdate
- onUpdated
- onBeforeUnmount
- onUnmonted

# 自定义hooks

如果将所有的代码都写在组件中，那么不同的数据以及不同的方法将会都被写在一起，比较乱

hooks ：把一系列实现同一功能的所有 数据、生命周期、方法等都写在一个文件中，提高代码复用

新建hooks 文件夹，新建 userPoint.js，代码：

```js
import { reactive, onMounted, onBeforeUnmount } from 'vue';
export default function () {
  let point = reactive({
    x: 0,
    y: 0,
  });
  function savePoint(event) {
    point.x = event.pageX;
    point.y = event.pageY;
    console.log(event.pageX, event.pageY);
  }
  onMounted(() => {
    window.addEventListener('click', savePoint);
  });
  onBeforeUnmount(() => {
    window.removeEventListener('click', savePoint);
  });
  return point;
}
```

App.vue

```js
<script>
import userPoint from './hooks/usePoint.js';
export default {
  setup() {
    let point = userPoint();
    return {
      point,
    };
  },
};
</script>

<template>
  <div>当前鼠标位置：{{ point.x }}---{{ point.y }}</div>
</template>

<style scoped></style>
```

# 异步引入

如果是普通引入子组件，那么页面将会一直等待子组件加载完成，再进行渲染，使用异步引入，当组件完成渲染时，立刻渲染，子组件加载完成后再渲染

使用：

```js
import {defineAsyncComponent} from 'vue'
const Child = defineAsyncComponent(()=>import('./component/Child'))
```

这时 app会先出现，等待 子组件 完成后出现，但是这样页面会有一些抖动

这时我们可以用 Suspense 将其包裹

```html
<template>
	<div class='app'>
        <Suspense>
    		<template v-slot='default'>
                <Child></Child>
			</template>
            <template v-slot='fallback'>
                <h3>
                    稍等，加载中......
                </h3>
			</template>
    	</Suspense>
    </div>
</template>
```

当 Child 组件没加载完时，将会展示 稍等，加载中......

使用异步引入后，在 Child 组件中可以返回 Promise，没有的话不行

# 组件通信方式

1. props 

   使用 `defineProps`获取父组件传递的数据

   props 的数据是只读的

   ```js
   // 父
   <Child info="我是曹操" :money="money"></Child>
   // 子
   let props = defineProps(['info','money']); //数组|对象写法都可以
   <p>{{ info }}</p>
   <p>{{ money }}</p>
   ```

2. 自定义事件

   子组件给父组件传递数据，使用 `defineEmits`方法返回函数触发自定义事件 

   子组件：

   ```vue
   <template>
     <div class="child">
       <p>我是子组件2</p>
       <button @click="handler">点击我触发自定义事件xxx</button>
       <button @click="$emit('click','AK47','J20')">点击我触发自定义事件click</button>
     </div>
   </template>
   <script setup lang="ts">
   //利用defineEmits方法返回函数触发自定义事件
   //defineEmits方法不需要引入直接使用
   let $emit = defineEmits(['xxx','click']);
   //按钮点击回调
   const handler = () => {
     //第一个参数:事件类型 第二个|三个|N参数即为注入数据
       $emit('xxx','东风导弹','航母');
   };
   </script>
   ```

   父组件：

   ```js
   <Event2 @xxx="handler3" @click="handler4"></Event2>
   const handler3 = (param1,param2)=>{
       console.log(param1,param2);
   }
   ```

3. 在 vue3 中由于已经没有了 vm 实例，所以已经无法绑定全局事件总线，在 vue3 中使用`mitt`这个插件实现。

   在 src 文件夹下新建 bus 文件夹，并新建 index.js 文件

   ```js
   //引入mitt插件:mitt一个方法,方法执行会返回bus对象
   import mitt from 'mitt';
   const $bus = mitt();
   export default $bus;
   ```

   在需要收到数据的文件中注册事件

   ```js
   import $bus from "../../bus";
   //组合式API函数
   import { onMounted } from "vue";
   //组件挂载完毕的时候,当前组件绑定一个事件,接受将来兄弟组件传递的数据
   onMounted(() => {
     //第一个参数:即为事件类型  第二个参数:即为事件回调
     $bus.on("car", (car) => {
       console.log(car);
     });
   });
   ```

   在需要发送数据的地方触发事件：

   ```vue
   <template>
     <div class="child2">
        <h2>我是子组件2:曹丕</h2>
        <button @click="handler">点击我给兄弟送一台法拉利</button>
     </div>
   </template>
   <script setup lang="ts">
   //引入$bus对象
   import $bus from '../../bus';
   //点击按钮回调
   const handler = ()=>{
     $bus.emit('car',{car:"法拉利"});
   }
   </script>
   ```

4. v-model 实现父子组件数据同步 props + 自定义事件

   原理：父亲把一个响应式数据传给儿子，并且传递一个自定义事件，儿子拿到响应式数据，触发自定义事件，将这个响应式数据加1000传递给父亲，父亲通过函数拿到这个数据，赋值给响应式数据。

   父组件:

   ```vue
   <template>
   	<Child :modelValue="money" @update:modelValue="handler"></Child>
   </template>
   <script setup lang="ts">
       import { ref } from "vue";
       let money = ref(10000);
       const handler = (num: number) => {
         //将来接受子组件传递过来的数据
         money.value = num;
       };
   </script>
   ```

   子组件：

   ```vue
   <template>
     <div class="child">
       <h3>钱数:{{ modelValue }}</h3>
       <button @click="handler">父子组件数据同步</button>
     </div>
   </template>
   
   <script setup lang="ts">
   //接受props
   let props = defineProps(["modelValue"]);
   let $emit = defineEmits(['update:modelValue']);
   //子组件内部按钮的点击回调
   const handler = ()=>{
      //触发自定义事件
      $emit('update:modelValue',props.modelValue + 1000);
   }
   </script>
   ```

5. useAttrs 相当于 vue2 中的 $attrs，但是比 vue2 高级些，他也能接收自定义事件

   凡是没有被 `defineProps`接收的参数，都会被留存到 `$attrs`中，同时没有被`defineEmits`接收的，也会保存在`$attrs`中

6. 使用 `ref`以及`$parent`

   父组件中给子组件加上 ref 属性，使用`son = ref()`获取子组件实例，再在子组件中使用`defineExpose`将需要让父组件看到的数据暴露出来，父组件即可使用子组件的数据。

   父组件

   ```vue
   <template>
     <div class="box">
       <button @click="handler">找我的儿子曹植借10元</button>
       <Son ref="son"></Son>
     </div>
   </template>
   
   <script setup lang="ts">
   //ref:可以获取真实的DOM节点,可以获取到子组件实例VC
   //$parent:可以在子组件内部获取到父组件的实例
   //引入子组件
   import Son from './Son.vue'
   import {ref} from 'vue';
   //父组件钱数
   let money = ref(100000000);
   //获取子组件的实例
   let son = ref();
   //父组件内部按钮点击回调
   const handler = ()=>{
      money.value+=10;
      //儿子钱数减去10
      son.value.money-=10;
   }
   //对外暴露
   defineExpose({
      money
   })
   </script>
   ```

   子组件：

   ```vue
   <script setup lang="ts">
   import {ref} from 'vue';
   //儿子钱数
   let money = ref(666);
   //组件内部数据对外关闭的，别人不能访问
   //如果想让外部访问需要通过defineExpose方法对外暴露
   defineExpose({
     money,
     fly
   })
   </script>
   ```

   如果子组件想要使用父组件的数据，使用`$parent`，为按钮点击事件注入`$parent`参数，并在事件中接收，然后在父组件中使用`defineExpose`暴露出来想要被使用的数据，即可使用`$parent.value`使用数据。

   父组件：

   ```js
   //对外暴露
   defineExpose({
     money,
   });
   ```

   子组件：

   ```js
   <button @click="handler($parent)">点击我爸爸给我10000元</button>
   const handler = ($parent)=>{
      money.value+=10000;
      $parent.money-=10000;
   }
   ```

7. provide 和 inject

   实现祖孙组件间通信，父子组件也行，在任何后代间都可以传递，但是一般我们都用于祖孙，父子就使用 props

   传数据：`provide(传入数据的名字，传入的数据)`

   接收数据：`inject(得到数据的名字)`

8. vuex 集中式状态管理工具，可以实现任意组件之间的通信

   核心概念：`state`,`mutations`,`actions`,`getters`,`modules`

   pinia 集中式状态管理工具，可以实现任意组件之间的通信

   核心概念：`state`,`actions`,`getters`

   使用：

   - 在 src 下新建文件夹 store，编写 index.ts 文件

     ```js
     //创建大仓库
     import { createPinia } from 'pinia';
     //createPinia方法可以用于创建大仓库
     let store = createPinia();
     //对外暴露,安装仓库
     export default store;
     ```

   - 在 mian.ts 下引入仓库：

     ```js
     //引入仓库
     import store from './store'
     app.use(store)
     ```

   - 在 store 文件夹下新建 modules 文件夹，存放子模块

     新建 info.ts 文件

     这里有两种写法：选项式API 以及 组合式API

     选项式API：

     ```js
     //定义info小仓库
     import { defineStore } from "pinia";
     //第一个仓库:小仓库名字  第二个参数:小仓库配置对象
     //defineStore方法执行会返回一个函数,函数作用就是让组件可以获取到仓库数据
     let useInfoStore = defineStore("info", {
         //存储数据:state
         state: () => {
             return {
                 count: 99,
                 arr: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
             }
         },
         actions: {
             //注意:函数没有context上下文对象
             //没有commit、没有mutations去修改数据
             updateNum(a: number, b: number) {
                 this.count += a;
             }
         },
         getters: {
             total() {
                 let result:any = this.arr.reduce((prev: number, next: number) => {
                     return prev + next;
                 }, 0);
                 return result;
             }
         }
     });
     //对外暴露方法
     export default useInfoStore;
     ```

     组合式API：

     ```js
     //定义组合式API仓库
     import { defineStore } from "pinia";
     import { ref, computed,watch} from 'vue';
     //创建小仓库
     let useTodoStore = defineStore('todo', () => {
         let todos = ref([{ id: 1, title: '吃饭' }, { id: 2, title: '睡觉' }, { id: 3, title: '打豆豆' }]);
         let arr = ref([1,2,3,4,5]);
     
         const total = computed(() => {
             return arr.value.reduce((prev, next) => {
                 return prev + next;
             }, 0)
         })
         //务必要返回一个对象:属性与方法可以提供给组件使用
         return {
             todos,
             arr,
             total,
             updateTodo() {
                 todos.value.push({ id: 4, title: '组合式API方法' });
             }
         }
     });
     
     export default useTodoStore;
     ```

     在组件中使用：

     ```js
     import useInfoStore from "../../store/modules/info";
     let infoStore = useInfoStore();
     import useTodoStore from "../../store/modules/todo";
     let todoStore = useTodoStore();
     todoStore.updateTodo();
     ```

9. 使用插槽进行数据传递

   默认插槽：在子组件中使用`<slot></slot>`可以留出位置用于父组件传递

   具名插槽：在子组件中使用`<slot name="name"></slot>`，为子组件起名字，然后再父组件中使用`<template v-slot="name"></template>`或者`<template #name>填充的内容</template>`

   作用域插槽：再子组件中使用`<slot :$row="item" :$index="index"></slot>`，使用这种方式，就将 $row 和  $index 传递给父组件了，然后在父组件中`<template v-slot="{ $row, $index }"></template>`

   父组件：

   ```vue
   <Test1 :todos="todos">
       <template v-slot="{ $row, $index }">
           <p :style="{ color: $row.done ? 'green' : 'red' }">
               {{ $row.title }}--{{ $index }}
           </p>
   	</template>
   </Test1>
   ```

   子组件：

   ```js
   <template>
     <div class="box">
       <h1>作用域插槽</h1>
       <ul>
         <li v-for="(item, index) in todos" :key="item.id">
           <!--作用域插槽:可以讲数据回传给父组件-->
           <slot :$row="item" :$index="index"></slot>
         </li>
       </ul>
     </div>
   </template>
   ```

   
