# vue复习

## vue2与vue3

**vue2**

```js
const app = new Vue({
    el: '#app',
    data: {
        message: 'hello Vue.js',
    },
});
```

**vue3**

使用CDN来导入vue3

```html
<script src="https://unpkg.com/vue@next"></script>
```



```js
const Counter = {
    data() {
        return {
            counter: 0,
        };
    },
    methods: {
        add(){
            this.counter++
        },
        sub(){
            this.counter--
        }
    },
};
Vue.createApp(Counter).mount('#counter');
```

## vue指令

### 1.`v-bind`动态绑定

可以用于绑定属性，可以动态绑定class，href，src各种属性。

语法糖写法：v-bind:class="" -> :class=""

例子：

```html
<div id="app">
    <span v-bind:title="message">悬停几秒来显示信息</span>
</div>
<script>
   const app = {
       data() {
           return {
               message: 'Your page date is ' + new Date().toLocaleDateString(),
           };
       },
   };
Vue.createApp(app).mount('#app');
</script>
```

### 2.`v-on`事件监听

用于绑定事件监听

语法糖：v-on:click="function()" -> @click="function()"

```html
<div id="app">
    {{message}}
        <button v-on:click="reverseMessage()">点击反转字体</button>
</div>
<script>
            const app = {
                data() {
                    return {
                        message: 'Hello Vue.js!',
                    };
                },
                methods: {
                    reverseMessage() {
                        this.message = this.message.split('').reverse().join('');
                    },
                },
            };
Vue.createApp(app).mount('#app');
</script>
```

#### 事件修饰符

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 `v-on` 提供了**事件修饰符**。之前提过，修饰符是由点开头的指令后缀来表示的。

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`
- `.passive`

```html
<!-- 阻止单击事件继续冒泡 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>
```

TIP

使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 `@click.prevent.self` 会阻止**元素本身及其子元素的点击的默认行为**，而 `@click.self.prevent` 只会阻止对元素自身的点击的默认行为。

```html
<!-- 点击事件将只会触发一次 -->
<a @click.once="doThis"></a>
```

1
2

不像其它只能对原生的 DOM 事件起作用的修饰符，`.once` 修饰符还能被用到自定义的[组件事件](https://v3.cn.vuejs.org/guide/component-custom-events.html)上。如果你还没有阅读关于组件的文档，现在大可不必担心。

Vue 还对应 [`addEventListener` 中的 passive 选项](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)提供了 `.passive` 修饰符。

```html
<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发，   -->
<!-- 而不会等待 `onScroll` 完成，                    -->
<!-- 以防止其中包含 `event.preventDefault()` 的情况  -->
<div @scroll.passive="onScroll">...</div>
```

1
2
3
4

这个 `.passive` 修饰符尤其能够提升移动端的性能。

TIP

不要把 `.passive` 和 `.prevent` 一起使用，因为 `.prevent` 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，`.passive` 会告诉浏览器你*不想*阻止事件的默认行为。

#### 按键修饰符

在监听键盘事件时，我们经常需要检查特定的按键。Vue 允许为 `v-on` 或者 `@` 在监听键盘事件时添加按键修饰符：

```html
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input @keyup.enter="submit" />
```

你可以直接将 [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) 暴露的任意有效按键名转换为 kebab-case 来作为修饰符。

```html
<input @keyup.page-down="onPageDown" />
```

在上示例中，处理函数只会在 `$event.key` 等于 `'PageDown'` 时被调用。

#### 按键别名

Vue 为最常用的键提供了别名：

- `.enter`
- `.tab`
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

#### 系统修饰键

可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

提示

注意：在 Mac 系统键盘上，meta 对应 command 键 (⌘)。在 Windows 系统键盘 meta 对应 Windows 徽标键 (⊞)。在 Sun 操作系统键盘上，meta 对应实心宝石键 (◆)。在其他特定键盘上，尤其在 MIT 和 Lisp 机器的键盘、以及其后继产品，比如 Knight 键盘、space-cadet 键盘，meta 被标记为“META”。在 Symbolics 键盘上，meta 被标记为“META”或者“Meta”。

例如：

```html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

