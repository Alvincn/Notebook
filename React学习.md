# Hello React

## 官网

1. 英文官网:[ https://reactjs.org/](https://reactjs.org/)

2. 中文官网: https://react.docschina.org/

## Hello React

**初识React**

React是单向数据绑定，并且没有指令，配合JSX

使用React需要引入三个库：

1. `react.development.js`React的核心库，核心业务所在
2. `react-dom.development.js`支持react操作DOM
3. `babel.min.js`用于将 jsx 转为 js（react 默认写法为 jsx）

页面内使用react：

```js
<div id="test"></div>
<script type="text/babel" > /* 此处一定要写babel */
    //1.创建虚拟DOM
    const VDOM = <h1>Hello,React</h1> /* 此处一定不要写引号，因为不是字符串 */
	//2.渲染虚拟DOM到页面
	ReactDOM.render(VDOM,document.getElementById('test'))
</script>
```

> > 注
>
> 再`<script></script>`中的type必须保证是`text/babel`,这将使用babel将jsx语法转化为js，否则无法识别
>
> 在jsx中，虚拟DOM千万不要加引号（''/""），否则将会被当成字符串渲染到DOM上。

**使用原生js创建**

使用`React.createElement('xx',{id:'xx},'xx')`创建一般js对象。

第一个参数：要创建什么元素（span、h1）

第二个参数：创建的元素属性(id)

第三个参数：元素的内容

例如：`const VDOM = React.createElement('h1',{id:'title'},'Hello React')`

之后，再将此页面渲染：

`ReactDOM.render(VDOM,document.getElementByID('test'))`将上方创建的VDOM渲染到 id='test' 这个节点上。

代码：

```js
<div id="test"></div>
<script type="text/javascript" > 
    //1.创建虚拟DOM
    const VDOM = React.createElement('h1',{id:'title'},React.createElement('span',{},'Hello,React'))
	//2.渲染虚拟DOM到页面
	ReactDOM.render(VDOM,document.getElementById('test'))
</script>
```

**使用原生jsx创建**

首先创建一个虚拟DOM：`const VDOM = (<h1><span>Hello React</span></h1>)`

将此虚拟DOM交给React渲染成真实DOM：`ReactDOM.render(VDOM,document.getElementByID('test'))`

代码：

```js
<div id="test"></div>
<script type="text/babel" > /* 此处一定要写babel */
    //1.创建虚拟DOM
    const VDOM = (  /* 此处一定不要写引号，因为不是字符串 */
        <h1 id="title">
        <span>Hello,React</span>
        </h1>
    )
	//2.渲染虚拟DOM到页面
	ReactDOM.render(VDOM,document.getElementById('test'))
</script>	
```

## 虚拟DOM和真实DOM的比较

![虚拟DOM和真实DOM的比较](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20221117190231253.png)

虚拟DOM会比真实DOM的属性值少，因为虚拟DOM只是React用来渲染页面的，不必要用那么多属性。

# React基础

## jsx语法规则

jsx是React专用的 js 扩展，在原有的基础上新增了一些规则，规则如下：

1. 定义虚拟DOM时，不要添加引号。
2. 标签中混入JS表达式要用 {}
3. 样式的类名指定不要用class，要用className。
4. 内联样式，要用style={{key:value}}，第一个大括号代表内部是表达式而不是字符串，第二个大括号代表对象。
5. 有且只有一个根标签
6. 标签必须闭合，自闭合和完整标签均可。
7. 标签首字母如果是小写，则代表着HTMl标签元素，如果HTML中没有响应的标签元素，则报错
8. 标签首字母如果是大写，则代表着React组件，如果组件没有定义，则报错。

代码举例：

```js
<style>
    .title{
        background-color: orange;
        width: 200px;
    }
</style>
<div id="test"></div>
<script type="text/babel" >
    const myId = 'aTgUiGu'
    const myData = 'HeLlo,rEaCt'
    //1.创建虚拟DOM
    const VDOM = (
        <div>
            <h2 className="title" id={myId.toLowerCase()}>
                <span style={{color:'white',fontSize:'29px'}}>{myData.toLowerCase()}</span>
            </h2>
            <h2 className="title" id={myId.toUpperCase()}>
                <span style={{color:'white',fontSize:'29px'}}>{myData.toLowerCase()}</span>
            </h2>
            <input type="text"/>
        </div>
    )
    //2.渲染虚拟DOM到页面
    ReactDOM.render(VDOM,document.getElementById('test'))

</script>
```

## jsx渲染列表

```js
	<script type="text/babel" >

		//模拟一些数据
		const data = ['Angular','React','Vue']
		//1.创建虚拟DOM
		const VDOM = (
			<div>
				<h1>前端js框架列表</h1>
				<ul>
					{
						data.map((item,index)=>{
							return <li key={index}>{item}</li>
						})
					}
				</ul>
			</div>
		)
		//2.渲染虚拟DOM到页面
		ReactDOM.render(VDOM,document.getElementById('test'))
	</script>
```

>> 注
>
>	一定注意区分：【js语句(代码)】与【js表达式】
>			1. 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方
>						下面这些都是表达式：
>								(1). a
>								(2). a+b
>								(3). demo(1)
>								(4). arr.map()
>								(5). function test () {}
>			2. 语句(代码)：
>						下面这些都是语句(代码)：
>								(1).if(){}
>								(2).for(){}
>								(3).switch(){case:xxxx}
>
>		

## 定义组件

这里的组件和Vue其实差不多，分为两种创建组件的方式：

**函数式组件**

1. 函数式组件，类似于创建函数，只不过必须有返回值，且返回值为虚拟DOM

   ```js
   //1.创建函数式组件
   function MyComponent(){
       console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
       return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
   }
   ```

2. 渲染组件到页面

   `ReactDOM.render(<MyComponent/>,document.getElementByID('test'))`

> > 执行了ReactDOM.render(...)之后，发生了什么？
>
> 1. React解析组件标签，找到MyComponent组件
> 2. 发现组件时使用函数定义的，调用函数，将返回的虚拟DOM转化为真实DOM，随后呈现在页面中。

**类式组件**

1. 使用 class 关键字创建一个组件，必须继承于React.Component才会被当成组件且必须要写render，render必须有返回值，返回值为虚拟DOM

   ```js
   class MyComponent extends React.Component {
       render(){
           //render是放在哪里的？—— MyComponent的原型对象上，供实例使用。
           //render中的this是谁？—— MyComponent的实例对象 <=> MyComponent组件实例对象。
           console.log('render中的this:',this);
           return <h2>我是用类定义的组件(适用于【复杂组件】的定义)</h2>
       }
   }
   ```

2. 渲染组件到页面`ReactDOM.render(<MyComponent/>,document.getElementById('test'))`

> > 执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
>
> 1. React解析组件标签，找到了MyComponent组件。
> 2. 发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
> 3. 将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。

## 修改state

修改 state 不要直接`this.state  = `，

而是：`this.setState({})`

## refs

在 react 中使用 refs 方法：

1. 对需要获取的节点加上 refs 属性

   ```jsx
   <input ref={c => console.log(c)} type="text" placeholder="enter the name you search"/>
   
   ```

2. ref 中这个参数可以获取到这个节点，这里需要写一个回调函数，操作这个节点

   ```jsx
   <input ref={c => this.keyWordElement = c} type="text" placeholder="enter the name you search"/>
   ```

3. 然后在需要使用这个节点的地方使用：`this.refs.keyWordElement`

## 组件通信

### 父传子Props

使用props为子组件传递数据，用法：

```jsx
父组件，就是在子组件里放一个属性
class List extends Component {
    render() {
        return (
            <div>
                <ul className="todo-main">
                    <Item a={1}></Item>
                </ul>
            </div>
        );
    }
}
```

```jsx
子组件
使用 this.props.属性 进行接收
```

### 子传父

这里和 Vue 一样，就是父给子传一个函数，这个函数需要一个参数，子调用这个函数，并把数据传入，这样父就拿到了子传过来的实参了。

```jsx
父组件
class App extends Component {
    state = {
        todos: [{id: 1, name: '吃饭', done: true}, {id: 2, name: '睡觉', done: true}, {id: 3, name: '打豆豆', done: true}]
    }
    sonChange = (todo) => {
        this.state.todos.push({id: this.state.todos.length + 1, name: todo, done: false})
    }

    render() {
        return (
            <div className="todo-container">
                <div className="todo-wrap">
                    ---
                    <Header sonChange={this.sonChange}></Header>
                    --
                    <List todos={this.state.todos}></List>
                    <Footer></Footer>
                </div>
            </div>
        )
    }

}
```

```jsx
子组件
class Header extends Component {
    handleKeyUp = (event) => {
        if(event.keyCode !== 13) return
        --- 
        this.props.sonChange(event.target.value)
        ---
    }
    render() {
        return (
            <div>
                <div className="todo-header">
                    <input onKeyUp={this.handleKeyUp} type="text" placeholder="请输入你的任务名称，按回车键确认"/>
                </div>
            </div>
        );
    }
}
```

### 万能组件通信-消息订阅发布机制

这里我们使用一个工具库`PubSuBJs`，安装：`npm install pubsub-js`

使用方法：

- 引入：

  ```js
  import PubSub from 'pubsub-js'
  // commonJS
  const PubSub = require('pubsub-js')
  ```

- 订阅消息

  ```js
  // 定义一个函数
  var mySubscriber = function(msg,data) {
      console.log(msg,data)
  }
  // 订阅消息
  // 每次订阅消息都有一个token，之后如果不想订阅这个消息了，可以使用这个来取消订阅
  // 只要有东西 发布 这个消息，并携带内容，就可以接收到
      var token = PubSub.subscribe("My TOPIC", mySubscriber)
  ```

- 发布消息

  ```js
  // 只要发布消息，就会触发所有订阅这个消息的内容
  // 比如在这里发布，就会触发上边哪个订阅，从而触发 mySubscriber 这个函数
  PubSub.publish("My TOPIC",'hello world')
  ```

- 一般我们都在生命周期钩子中订阅消息，在需要使用的地方发布

  ```js
  componentDidMount() {
      // 订阅消息，等待 Search 发布
      this.token = PubSub.subscribe("updateUser", (users,data) => {
          this.updateUser(data)
      })
  }
  ```

- 最好在组件销毁时将他卸载

  ```js
  componentWillUnmount() {
      PubSub.unsubscribe(this.token)
  }
  ```

## 生命周期钩子

### 旧的生命周期

![image-20230504115225491](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504115225491.png)

**初始化阶段：**由ReactDOM.render()触发--初次渲染

1. constructor()
2. componentWillMount()
3. render()
4. componentDidMount() ===> 常用
   - 一般再这个钩子中做一些初始化的事，比如：开启定时器，发送网络请求、订阅消息（相当于Vue中的mounted）

**更新阶段：**由组件内部 this.setState() 或父组件 render 触发

1. shouldComponentUpdate() 
2. componentWillUpdate()
3. render()     ===> 必须用
4. componentDidUpdate()

**卸载组件：**由 ReactDOM.unmountComponentAtNode() 触发

1. componentWillUnmount() ===> 常用
   - 一般再这个钩子中做些收尾的事，例如：关闭定时器、取消订阅消息

### 新的生命周期

![image-20230504115049893](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504115049893.png)

> 新版本中可以使用旧的钩子，但是会出现警告
>
> ![image-20230504115434267](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504115434267.png)
>
> 需要将这个生命周期加上UNSAFE_，比如使用componentWillMount改成UNSAFE_componentWillMount，需要添加UNSAFE _的钩子：componentWillMount,componentWillReceiveProps,componentWillUpdate，也就是所有带Will的钩子都要加上这个，除了componentWillUnmount

 相对于旧的生命周期，新的生命周期取消了三个钩子，提出了两个新的钩子。

新的钩子：

1. getDerivedStateFromProps()
2. getSnapshotBeforeUpdate()

这两个新提出的钩子用法比较罕见，一般不用

## Diffing算法

在 React 中有 Diffing 算法，此算法用来在 state 更新后，重新  render 的时候比较前后节点是否相同，如果发现前后节点相同，那么直接使用之前的，如果不同，才会重新渲染此节点

- Diffing 比较的是标签，不能对标签中的内容进行比较

- 如果节点中 state 被修改，而此节点中还存在另外一个标签没有被修改，那么将会保留其中的标签

  ```jsx
  <span>
  	现在是：{this.state.date.toTimeString()}
      <input type="text"></input>
  </span>
  ```

  这里的 input 标签将会被保留

---

在 React，Vue中，想要遍历状态渲染节点都需要传入一个 key，作为唯一标识，最好是使用此状态专属的key，如果使用遍历的 index，会产生问题：

```jsx
class Person extends React.Component{
    state = {
        persons:[
            {id:1,name:'小张',age:18},
            {id:2,name:'小李',age:19}
        ]
    }
    add = () {
        const {persons} = this.state
        const p = {id:persons.length+1,name:'小陈',age:20}
        this.setState({persons:[p,...persons]})
    }
    render() {
        return (
        	<div>
                <h2>展示人员信息</h2>
                <button onClick={this.add}>添加小王</button>
                {
                    this.state.persons.map((personObj,index)=>{
                        return <li key={index}>{personObj.name}---{personObj.age}</li>
                    })
                }
            </div>
        )
    }
}
```

点击按钮，将会出现这个小陈，但是在这里边，有一个很严重的效率问题，使用id的效率很低

```
两道经典面试题：

1. react / vue 中的 key 有什么作用？（key 的内部原理是什么？）
2. 为什么遍历列表时，key 最好不要使用 index？

答：
1. 虚拟D0M中key的作用：
   1. 简单的说：key是虚拟DoM对象的标识，在更新显示时key起着极其重要的作用。
   2. 详细的说：当状态中的数据发生变化时，react会根据【新数据】生成【新的虚拟DoM】,
      随后React.进行【新虚拟DoM】与【旧虚拟DoM】的diff比较，比较规则如下：
      - 旧虚拟DoM中找到了与新虚拟DoM相同的key:
        (1),若虚拟D0M中内容没变，直接使用之前的真实D0M
        (2),若虚拟D0M中内容变了，则生成新的真实D0M,随后替换掉页面中之前的真实D0M
      - 旧虚拟DoM中未找到与新虚拟DoM相同的key
        根据数据创建新的真实D0M,随后渲染到到页面
2. 用index作为key可能会引发的问题：
   1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作：
      会产生没有必要的真实D0M更新=>界面效果没问题，但效率低。
   2. 如果结构中还包含输入类的D0M:
      会产生错误D0M更新=>界面有问题。
   3. 注意！如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，
      仅用于渲染列表用于展示，使用index作为key是没有问题的。
```

对上方代码进行慢动作回放：

```
使用 index 索引值作为 key
    初始数据：
        {id:1,name:'小张',age:18},
        {id:2,name:'小李',age:19}
    初始的虚拟DOM：
    	<li key=0>小张---18</li>
    	<li key=1>小李---19</li>
   	更新后的数据
   		{id:3,name:'小陈',age:20},
   		{id:1,name:'小张',age:18},
        {id:2,name:'小李',age:19},
    更新数据后的虚拟DOM：
    	<li key=0>小陈---20</li>
    	<li key=1>小李---19</li>
    	<li key=2>小张---18</li>
   	然后这个虚拟DOM根据 key 与上方初始虚拟DOM对比，发现全部对比不上，所以只能全部重新渲染 
```

使用数据的唯一标识（id）作为key，是不会有这个问题的

## prop类型限制

可以使用库，对prop类型进行限制，需要安装一个库：`prop-types`，就可以这样：

```jsx
import PropTypes from 'prop-types'
export default class Header extends Component {
    static propTypes = {
        // 需要传递的是一个函数，并且需要传递，不传递就报错
        addTodo:PropTypes.func.isRequired,
        // 如果是一个数组
        todos:PropTypes.array.isRequired,
     	// 如果是基本数据类型
        id:PropTypes.number.isRequired
    }
}
```







# 生态

- React、react-router、Axios、Redux、Ant-design(蚂蚁金服UI库)
- 小程序：taro(多端开发)
- 整合：DVA（简化API）
- 脚手架：create-react-app

# 脚手架

## 基础使用

安装脚手架：`npm install -g create-react-app`

判断状态：`create-react-app -V`

成功会显示版本号

创建项目：`create-react-app 项目名`

启动项目：`npm start`

使用 React 最好使用 yarn，因为都是 Facebook 出品的



## 项目目录

生成项目后查看 package.json，可以看到如下四个命令

![image-20230504204054547](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504204054547.png)

对应如下：

- start：启动项目
- build：打包  
- test：测试项目
- eject：react 把所有 webpack 的配置文件都给隐藏了，如果使用这个命令，将会把所有 webpack 相关的配置文件都暴露出来 

![image-20230504211020440](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504211020440.png)

文件目录：

- public：静态资源文件夹
  - favicon.icon 网站页签图标
  - index.html 主页面
  - logo192.png logo图
  - logo512.png logo图
  - manifest.json 应用加壳的配置文件
  - robots.txt 爬虫协议文件
- src：源码文件夹
  - App.css App组件的样式
  - App.js App组件
  - App.test.js 用于给 App做测试
  - index.css 入口文件
  - logo.svg logo图
  - reportWebVitals.js 页面性能分析文件（需要 web-bitals 库的支持）
  - setupTests.js 组件单元测试的文件（需要 jest-dom 库的支持） 

一般来说，我们都这样写，然后在需要使用此组件的父组件中导入

![image-20230504214956321](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504214956321.png)

当然，如同Vue，我们也可以这样写，然后只需要导入`import Hello from './components/Hello'`即可

![image-20230504215214618](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504215214618.png)

由于使用 .js 结尾很难分清，哪个是组件文件，哪个是 js 文件，那么我们都可以把组件文件以 jsx 结尾，这样的话，一看到 jsx 结尾的文件，就知道是组件了

![image-20230504220005872](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504220005872.png)

## 样式的模块化

如果多个模块都叫一个类名，那么类名之间一定会产生冲突，有两种解决方法：

1. 使用 less：

   ```less
   .Hello{
   	.title {
   		color:red
   	}
   }
   .Welcome {
   	.title {
   		color:orange
   	}
   }
   ```

2. 使用样式模块化：把 css 样式的后缀改成 .module.css ，然后就可以这样进行导入：`import Hello from 'index.module.css'`，然后使用样式：`class={Hello.title}`

   <div style="display:flex">
       <div style='flex:2'>
           <img src='C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504221003634.png'/>
       </div>
    	<div>
       <img src='C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230504221015320.png' style='flex:1'/>        
       </div>

## 创建React快捷键

1. rcc+tab键：用ES6模块系统创建一个React组件类

   ```jsx
   import React, {Component} from 'react';
   
   class Test extends Component {
       render() {
           return (
               <div>
                   
               </div>
           );
       }
   }
   
   export default Test;
   ```

2. rccp+tab键：创建一个带有PropTypes和ES6模块系统的React组件类

   ```jsx
   import React, {Component} from 'react';
   import PropTypes from 'prop-types';
   
   class Test extends Component {
       render() {
           return (
               <div>
                   
               </div>
           );
       }
   }
   
   Test.propTypes = {};
   
   export default Test;
   ```

3. rcfc+tab键：创建一个带有PropTypes和所有生命周期方法以及ES6模块系统的React组件类

   ```jsx
   import React, {Component} from 'react';
   import PropTypes from 'prop-types';
   
   class Test extends Component {
       constructor(props) {
           super(props);
   
       }
   
       componentWillMount() {
   
       }
   
       componentDidMount() {
   
       }
   
       componentWillReceiveProps(nextProps) {
   
       }
   
       shouldComponentUpdate(nextProps, nextState) {
   
       }
   
       componentWillUpdate(nextProps, nextState) {
   
       }
   
       componentDidUpdate(prevProps, prevState) {
   
       }
   
       componentWillUnmount() {
   
       }
   
       render() {
           return (
               <div>
   
               </div>
           );
       }
   }
   
   Test.propTypes = {};
   
   export default Test;
   ```

4. rcjc+tab键：用ES6模块系统创建一个React组件类（无导出）

   ```jsx
   class Test extends Component {
       render() {
           return (
               <div>
                   
               </div>
           );
       }
   }
   ```

5. **rdp+tab键**：快速生成defaultProps

   ```javascript
   .defaultProps = {
       
   };
   ```
   
6. **rpc+tab键**：用PropTypes和ES6 moudle系统创建一个React纯组件类
   
   ```javascript
   import React, {PureComponent} from 'react';
   import PropTypes from 'prop-types';
   
   class Test extends PureComponent {
       render() {
           return (
               <div>
                   
               </div>
           );
       }
   }
   
   Test.propTypes = {};
   export default Test;
   ```
   
7. **rrc+tab键**：创建一个连接到redux的React组件类

   ```jsx
   import React, {Component} from 'react';
   import {connect} from 'react-redux';
   
   function mapStateToProps(state) {
       return {};
   }
   
   class Test extends Component {
       render() {
           return (
               <div>
                   
               </div>
           );
       }
   }
   
   export default connect(
       mapStateToProps,
   )(Test);
   ```

8. **rrdc+tab键**：创建一个通过dispatch连接到redux的React组件类
   
   ```javascript
   import React, {Component} from 'react';
   import {connect} from 'react-redux';
   
   function mapStateToProps(state) {
       return {};
   }
   
   function mapDispatchToProps(dispatch) {
       return {};
   }
   
   class Test extends Component {
       render() {
           return (
               <div>
                   
               </div>
           );
       }
   }
   
   export default connect(
       mapStateToProps,
   )(Test);
   ```
   
9. **rsc+tab键**：创建没有PropTypes和ES6模块系统的无状态React组件
   
   ```javascript
   import React from 'react';
   
   const Test = () => {
       return (
           <div>
               
           </div>
       );
   };
   
   export default Test;
   复制代码
   ```
   
10. **rscp+tab键**：创建有PropTypes和ES6模块系统的无状态React组件

    ```jsx
    import React from 'react';
    import PropTypes from 'prop-types';
    
    const Test = props => {
        return (
            <div>
    
            </div>
        );
    };
    
    Test.propTypes = {
        
    };
    
    export default Test;
    ```

11. **rsf+tab键**：以命名函数的形式创建无状态的React组件，不使用PropTypes

    ```jsx
    import React from 'react';
    
    function Test(props) {
        return (
            <div></div>
        );
    }
    
    export default Test;
    ```

    

12. **rsfp+tab键**：使用PropTypes将无状态的React组件作为命名函数创建

    ```jsx
    import React from 'react';
    import PropTypes from 'prop-types';
    
    Test.propTypes = {
        
    };
    
    function Test(props) {
        return (
            <div></div>
        );
    }
    
    export default Test;
    ```

13. **rsi+tab键**：创建无状态的React组件，不使用PropTypes和ES6模块系统，但使用隐式返回和道具

    ```jsx
    import React from 'react';
    
    const Test = (props) => (
        
    );
    
    export default Test;
    ```

14. **rwwd+tab键**：在没有导入的情况下，在ES6模块系统中创建一个有构造函数、空状态、proptypes和导出的React组件类。(主要用于React时，proptype由webpack提供插件提供)

    ```jsx
    class Test extends React.Component {
        constructor(props) {
            super(props);
    
            this.state = {};
    
        }
    
        render() {
            return (
                <div>
                    
                </div>
            );
        }
    }
    
    Test.propTypes = {};
    
    export default Test;
    ```

## React Ajax

在 react 中使用 Ajax，这里推荐使用 axios，需要安装`axios`

### react 解决跨域

![原理图](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230508114257657.png)

>注意：
>
>1. 跨域是 ajax 引擎同源策略的影响，开启代理服务器，开在3000这样就不算是跨域，代理服务器也是开在和客户端同一接口
>2. 由于代理服务器不具有 ajax 引擎，所以不算是跨域
>3. 阶段：client 发送给 代理服务器，发现代理服务器也是 3000，不算跨域，代理服务器将请求转发给服务器，服务器返回响应，代理服务器拿到响应，发给 client，client 一看，也是3000端口回来的，不管跨域，解决跨域

代理服务器：

1. 最简单的方式，在`package.json`中操作，添加字段：`"proxy":"需要代理的服务器地址"`，例：`"proxy":"http://localhost:5000/"`，然后在请求中使用`axios.get("http://localhost:3000/cars")`，这样就会把这个请求转发到5000端口

   但是：这样只能配置一个，无法配置多个代理，且优先请求3000，3000没有请求5000

2. 使用 react 提供的代理服务器，但这里不像是 vue 一样，在 vue.config.json 中配置，需要有如下步骤：

   - 在 src 目录下（App.js同级目录）新建 setupProxy.js ，这里配置成功后，react 会将此配置自动添加到 webpack 的配置中

   - 在文件中写入如下代码

     ```js
     const { createProxyMiddleware } = require("http-proxy-middleware")
     module.exports = function (app) {
         app.use(
             createProxyMiddleware('/api1',{
                 // 请求转发地址
                 target:"http://localhost:5000",
                 // 控制服务器收到的请求头中 Host 字段的值
                 // 如果不加这个，服务器收到的 Host 是 localhost:3000
                 // 加上这个后，服务器收到的是 localhost:5000
                 changeOrigin:true,
                 // 重写请求路径，把所有的 /api1 重写成空值
                 // 不加的话请求的地址：http://localhost:5000/api1/students
                 pathRewrite:{'/api1':''}
             }),
             createProxyMiddleware('/api2',{
                 target:"http://localhost:5001",
                 changeOrigin:true,
                 pathRewrite:{'/api2':''}
             })
         )
     }
     ```

   - 在请求中如下书写

     ```js
     axios.get('http://localhost:3000/api1/students/').then(res=>{
         console.log(res)
     })
     axios.get('http://localhost:3000/api2/cars').then(res=>{
         console.log(res)
     })
     ```

### 使用fetch发送ajax请求

优点：原生的请求，不用安装库

缺点：使用率很低，兼容性有问题

fetch 相对于原生 xhr 相比，其属于 Promise 风格，用法：

```js
fetch('http://locahost:5000/user').then(
    // 这里第一层回调只是连接服务器，不管成功与否都会执行这个
    // 哪怕这次请求有错误，触发 error 那么也会触发这个 response，因为哪怕是回复一个 404，那也是连接成功了
	response => {
        console.log('联系服务器成功了')
        // 这个 response 只是连接服务器成功，真正的数据在 response.json()中，同时这个也是一个Promise
        return response.json()
    },
    // 这里是连接服务器失败，如果不是200开头的错误码将会显示这个
    error => {
        console.log('联系服务器失败了')
        // 这样写，就可以让 response 不再调用了
        return new Promise(()=>{})
    }
).then(
    // 对于 Promise，只要前边有返回，那么下一个 then 拿到的数据，就是上一个 then 的返回值
    // 所以这里的这个 response 是上一个 response.json() 的返回值
    // 也就是这次请求的数据
	response => {
        console.log(response)
    },
    error => {
        
    }
)
```

精简写法：

```js
const response = await fetch('http://locahost:5000/user')
const data = await response.json() 
```

# 路由

react-router-dom

原理：

靠浏览器的 BOM history ，也就是历史记录

这里想要实现 history 可以使用 history.js 这个库（原生的语法写着不方便）

`react-router`有三个版本：

1. 针对 web 的
2. 针对 native 做原生开发的
3. any 通用的，用着不是很舒服

这里我们使用的是第一个针对web的，使用的是 `react-router-dom`

## 使用

这里我们想要实现路由跳转，类似 Vue 中 router-link ，这里我们使用`<Link to='path'>跳转</Link>`，这样使用，同时我们需要引入`import {Link} from 'react-router-dom'`，这时，将会产生如下错误：

![image-20230515214746904](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230515214746904.png)

看错误信息，让我们再 Link 外侧包裹一个 Router，包裹上之后，产生错误：

![image-20230515214851414](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230515214851414.png)

这是因为，Router 这个标签分为 BrowserRouter 和 HashRouter，并不是直接引用 Router

```js
<BrowserRouter>
    <Link className="list-group-item" to='/about'>About</Link>
	<Link className="list-group-item" to='/home'>Home</Link>
</BrowserRouter>
<HashRouter>
    <Link className="list-group-item" to='/about'>About</Link>
	<Link className="list-group-item" to='/home'>Home</Link>
</HashRouter>
```

问题解决，这两种的区别就在于 Hash 的有 # ，Browser 的好看，这样可以完成路由跳转了，但是想要改变内容还需要配置：

```jsx
<div className="col-xs-6">
    <Route path='/about' component={About}></Route>
    <Route path='/home' component={Home}></Route>
</div>
```

这样就把路由和组件都关联起来了，但是这样还会产生之前要求包裹  Router 的错误；

这里直接来一个一劳永逸的方法：

在 index.js 中：

```jsx
import {BrowserRouter} from "react-router-dom";
root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

路由组件一般放在 pages 文件夹中，一般组件放在 components 中

## 路由组件

路由组件会默认收到三个	 props ，留下最重要的如下：

```json
{
    "history": {
        go:f go(n),
    	goBack:f goBack(),
		goForward: f goForward(),
		push: f push(path, state),
		replace: f replace(path, state)
    },
    "location": {
        "pathname": "/about",
        "search": "",
        "hash": ""
    },
    "match": {
        "path": "/about",
        "url": "/about",
        "params": {}
    }
}
```

可以使用 navLink 来为当前路由添加一个样式，默认添加一个类名为 active 的类，当然也可以指定：

```jsx
<NavLink activeClassName='demo' className="list-group-item" to='/about'>About</NavLink>
```

这样，当点击的时候，就会将这个标签添加一个 demo 的类名

**二次封装 NavLink **

父组件：

```jsx
<MyNavLink to='/home'>Home</MyNavLink>
<MyNavLink to='/about'>About</MyNavLink>
```

这里编写一个 MyNavLink，其中编写代码：

```jsx
class MyNavLink extends Component {
    render() {
        return (
            <NavLink className="list-group-item" to={this.props.to}>{this.props.children}</NavLink>
        );
    }
}
```

因为在  MyNavLink 标签中编写的代码，将会自动添加到子组件的 props 中：

```json
{
    "to": "/about",
    "children": "About"
}
```

所以完全可以这么写：

```jsx
<NavLink className="list-group-item" {...this.props}/>
```

## Switch

如果是两个路由路径相同，但是对应组件不同，那么将会将两个组件都展示出来，这时我们需要使用Switch，这时候匹配到一个路径之后就不会再继续向下匹配。

```jsx
import { Route, Switch} from 'react-router-dom'
<Switch>
    <Route path='/about' component={About}></Route>
    <Route path='/home' component={Home}></Route>
</Switch>
```

## 多级路由样式丢失

如果路由跳转写了这种：`<Route path='/csb/about' component={About}></Route>`

跳转成功后刷新，这样就会发现样式都丢失了，这里我们的解决办法就是，在 index.html 中把引入修改：

```html
<!--引入方式不要这样引入-->
<link rel="stylesheet" href="./css/bootstrap.css">
<!--这样引入-->
<link rel="stylesheet" href="/css/bootstrap.css">
<!--或者这样引入-->
<link rel="stylesheet" href="%PUBLIC_URL%/css/bootstrap.css">
```

还有一种改变方法，不要使用 BrowserRouter ，使用 HashRouter，index.js:

一般不这样弄，因为这样太丑了。

```js
root.render(
  <HashRouter>
    <App />
  </HashRouter>
);
```

## 二级路由

```jsx
<div>
    <h2>Home组件内容</h2>
    <div>
        <ul className="nav nav-tabs">
            <li>
                <MyNavLink to='/home/news'>News</MyNavLink>
            </li>
            <li>
                <MyNavLink to='/home/message'>Message</MyNavLink>
            </li>
        </ul>
        <Switch>
            <Route path="/home/news" component={News}></Route>
            <Route path="/home/message" component={Message}></Route>
            <Redirect to='/home/news'></Redirect>
        </Switch>
    </div>
</div>
```

## 其他路由内容

精准匹配：输入的路径必须和匹配的路径完全一致，一点不一样都不行，一般我们不开启这个，除非非常需要，有时候无法匹配二级路由

```jsx
<Route path='/home' exact component={Home}></Route>
```

重定向：当前边都无法匹配时，跳转到哪个页面，一般写在路由匹配最下方

```jsx
import {Route, Switch, Redirect} from 'react-router-dom'
<Switch>
    <Route path='/about' component={About}></Route>
    <Route path='/home' exact component={Home}></Route>
    <Redirect to='/home'></Redirect>
</Switch>
```

## 路由传参

使用频率：params > search > state

### params参数

路径样式：`/home/detail/1`

路由跳转传递参数：

```jsx
<Link to={`/home/message/detail/${messageObj.id}`}>{messageObj.title}</Link>
```

接收路由参数：

```jsx
<Route path='/home/message/detail/:id'  component={Detail}></Route>
```

在 Detail 中使用：

```jsx
const {id} = this.props.match.params
```

### search参数

路径样式：`/home/detail?id=1`

传递参数：

```jsx
<Link to={`/home/message/detail?id=${messageObj.id}`}>{messageObj.title}</Link>
```

接收参数：

```jsx
this.props.location.search
```

但是这里的这个长这样：`search**: "?id=003"`

所以我们需要使用一个库给他变成 json，这个库已经内置了，直接引入`import qs from 'querystring'`，如果没有就安装，其方法：

- `qs.stringify(obj)`：把一个对象转换为 urlencoded 编码格式：key=value&&id=id

- `qs.parse(str)`:把 urlencoded 编码格式转换成对象形式。

  ```jsx
  search: "?id=002"
  转换成：
  {?id: '002'}
  ```

  一般这样写：

  ```jsx
  const {id} = qs.parse(this.props.location.search.slice(1))
  ```


### state参数 

传递参数：

```jsx
<Link to={{pathname: '/home/message/detail', state: {id: messageObj.id}}}>{messageObj.title}</Link>
```

接收参数：

```jsx
const {id} = this.props.location.state
```

刷新也可以保留住参数

## 跳转模式

### replace 模式

路由跳转不会留下痕迹

```jsx
<Link replace to='/home/message/detail'>{messageObj.title}</Link>
```

### push 模式

默认开启，会留下痕迹

## 编程式路由导航

携带 params 参数

```jsx
pushShow = (id) => {
    // eslint-disable-next-line no-restricted-globals
    this.props.history.push(`/home/message/detail/${id}`)
};
replaceShow = (id) => {
    // eslint-disable-next-line no-restricted-globals
    this.props.history.replace(`/home/message/detail/${id}`)
};
```

携带 search 参数

```jsx
pushShow = (id) => {
    // eslint-disable-next-line no-restricted-globals
    this.props.history.push(`/home/message/detail?id=${id}`)
};
replaceShow = (id) => {
    // eslint-disable-next-line no-restricted-globals
    this.props.history.replace(`/home/message/detail?id=${id}`)
};
```

携带 state 参数

`this.props.history.push(path, state)`

```jsx
pushShow = (id) => {
    // eslint-disable-next-line no-restricted-globals
    this.props.history.push(`/home/message/detail`,{id})
};
replaceShow = (id) => {
    // eslint-disable-next-line no-restricted-globals
    this.props.history.replace(`/home/message/detail`,{id})
};
```

同时，history 上也有一些其他方法

- `goBack`：回退
- `goForward`：前进
- `go`：传 index 为正代表前进几步，为负代表后退几步

## withRouter

引入 `withRouter`：`import {withRouter} from 'react-router-dom'`

可以加工一般组件，让一般组件具有路由组件所特有的api，返回值是一个新的组件

什么意思呢，就是如果是一个普通的一般组件，你想要实现路由跳转，是没有办法从props中提取到 history 的，但是使用 withRouter 后，就可以使用了

```jsx
import React, {Component} from 'react';
import {withRouter} from "react-router-dom";
class Header extends Component {
    render() {
        console.log(this.props)
        return (
            <div>
                <div className="col-xs-offset-2 col-xs-8">
                    <div className="page-header"><h2>React Router Demo</h2></div>
                </div>
            </div>
        );
    }
}

export default withRouter(Header);
```

这样：

Home 组件的 props 就有了路由的方法：

![image-20230523205403259](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230523205403259.png)

# 组件库-ANtd

官网：[按钮 Button - Ant Design](https://ant.design/components/button-cn)

安装`cnpm install antd`

引入组件：`import {Button} from "antd";`，

使用：`<Button type="primary">Primary Button</Button>`

> Antd4需要手动引入css，但是Antd5不需要自己引入
>
> 引入：import 'antd/dist/antd.css'

## 按需引入

[在 create-react-app 中使用 - Ant Design](https://3x.ant.design/docs/react/use-with-create-react-app-cn#高级配置)

## 自定义主题

[在 create-react-app 中使用 - Ant Design](https://3x.ant.design/docs/react/use-with-create-react-app-cn#自定义主题)

# Redux

相当于 Vuex ，是一个专门用于状态管理的 JS 库（不是 React 插件库），可以在 react、vue、angular都可以用，但是 react 中用的最多。

安装 redux:`cnpm install redux`

原则：能不用就不用，如果不用比较吃力才考虑使用

![image-20230526135150027](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230526135150027.png)

## 三个核心概念

### action

1. 动作的对象 ，可以是

   - Object 对象：同步 action
   - Function 函数：异步 action

2. 两个属性

   - type：标识属性，值为字符串，唯一，必要属性
   - data：数据属性，值类型任意，可选属性

3. 例子：

   ```js
   {
       type:'ADD_STUDENT',
       data:{
           name:'tom',
          	age:18 
       }
   }
   ```

   

### reducer

提交申请，修改状态，处理数据

```js
/*
* 1. 该文件是用于创建一个为Count组件服务的reducer，reducer的本质就是一个函数
* 2. reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)
* */
const initState = 0
function countReducer(preState = initState, action) {
    // 从action对象中获取：type、data
    const { type, data } = action
    // 根据type决定如何加工数据
    switch (type) {
        // 如果是加
        case 'increment':
            return preState + data
        case 'decrement':
            return preState - data
        default:
            return preState
    }
}
export default countReducer
```



### store

保存数据的仓库

```js
// 引入createStore，专门用于创建redux中最为核心的store对象
import {legacy_createStore as createStore} from 'redux'
// 引入为Count组件服务的reducer
import countReducer from './count_reducer';
const store =  createStore(countReducer);
export default store
```

## 流程

1. 对于`redux`，要有一个文件夹专门用于编写 redux 相关文件，新建 redux 文件夹

2. 在这个文件夹下新建 store.js 文件，并编写如下代码

   - 引入createStore，专门用于创建redux中最为核心的store对象，`import {legacy_createStore as createStore} from 'redux'`
   - 引入 Reducer ，`import countReducer from './count_reducer'`，应当每个需要使用 redux 的组件对应一个 Reducer
   - 导出内容`const store =  createStore(countReducer); export default store`

3. 编写 count_reducer.js 文件，编写如下函数：

   ```js
   /*
   * 1. 该文件是用于创建一个为Count组件服务的reducer，reducer的本质就是一个函数
   * 2. reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)
   * */
   const initState = 0
   function countReducer(preState = initState, action) {
       // 从action对象中获取：type、data
       const { type, data } = action
   
       // 根据type决定如何加工数据
       switch (type) {
           // 如果是加
           case 'increment':
               console.log(preState,data)
               return preState + data
           case 'decrement':
               return preState - data
           default:
               return preState
       }
   }
   ```

   - 此函数接收两个参数，
     - 前一次状态，第一次初始化时会触发这个事件，但是这时候是没有这个 preState 的，所以如果没有就将其初始化为0
     - 对应的行为，行为中有两个参数，其为用户传入，分别对应 type 也就是行为类型，以及 data 也就是用户传入数据，根据行为类型可以对数据进行不同的操作，使其添加到之前的 preState 之上。
   - 导出该函数

4. 在需要运行的修改状态的地方，使用：`store.dispatch({type: 'increment', data: value * 1});`

   这将会触发 countReducer 事件，并自动将之前的状态传入，而括号中写的就是需要进行的动作

5. 现在状态已经可以更新了，但是看到页面上并没有对应数据增加，这是因为 react 只有在使用 setState 事件时，才会触发 render 将此组件重新渲染，我们现在需要让这个组件重新渲染，可以使用

   - `store.subscribe(callback)`：在 componentDidMount 开启监测，就是只要 store 中的数据更新，就会触发这个函数，在这里写`this.setState({});`由于这个组件并没有自己的 state，所以就调用一下 setState，就会触发render ，导致这个页面更新

   - 也可以在 `index.js` 中

     ```js
     import store from "./redux/store";
     createRoot(document.getElementById("root")).render(<App/>);
     store.subscribe(() => {
         createRoot(document.getElementById("root")).render(<App/>);
     });
     ```

     但是这样会报错`You are calling ReactDOMClient.createRoot() on a container that has already been passed to createRoot() before. Instead, call root.render() on the existing root instead if you want to update it.`

     修改这种写法，报错解除：

     ```js
     import store from "./redux/store";
     const root = createRoot(document.getElementById("root"));
     root.render(<App/>);
     store.subscribe(() => {
         root.render(<App/>)
     });
     ```


## redux完整版

1. 新建 redux 文件夹，创建四个文件

2. constant.js 文件

   专门用于定义 action 对象中 type 类型的常量值，目的就是：便于管理并且防止程序员写错单词

   ```js
   export const INCREMENT = 'increment'  // 加
   export const DECREMENT = 'decrement'  // 减
   ```

   就导出两个常量

3. count_action.js 文件

   专门为 Count 组件生成 action 对象

   ```js
   import { INCREMENT, DECREMENT } from './constant'
   export const createIncrementAction = (data) => ({ type: INCREMENT, data })
   export const createDecrementAction = (data) => ({ type: DECREMENT, data })
   ```

4. count_reducer.js 文件

   - 该文件是用于创建一个为Count组件服务的reducer，reducer的本质就是一个函数
   - reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)

   ```js
   import { INCREMENT, DECREMENT } from './constant'
   const initState = 0
   function countReducer(preState = initState, action) {
       // 从action对象中获取：type、data
       const { type, data } = action
   
       // 根据type决定如何加工数据
       switch (type) {
           // 如果是加
           case INCREMENT:
               return preState + data
           case DECREMENT:
               return preState - data
           default:
               return preState
       }
   }
   export default countReducer
   ```

5. store.js 文件

   该文件是 redux 的核心文件，用于创建 redux 最为核心的 store 对象

   ```js
   import {legacy_createStore as createStore} from 'redux'
   // 引入为Count组件服务的reducer
   import countReducer from './count_reducer';
   const store =  createStore(countReducer);
   export default store
   ```

6. 使用：获取状态
   ```js
   store.getState()
   ```

7. 派发 action，触发 reducer 事件

   ```js
   import store from "../../redux/store";
   import {createIncrementAction, createDecrementAction} from "../../redux/count_action";
   increment = () => {
       const {value} = this.selectNumber;
       store.dispatch(createIncrementAction(value * 1));
   }
   decrement = () => {
       const {value} = this.selectNumber;
       store.dispatch(createDecrementAction(value * 1));
   }
   ```

## 异步 action

在 action 中，可以是

- Object 对象：同步 action
- Function 函数：异步 action

如果是这个异步 action，那我们这样写，需要让 action 返回一个对象。

```js
export const createAsyncDecrementAction = (data,time) => {
    return (dispatch) => {
        setTimeout(() => {
            dispatch(createIncrementAction(data))
        }, time);
    }
}
```

但是呢，这样就报错了：

```bash
redux.js:275 Uncaught Error: Actions must be plain objects. Instead, the actual type was: 'function'. You may need to add middleware to your store setup to handle dispatching other values, such as 'redux-thunk' to handle dispatching functions. See https://redux.js.org/tutorials/fundamentals/part-4-store#middleware and https://redux.js.org/tutorials/fundamentals/part-6-async-logic#using-the-redux-thunk-middleware for examples.
```

意思是 store 只认识 Object 这种类型的 action，现在传的是一个 Function，就不行，这时我们可以安装`redux-thunk`中间件解决这个问题：`cnpm install redux-thunk`

然后安装这个中间件：

```js
// 引入createStore，专门用于创建redux中最为核心的store对象
import {legacy_createStore as createStore,applyMiddleware} from 'redux'
// 引入为Count组件服务的reducer
import countReducer from './count_reducer';
// 引入 redux-thunk 用于支持异步action
import thunk from 'redux-thunk'
const store =  createStore(countReducer,applyMiddleware(thunk));
export default store
```

然后就已经没问题了

## react-redux

![image-20230530111225284](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20230530111225284.png)

这个是 Facebook 出品的专门为 react 的 redux 库，
