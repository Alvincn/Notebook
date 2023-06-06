# 初识webpack

1. 新建文件夹，运行`npm init -y`初始化环境

2. 在文件夹中打开终端，运行`cnpm i webpack webpack-cli`安装webpack依赖

3. 新建main.js文件随便写点代码

4. 终端中运行`npx webpack ./main.js --mode=development`，开发模式打包，打包后文件目录将会生成dist文件夹，里边有一个main文件夹就是打包完成的，长这样

   ![image-20221113170115920](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20221113170115920.png)

5. 如果是生产模式下，使用命令`npx webpack ./main.js --mode=development`，打包文件长这样

   ![image-20221113170211377](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20221113170211377.png)

# 配置webpack

## 基本属性

webpack有五种基本属性，一般在webpack.config.js文件中编写：

1. 文件入口entry
2. 输出 output
3. 加载器 module
4. 插件 plugins
5. 模式 mode

webpack有两种打包模式：

1. development 开发环境打包，不算太压缩
2. production 生产模式打包，代码全部压缩 

代码：

```js
const path = require('path');
module.exports = {
  // 入口
  entry: './main.js',
  // 输出
  output: {
    //  所有文件的输出路径
    path: path.resolve(__dirname, 'dist'),
    // 入口文件打包输出的文件名(dist/static/main.js)
    filename: 'static/main.js',
    // 自动清空上一次打包的内容
    // 原理：在打包前，将path整个目录内容清空，在进行打包
    cl
  },
  // 加载器
  module: {
    rules: [
      // loader的配置
    ],
  },
  // 插件
  plugins: [
    // plugin的配置
  ],
  // 模式
  mode: 'development',
};
```

之后只需要执行`npx webpack`即可。

## 两种模式

### 开发模式

顾名思义就是我们开发代码时的模式，主要就是让代码在浏览器端能运行，同时保证代码的质量规范。

主要帮我们做两件事：

1. 编译代码，使浏览器能识别并运行

   开发时我们有样式资源、字体图标、图片资源、html资源等，webpack默认都不能处理这些资源，所以我们要加载配置来编译这些资源。

2. 代码质量检查，树立代码规范

   提前检查代码的一些隐患，让代码运行时能更加健壮

   提前检查代码的规范和格式，统一团队编码风格，让代码更优雅美观

# Loader

## 样式资源

### css资源

原生的webpack是无法处理css样式的，如果在入口文件引入css样式`import './style.css'`，这时候进行打包，会出现错误。这是因为原生的webpack并不能处理样式，需要我们手动的添加loader。

1. 安装`css-loader`

   `npm install --save-dev css-loader`

2. 在文件入口 main.js 中引入css样式

   `import './style.css'`

3. 在 webpack.config.js 中配置

   ```js
   module.exports = {
       ...
       module:{
           rules: [
               // loader 的配置
               {
                   // 这是一个正则表达式，代表着要匹配什么样的文件，如下匹配以.css结束的文件
                   test:/\.css$/,
                   use: {
                       // 执行顺序，从右到左，从下到上
                       // 将 js 中 css 通过创建 style 标签添加 html 文件中生效
   					"style-loader",
                       // 将 css 资源编译成 commonjs 的模块到 js 中
                       "css-loader"
                   }
               }
           ]
       }
       ...
   }
   ```

4. 执行`npx webpack`

5. 这时其实运行的话会报错，因为 style-loader 这个包我们没有下载，还需要下载这个包

   `cnpm install style-loader`

> > 注
>
> 这是 webpack 文档的一个通病，如果说有报错说包未找到，只需要下载这个包即可

### less资源

同样的，webpack无法识别less，还是要下载loader

1. 下载处理 less 资源的 loader 

   `cnpm install less less-loader`

2. 配置

   ```js
   module.exports = {
       ...
       module:{
           rules: [
               // loader 的配置
               {
                   // 这是一个正则表达式，代表着要匹配什么样的文件，如下匹配以.less结束的文件
                   test:/\.less$/,
                   use: {
                       // 将 js 中 css 通过创建 style 标签添加 html 文件中生效
   					"style-loader",
                       // 将 css 资源编译成 commonjs 的模块到 js 中
                       "css-loader",
                       // 将 less 文件转换为 css 文件
                       "less-loader"
                   }
               }
           ]
       }
       ...
   }
   ```

   > > 注
   >
   > 如果在rules中test下边一行为loader:'xxx'，这种只能配置一个loader，而use:{}这种可以配置多个
   >
   > 因为loader的执行顺序是从下到上，所以上方的步骤为：
   >
   > 1. less-loader 将 less 文件转换成 css文件
   > 2. css-loader 将 css 资源编译成 js 语法添加到 js 文件中
   > 3. 将 js 中的 css 通过创建 style 标签的方式添加到 html 文件头部
   >
   > 上方顺序不可改变，改变 loader 位置会导致报错

### sass资源

步骤与上文一样，唯一不太一样的位置：

```js
module.exports = {
    ...
    module:{
        rules: [
            // loader 的配置
            {
                // 这是一个正则表达式，代表着要匹配什么样的文件，如下匹配以.sass/scss结束的文件
                test:/\.s[ac]ss$/,
                ...
            }
        ]
    }
    ...
}
```

## 文件资源

### 图片资源

对于图片资源，我们一般会将体积较小的图片转换为base64格式，相当于字符串格式进行打包，不需要下载loader。

需要对文件做出一些配置，配置如下： 

```js
rules:[
    ... 
    {
        // 匹配所有后缀为png,jpeg,jpg,gif,webp,svg格式的
        test:/\.(png|jpe?g|gif|webp|svg)$/,
        type:"asset",
        parser: {
            // 小于10kb的图片转base64
            // 优点：减少请求数量
            // 缺点：体积会大一些
            dataUrlCondition: {
				maxSize:10 * 1024, // 10kb
            }
        },
        generator: {
            // 输出图片名称（路径:static/images/）
            // hash 就是分配给文件的名字 
            // hash:10就是hash值只取10位（也就是文件名最多10位）
            filename:"static/images/[hash:10][ext][query]"
        }
    }
    ...
]
```

> > 注
>
> 体积小于4kb的图片会转换成base64格式，而base64格式会内嵌到js中。
>
> 如果有三张图片，第一张大小100kb，第二张大小3.2kb，第三张10kb
>
> 那么在打包之后，将会把大小为3.2kb的这张图片转换为base64内嵌到js中，所以打包之后的dist文件夹中只有两张图片，这就是减少请求次数。