请注意修饰键与常规按键不同，在和 `keyup` 事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，只有在按住 `ctrl` 的情况下释放其它按键，才能触发 `keyup.ctrl`。而单单释放 `ctrl` 也不会触发事件。

#### `.exact` 修饰符

`.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件。

```html
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button @click.exact="onClick">A</button>
```

#### 鼠标按钮修饰符

- `.left`
- `.right`
- `.middle`

这些修饰符会限制处理函数仅响应特定的鼠标按钮。        

### 3.`v-for`事件循环

使用`v-for`进行事件循环，语法：

```html
<li v-for="item in items"></li>
<script>
    Vue.createApp({
        data() {
            return {
                items: [{ message: 'Foo' }, { message: 'Bar' }]
            }
        }
    }).mount('#array-rendering')
</script>
```

也可以使用`of`代替`in`作为分隔符。在 `v-for` 块中，我们可以访问所有父作用域的 property。`v-for` 还支持一个可选的第二个参数，即当前项的索引。

```html
<ul id="array-with-index">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      parentMessage: 'Parent',
      items: [{ message: 'Foo' }, { message: 'Bar' }]
    }
  }
}).mount('#array-with-index')
```

#### 在 `v-for` 里使用对象

你也可以用 `v-for` 来遍历一个对象的 property。

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      myObject: {
        title: 'How to do lists in Vue',
        author: 'Jane Doe',
        publishedAt: '2016-04-10'
      }
    }
  }
}).mount('#v-for-object')
```

结果：

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_3" scrolling="no" src="https://codepen.io/Vue/embed/NWqLjqy?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=js%2Cresult&amp;user=Vue&amp;slug-hash=NWqLjqy&amp;pen-title=v-for%20with%20Object&amp;name=cp_embed_3" title="v-for with Object" loading="lazy" id="cp_embed_NWqLjqy" style="width: 740px; overflow: hidden; display: block;"></iframe>

你也可以提供第二个的参数为 property 名称 (也就是键名 key)：

```html
<li v-for="(value, name) in myObject">
  {{ name }}: {{ value }}
</li>
```

```html
<div id="app">
    <ul>
        <li v-for="todo in todos">{{todo.text}}</li>
    </ul>
</div>
<script>
    const app = {
        data() {
            return {
                todos: [
                    { text: 'Learn JavaScript' },
                    { text: 'Learn Vue' },
                    { text: 'Build something awesome' },
                ],
            };
        },
    };
    Vue.createApp(app).mount('#app');
</script>
```

还可以用第三个参数作为索引：

```html
<li v-for="(value, name, index) in myObject">
  {{ index }}. {{ name }}: {{ value }}
</li>
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_5" scrolling="no" src="https://codepen.io/Vue/embed/abOaWdo?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=js%2Cresult&amp;user=Vue&amp;slug-hash=abOaWdo&amp;pen-title=v-for%20with%20Object%20key%20and%20index&amp;name=cp_embed_5" title="v-for with Object key and index" loading="lazy" id="cp_embed_abOaWdo" style="width: 740px; overflow: hidden; display: block;"></iframe>

实例：

下面是一个简单的 todo 列表的完整例子：

```html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    />
    <button>Add</button>
  </form>
  <ul>
    <todo-item
      v-for="(todo, index) in todos"
      :key="todo.id"
      :title="todo.title"
      @remove="todos.splice(index, 1)"
    ></todo-item>
  </ul>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      newTodoText: '',
      todos: [
        {
          id: 1,
          title: 'Do the dishes'
        },
        {
          id: 2,
          title: 'Take out the trash'
        },
        {
          id: 3,
          title: 'Mow the lawn'
        }
      ],
      nextTodoId: 4
    }
  },
  methods: {
    addNewTodo() {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})

app.component('todo-item', {
  template: `
    <li>
      {{ title }}
      <button @click="$emit('remove')">Remove</button>
    </li>
  `,
  props: ['title'],
  emits: ['remove']
})

app.mount('#todo-list-example')
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_7" scrolling="no" src="https://codepen.io/Vue/embed/abOaWpz?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=js%2Cresult&amp;user=Vue&amp;slug-hash=abOaWpz&amp;pen-title=v-for%20with%20components&amp;name=cp_embed_7" title="v-for with components" loading="lazy" id="cp_embed_abOaWpz" style="width: 740px; overflow: hidden; display: block;"></iframe>

### 4.v-if/v-show

v-if 意味着事件的销毁和重建，v-show将元素添加一个`display:none`属性将元素隐藏，一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

当`v-if`和`v-show`一起出现时，`v-if`优先级高于`v-show`，最好不要同时使用。

```html
    <script src="https://unpkg.com/vue@next"></script>
    <div id="app">
      //v-if用法相同
      <span v-show="isShow">我出来啦</span>
      <button @click="reserveShow()">显示/消失</button>
    </div>
    <script>
      const app = {
        data() {
          return {
            isShow: true,
          };
        },
        methods: {
          reserveShow() {
            this.isShow = !this.isShow;
          },
        },
      };
      Vue.createApp(app).mount('#app');
    </script>
```

### 5. `v-once`事件渲染

加了v-once的内容只会渲染一次，之后内容改变不会重新进行渲染。

```html
<span v-once>这个将不会改变: {{ msg }}</span>
```

### 6. `v-text`渲染文字

将内容转换为文字

```html
<span v-text="message"></span>
```

相当于：

```html
<span>{{message}}</span>
```

### 7. `v-html`渲染文档模型

将可识别的内容转换为html再渲染出来

```html
<span v-html='html'></span>
//html = "<h1>Nihaoa</h1>"
```

### 8. `v-model`表单绑定

你可以用 v-model 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。

`v-model` 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

- text 和 textarea 元素使用 `value` property 和 `input` 事件；
- checkbox 和 radio 使用 `checked` property 和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

#### 文本 (Text)

```html
<input v-model="message" placeholder="edit me" />
<p>Message is: {{ message }}</p>
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_1" scrolling="no" src="https://codepen.io/Vue/embed/eYNPEqj?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=eYNPEqj&amp;pen-title=Handling%20forms%3A%20basic%20v-model&amp;name=cp_embed_1" title="Handling forms: basic v-model" loading="lazy" id="cp_embed_eYNPEqj" style="width: 740px; overflow: hidden; display: block;"></iframe>

#### 多行文本 (Textarea)

```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br />
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_2" scrolling="no" src="https://codepen.io/Vue/embed/xxGyXaG?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=xxGyXaG&amp;pen-title=Handling%20forms%3A%20textarea&amp;name=cp_embed_2" title="Handling forms: textarea" loading="lazy" id="cp_embed_xxGyXaG" style="width: 740px; overflow: hidden; display: block;"></iframe>

插值在 textarea 中不起作用，请使用 `v-model` 来代替。

```html
<!-- bad -->
<textarea>{{ text }}</textarea>

<!-- good -->
<textarea v-model="text"></textarea>
```

#### 复选框 (Checkbox)

单个复选框，绑定到布尔值：

```html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_3" scrolling="no" src="https://codepen.io/Vue/embed/PoqyJVE?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=PoqyJVE&amp;pen-title=Handling%20forms%3A%20checkbox&amp;name=cp_embed_3" title="Handling forms: checkbox" loading="lazy" id="cp_embed_PoqyJVE" style="width: 740px; overflow: hidden; display: block;"></iframe>

多个复选框，绑定到同一个数组：

```html
<div id="v-model-multiple-checkboxes">
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames" />
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
  <label for="mike">Mike</label>
  <br />
  <span>Checked names: {{ checkedNames }}</span>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      checkedNames: []
    }
  }
}).mount('#v-model-multiple-checkboxes')
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_4" scrolling="no" src="https://codepen.io/Vue/embed/bGdmoyj?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=bGdmoyj&amp;pen-title=Handling%20forms%3A%20multiple%20checkboxes&amp;name=cp_embed_4" title="Handling forms: multiple checkboxes" loading="lazy" id="cp_embed_bGdmoyj" style="width: 740px; overflow: hidden; display: block;"></iframe>

#### 单选框 (Radio)

```html
<div id="v-model-radiobutton">
  <input type="radio" id="one" value="One" v-model="picked" />
  <label for="one">One</label>
  <br />
  <input type="radio" id="two" value="Two" v-model="picked" />
  <label for="two">Two</label>
  <br />
  <span>Picked: {{ picked }}</span>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      picked: ''
    }
  }
}).mount('#v-model-radiobutton')
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_5" scrolling="no" src="https://codepen.io/Vue/embed/MWwPEMM?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=MWwPEMM&amp;pen-title=Handling%20forms%3A%20radiobutton&amp;name=cp_embed_5" title="Handling forms: radiobutton" loading="lazy" id="cp_embed_MWwPEMM" style="width: 740px; overflow: hidden; display: block;"></iframe>

#### 选择框 (Select)

单选时：

```html
<div id="v-model-select" class="demo">
  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      selected: ''
    }
  }
}).mount('#v-model-select')
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_6" scrolling="no" src="https://codepen.io/Vue/embed/KKpGydL?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=KKpGydL&amp;pen-title=Handling%20forms%3A%20select&amp;name=cp_embed_6" title="Handling forms: select" loading="lazy" id="cp_embed_KKpGydL" style="width: 740px; overflow: hidden; display: block;"></iframe>

注意

如果 `v-model` 表达式的初始值未能匹配任何选项，`<select>` 元素将被渲染为“未选中”状态。在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 `change` 事件。因此，更推荐像上面这样提供一个值为空的禁用选项。

多选时 (绑定到一个数组)：

```html
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br />
<span>Selected: {{ selected }}</span>
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_7" scrolling="no" src="https://codepen.io/Vue/embed/gOpBXPz?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=gOpBXPz&amp;pen-title=Handling%20forms%3A%20select%20bound%20to%20array&amp;name=cp_embed_7" title="Handling forms: select bound to array" loading="lazy" id="cp_embed_gOpBXPz" style="width: 740px; overflow: hidden; display: block;"></iframe>

用 `v-for` 渲染的动态选项：

```html
<div id="v-model-select-dynamic" class="demo">
  <select v-model="selected">
    <option v-for="option in options" :value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>

```

```js
Vue.createApp({
  data() {
    return {
      selected: 'A',
      options: [
        { text: 'One', value: 'A' },
        { text: 'Two', value: 'B' },
        { text: 'Three', value: 'C' }
      ]
    }
  }
}).mount('#v-model-select-dynamic')
```

<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_8" scrolling="no" src="https://codepen.io/Vue/embed/abORVZm?theme-id=39028&amp;editable=true&amp;height=300&amp;default-tab=result&amp;user=Vue&amp;slug-hash=abORVZm&amp;pen-title=Handling%20forms%3A%20select%20with%20dynamic%20options&amp;name=cp_embed_8" title="Handling forms: select with dynamic options" loading="lazy" id="cp_embed_abORVZm" style="width: 740px; overflow: hidden; display: block;"></iframe>

## 属性

### 1. data

用于存放数据，data为一个函数，Vue接受返回值。

```js
const app = Vue.createApp({
    data() {
        return {
            message: 'nihao',
        };
    }
}).mount('#app');
```

### 2. methods

一般在其中写方法，触发事件的函数

```js
const app = {
    data() {
        return {
            message: 'Hello Vue.js!',
        };
    },
    methods: {
        reverseMessage() {
            this.message = this.message.split('').reverse().join('');
        },
    },
};
Vue.createApp(app).mount('#app');	
```

### 3. computed

计算属性，一般在其中写一些计算，例如add()函数

```html
<div id="app">
     {{fullname}}
</div>
<script>
    const app = {
        data() {
            return {
                firstName: 'chen',
                lastName: 'Shibo',
            };
        },
        computed: {
            fullname() {
                return this.firstName + ' ' + this.lastName;
            },
        },
    };
    Vue.createApp(app).mount('#app');
</script>
```

在computed中有getter与setter属性，其中默认为getter属性，不过也可以为computed提供一个setter属性。

```js
const app = {
    data() {
        return {
            firstName: 'Chen',
            lastName: 'Shibo',
        };
    },
    computed: {
        fullName: {
            get() {
                return this.firstName + ' ' + this.lastName;
            },
            set(newName) {
                const name = newName.split(' ');
                this.firstName = name[0];
                this.lastName = name[name.length - 1];
            },
        },
    }
};
Vue.createApp(app).mount('#app');
```

使用setter只需要使用：

```js
app.fullName = "Chen Shibo";
```

也可以在methods中使用this关键词进行修改：

```js
methods: {
    change() {
        //setter的使用
        this.fullName = 'Liu Shibo';
    },
},
```

实例：

```html
<div id="app">
    {{fullName}}
    <button @click="change()">change</button>
</div>
<script>
    const app = {
        data() {
            return {
                firstName: 'Chen',
                lastName: 'Shibo',
            };
        },
        computed: {
            fullName: {
                get() {
                    return this.firstName + ' ' + this.lastName;
                },
                set(newName) {
                    const name = newName.split(' ');
                    this.firstName = name[0];
                    this.lastName = name[name.length - 1];
                },
            },
        },
        methods: {
            change() {
                this.fullName = 'Liu Shibo';
            },
        },
    };
    Vue.createApp(app).mount('#app');
</script>
```

### 4. watch

自定义监听器，监听内容每当内容改变时触发。

可以调用methods中的方法，使用时用v-model动态绑定。

使用 `watch` 选项允许我们执行异步操作 (访问一个 API)，并设置一个执行该操作的条件。这些都是计算属性无法做到的。

实例：

```html
<div id="app">
    <p>ask a question</p>
    <input type="text" v-model="question" />
    <p>{{answer}}</p>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
    const watchapp = Vue.createApp({
        data() {
            return {
                question: '',
                answer: 'Please input a question and end of "?"',
            };
        },
        watch: {
            question(newQuestion) {
                if (newQuestion.indexOf('？') > -1) {
                    this.getAnswer();
                }
            },
        },
        methods: {
            getAnswer() {
                this.answer = 'Thinking...';
                axios
                    .get('https://yesno.wtf/api')
                    .then((response) => {
                    this.answer = response.data.answer;
                })
                    .catch((error) => {
                    this.answer = "Sorry I can't find it";
                });
            },
        },
    }).mount('#app');
</script>
```

## Class与Style绑定

### Class绑定

因为class以及style都是一个attribute，所以可以使用`v-bind`进行动态绑定，绑定有对象和数组语法：

#### 对象语法

可以传给`:class`一个对象，动态切换class：

 ```html
 <div :class="{ active : isActive }"></div>
 ```

上面这句话表明`active`这个类是否存在取决于`isActive`这个值的真假.

绑定的数据对象不必内联定义在模板里：

```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```

渲染的结果和上面一样。我们也可以在这里绑定一个返回对象的[计算属性](https://v3.cn.vuejs.org/guide/computed.html)。这是一个常用且强大的模式：

```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

#### 数组语法

我们可以把一个数组传给 `:class`，以应用一个 class 列表：

```html
<div :class="[activeClass, errorClass]"></div>
```

```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

渲染的结果为：

```html
<div class="active text-danger"></div>
```

如果你想根据条件切换列表中的 class，可以使用三元表达式：

```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

这样写将始终添加 `errorClass`，但是只有在 `isActive` 为 truthy[[1\]](https://v3.cn.vuejs.org/guide/class-and-style.html#footnote-1) 时才添加 `activeClass`。

不过，当有多个条件 class 时这样写有些繁琐。所以在数组语法中也可以使用对象语法：

```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### Style绑定

#### 对象语法

`:style` 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名：

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```html
<div :style="styleObject"></div>
```

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

同样的，对象语法常常结合返回对象的计算属性使用。

## 组件

组件的基本使用方法：

- 首先构建组件
- 在components中注册组件
- 使用组件

```html
    <div id="app"><todo-item></todo-item></div>
    <script src="https://unpkg.com/vue@next"></script>
    <script>
      // 创建组件
      const TodoItem = {
        template: '<li>Hello liuneng!</li>',
      };
      //创建vue应用
      const app = Vue.createApp({
        data() {
          return {
            message: 'nihao',
          };
        },
        // 注册组件
        components: {
          TodoItem,
        },
        //g
      }).mount('#app');
    </script>
```

### 1. 组件实例 property

在前面的指南中，我们认识了 `data` property。在 `data` 中定义的 property 是通过组件实例暴露的：

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4
```

### 2. 生命周期钩子

每个组件在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会。

比如 [created](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#created) 钩子可以用来在一个实例被创建之后执行代码：

```js
Vue.createApp({
  data() {
    return { count: 1}
  },
  created() {
    // `this` 指向 vm 实例
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

也有一些其它的钩子，在实例生命周期的不同阶段被调用，如 [mounted](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#mounted)、[updated](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#updated) 和 [unmounted](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#unmounted)。生命周期钩子的 `this` 上下文指向调用它的当前活动实例。

TIP

不要在选项 property 或回调上使用[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。

![实例的生命周期](https://v3.cn.vuejs.org/images/lifecycle.svg)

![img](https://img-blog.csdnimg.cn/20200515103424403.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyMTMyMw==,size_16,color_FFFFFF,t_70)

## 组件传参

### 1. 父传子

子组件中定义`props:['title']`，使用子组件时可以在子组件传入`props`中定义的元素进行传递。

```html
<div id="blog-post-demo" class="demo">
    <blog-post title="you are the first"></blog-post>
    <blog-post title="you are the second"></blog-post>
    <blog-post title="you are the third"></blog-post>
</div>
<script>
    const app = Vue.createApp({});
    app.component('blog-post', {
        props: ['title'],
        template: `<h4>{{ title }}</h4>`,
    });
    app.mount('#blog-post-demo');
</script>
```

### 2. 子传父

子组件定义发送事件this.$emit(参数一，参数二) 参数一为触发的函数，参数二为传递的参数

父组件定义一个函数进行接受 使用@加上方参数一进行接收 然后触发函数 使用一个参数作为接受函数的形参  此形参就是子组件传递过来的数据

```html
<div id="blog-posts-events-demo">
    <div :style="{ fontSize: postFontSize + 'em' }">
        <blog-post
                   v-for="post in posts"
                   :key="post.id"
                   :title="post.title"
                   @enlarge-text="postFontSize += 0.1"
                   ></blog-post>
    </div>
</div>
<script>
    const App = {
        data() {
            return {
                posts: [
                    {
                        id: 1,
                        title: 'My journey with Vue',
                    },
                    {
                        id: 2,
                        title: 'Blogging with Vue',
                    },
                    {
                        id: 3,
                        title: 'Why Vue is so fun',
                    },
                ],
                postFontSize: 1,
            };
        },
    };
    const app = Vue.createApp(App);
    app.component('blog-post', {
        props: ['title'],
        template: `
            <div class="blog-post">
                <h4>{{ title }}</h4>
                <button @click="$emit('enlargeText')">
                    Enlarge text
    </button>
    </div>
        `,
    });
    app.mount('#blog-posts-events-demo');
</script>
```







# 复习

## 数据绑定

Vue中的数据绑定使用的是`object.defineproperty`这里会将data中的数据添加到Vue实例中，使用`object.defineproperty`中的getter方法可以将_data中的数据都付给Vue实例中的data，同时，当我们修改data中的属性时，我们实际上是修改的 _data的值，并且调用 _data中的setter方法设置data，Vue做数据劫持。

## 阻止默认行为

在 a 标签中，我们想要拒绝 a 标签的点击跳转，可以使用`event.preventDefault()`阻止默认行为

在Vue中我们可以使用 `@click.prevent=function()`阻止默认行为

**阻止事件冒泡**

在 js 中，使用`event.stopPropagation`阻止时间冒泡

在 Vue 中，我们使用`@click.stop()`阻止冒泡

**事件只触发一次**

使用`@click.once`

​     
