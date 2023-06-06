# 介绍

**项目：**Mongo Punding

**作者：**Alvin

**开发日期**：2022/3/31

**项目搁置**：2022/4/5

**重新开始时间**：待定

**结束时间**：待定

**技术栈**：Node.js 、Vue3 、 Mysql 、 Echarts

**介绍**：这是一个用于情侣的app，可以在此app上记日记，记录每天的心情，选择是否共享给伴侣，可以为对方写一段话温暖他的一整天，可以记录她的经期，守护她的每一天，不开心也可以通过一句话表达出来。

# 开发日记

## 初始化

### 初始化项目

1. 首先创建`MangoPunding`文件夹；

2. 运行`npm init`初始化项目，（注：终端命令：`dir`显示所有文件夹、`mkdir`创建文件夹、`code .`在vscode中打开此文件夹）

3. 创建`app.js`；

4. 在终端上输入命令:`npm install express`安装`express`依赖，安装`cors`使项目支持跨域请求；

5. 在`package.json`文件中`scripts`下定义命令：

![image-20220331095243523](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220331095243523.png)

6. 然后可以使用`npm start`使用`nodemon`启动`app.js`(nodemon 当文件发生改变自动重启服务）

- 首先导入依赖cors、express；

- 实例化 app 对象：`const app = express()`

- 使用 cros 中间件：`app.use(cors)`

- 开启服务监听 3100 端口；

代码：

```js
const express = require('express');
const app = express();
// 导入 cors 使项目支持跨域请求
const cors = require('cors');
// 使用中间件
app.use(cors());

// 开启服务，监听 3100 端口
app.listen(3100, (err, results) => {
    if (err) return console.log(err.message);
    console.log('Server is running at http://127.0.0.1:3100');
});
```

### 初始化路由模块

1. 先创建`router`文件夹，在其中创建`user.js`用来储存所有路由模块；

- 在`/router/user`中导入`express`

- 创建`express.Router()`实例对象；

- 写路由`router.post('/reguser',(req,res)=>{})`;

- 导出路由`module.export = router`

2. 在`app.js`中导入`/router/user`：`app.use('/api',router)`;

测试接口：测试地址为：http://127.0.0.1:3100/api/reguser

- 注：测试时一定要添加`/api`。

代码：`router/user.js`

```js
const express = require('express');
// 创建路由对象
const router = express.Router();

// 注册用户
router.post('/reguser', (req, res) => {
    res.send('reguser ok');
});

// 登录
router.post('/login', (req, res) => {
    res.send('login ok');
});

// 导出路由
module.exports = router;
```

`app.js`

```js
// 导入路由
const router = require('./router/user');

// 使用中间件
app.use(cors());
// 路由
app.use('/api', router);
```

---

1. 创建`router_handle`文件夹创建`user.js`储存路由处理函数；

- 导出两个模块`regUser`和`login`；

- 在`/router/user.js`中导入；

注：在此遇到一个问题：`Error: Cannot find module 'object-assign'`，安装`object-assign`模块即可；

代码：`/login_handle/user.js`

```js
exports.regUser = (req, res) => {
    res.send('reguser ok');
};
exports.login = (req, res) => {
    res.send('login ok ');
};
```

`/router/user.js`

```js
const express = require('express');
// 创建路由对象
const router = express.Router();
// 导入路由处理函数
const userHandle = require('../router_handle/user');
// 注册用户
router.post('/reguser', userHandle.regUser);

// 登录
router.post('/login', userHandle.login);

// 导出路由
module.exports = router;
```

## MySQL模块

1. 首先安装依赖：`cnpm i mysql`；

2. 在根目录下创建`db`文件夹，并在其中创建`index.js`文件；在`db/index.js`中先导入`MySQL`；
- 创建连接：`const db = mysql.createPool({})`，传入一个对象包含`host,user,password,datebase`；
- 导出这个模块：`module.exports = db`

`注`：

- 这里一定要用变量接收，并导出，否则会报错；
- `POST`请求的参数都保存在`res.body`中；
- 部署到服务器上时需要将 host 改为 0.0.0.0

3. 在`router_handle`下`user.js`中导入 db 模块

- 导入模块：`const db = require('../db/index')`
- 定义sql语句：`const sqr = select * from user where username=?`
- 查找：`db.query(str, userinfo.username,(err,result)=>{})`，判断用户名是否已存在
- 用户名不存在则创建，使用`bcryptjs`加密密码`userinfo.password = bcrypt.hashSync(userinfo.password, 10);`

**代码：**`/db/index.js`

```js
const mysql = require('mysql');
// 创建 MySQL 连接
const db = mysql.createPool({
    host: '127.0.0.1',
    user: 'root',
    password: 'chen20020423',
    database: 'user',
});
// 导出 MySQL 模块
module.exports = db;
```

`/router_handle/user.js`

```js
// 导入 MySQL 模块
const db = require('../db/index');

// 导出这两个处理函数
// 注册处理函数
exports.regUser = (req, res) => {
    const userinfo = req.body;
    // 插入语句 这句话是将 ？ 中全部的东西都给插入进来 一般用于知道的
    const sql = 'insert into user set ?';
    // 查询语句 先查询用户名是否存在
    const str = 'select * from user where username=?';
    // 判断用户名和密码是否输入为空
    if (!userinfo.username || !userinfo.password)
        return res.cc('用户名或密码不能为空！');
    // 对密码进行加密 第二个参数为加密后的密码长度
    userinfo.password = bcrypt.hashSync(userinfo.password, 10);
    // 先搜索用户名是否存在
    db.query(str, userinfo.username, function (err, results) {
        // 执行 SQL 语句失败
        if (err) {
            return res.send({ status: 1, message: err.message });
        }
        // 用户名被占用
        if (results.length > 0) {
            return res.send({
                status: 1,
                message: '用户名被占用，请更换其他用户名！',
            });
        }
        // 说明用户名可以创建 开始创建
        db.query(
            sql,
            { username: userinfo.username, password: userinfo.password },
            function (err, results) {
                // 执行 SQL 语句失败
                if (err) return res.cc(err.message);
                // SQL 语句执行成功，但影响行数不为 1
                if (results.affectedRows !== 1) {
                    return res.cc('注册用户失败，请稍后再试！');
                }
                // 注册成功
                res.send({ status: 0, message: '注册成功！' });
            }
        );
    });
};
// 登录处理函数
exports.login = (req, res) => {
    const userinfo = req.body;

    // 判断用户输入的用户名和密码是否为空
    if (!userinfo.username || !userinfo.password) {
        return res.cc('用户名或密码不能为空！');
    }
    // 定义 SQL 语句
};
```

测试：http://127.0.0.1:3100/api/login

失败：

![image-20220331131625167](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220331131625167.png)

成功：

![image-20220331131707722](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220331131707722.png)

为了防止代码复杂，在`app.js`中定义一个发送错误的中间件；

```js
app.use(function (req, res, next) {
    // status = 0 为成功； status = 1 为失败； 默认将 status 的值设置为 1，方便处理失败的情况;
    res.cc = function (err, status = 1) {
        res.send({
            // 状态

            status,
            // 状态描述，判断 err 是 错误对象 还是 字符串
            message: err instanceof Error ? err.message : err,
        });
    };
    next();
});
```

注：这个中间件必须在`router`之前使用

### 优化表单数据

1. 安装`joi`：为表单中每个数据定义规则；
2. 安装`@escook/express.joi`：对表单自动验证
3. 新建`/schema/user.js`对用户信息进行验证，代码：

```js
const joi = require('joi');

// 验证字符串
/**
 * string() 值必须是字符串
 * alphanum() 值只能是包含 a-zA-Z0-9 的字符串
 * min(length) 最小长度
 * max(length) 最大长度
 * required() 值是必填项，不能为 undefined
 * pattern(正则表达式) 值必须符合正则表达式的规则
 */
// 用户名的验证规则
const username = joi.string().alphanum().min(3).max(10).required();
// 密码的验证规则
const password = joi
.string()
.pattern(/^[\S]{6,12}$/)
.required();
exports.reg_login_schema = {
    body: {
        username,
        password,
    },
};
```

修改`/router/user.js`代码：

```js
const express = require('express')
const router = express.Router()
// 导入用户路由处理函数模块
const userHandler = require('../router_handler/user')
// 1. 导入验证表单数据的中间件
const expressJoi = require('@escook/express-joi')
// 2. 导入需要的验证规则对象
const { reg_login_schema } = require('../schema/user')
// 注册新用户
// 3. 在注册新用户的路由中，声明局部中间件，对当前请求中携带的数据进行验证
// 3.1 数据验证通过后，会把这次请求流转给后面的路由处理函数
// 3.2 数据验证失败后，终止后续代码的执行，并抛出一个全局的 Error 错误，进入全局错误级别中间件
中进行处理
router.post('/reguser', expressJoi(reg_login_schema), userHandler.regUser)
// 登录
router.post('/login', userHandler.login)
module.exports = router
```

### 登录功能

1. 检测登录表单的数据是否合法：

   将`/router/user.js`中登录的代码：

   `router.post('/login',expressJoi(reg_login_schema),userHandle.login)`

2. 查询用户数据

   - 接受表单数据：`const userinfo = req.body
   -  定义spl语句：const sql = `select * from user where username=?`

3. 执行 SQL 语句 ，查询用户数据

代码：

```js
db.query(sql, userinfo.username, (err, results) => {
    if (err) return res.cc(err.message);
    // 查找到无用户 正常应该返回1
    if (results.length === 0) return res.cc('用户名不存在，请先登录！');
    
});
```

4. 使用`bcrypt.compareSync(输入密码，数据库密码)`自动比较，返回一个 Boolean 值

```js
db.query(sql, userinfo.username, (err, results) => {
    if (err) return res.cc(err.message);
    // 查找到无用户 正常应该返回1
    if (results.length === 0) return res.cc('用户名不存在，请先注册！');
    const compareResult = bcrypt.compareSync(
        userinfo.password,
        results[0].password
    );
    if (!compareResult) {
        return res.cc('密码错误');
    }
    res.send('登录成功');
});
```

代码：

```js
// 导入 MySQL 模块
const db = require('../db/index');
// 导入密码加密模块
const bcrypt = require('bcryptjs');

// 导出这两个处理函数
// 注册处理函数
exports.regUser = (req, res) => {
    const userinfo = req.body;
    // 插入语句 这句话是将 ？ 中全部的东西都给插入进来 一般用于知道的
    const sql = 'insert into user set ?';
    // 查询语句 先查询用户名是否存在
    const str = 'select * from user where username=?';
    // 判断用户名和密码是否输入为空
    if (!userinfo.username || !userinfo.password)
        return res.cc('用户名或密码不能为空！');
    // 对密码进行加密 第二个参数为加密后的密码长度
    userinfo.password = bcrypt.hashSync(userinfo.password, 10);
    // 先搜索用户名是否存在
    db.query(str, userinfo.username, function (err, results) {
        // 执行 SQL 语句失败
        if (err) {
            return res.send({ status: 1, message: err.message });
        }
        // 用户名被占用
        if (results.length > 0) {
            return res.send({
                status: 1,
                message: '用户名被占用，请更换其他用户名！',
            });
        }
        // 说明用户名可以创建 开始创建
        db.query(
            sql,
            { username: userinfo.username, password: userinfo.password },
            function (err, results) {
                // 执行 SQL 语句失败
                if (err) return res.cc(err.message);
                // SQL 语句执行成功，但影响行数不为 1
                if (results.affectedRows !== 1) {
                    return res.cc('注册用户失败，请稍后再试！');
                }
                // 注册成功
                res.send({ status: 0, message: '注册成功！' });
            }
        );
    });
};
// 登录处理函数
exports.login = (req, res) => {
    const userinfo = req.body;

    // 判断用户输入的用户名和密码是否为空
    if (!userinfo.username || !userinfo.password) {
        return res.cc('用户名或密码不能为空！');
    }
    // 定义 SQL 语句
    const sql = 'select * from user where username=?';
    db.query(sql, userinfo.username, (err, results) => {
        if (err) return res.cc(err.message);
        // 查找到无用户 正常应该返回1
        if (results.length === 0) return res.cc('用户名不存在，请先注册！');
        const compareResult = bcrypt.compareSync(
            userinfo.password,
            results[0].password
        );
        if (!compareResult) {
            return res.cc('密码错误');
        }
        res.send('登录成功');
    });
};
```

## Token字符串

### 生成Token字符串

1. 在登录模块中，先使用ES6的高级语法，从数据库得到的结果中剔除密码和头像还有用户权限：
   `const user = { ...results[0], password: '', user_pic: '', root: '' };`

2. 安装生成Token包的依赖：

   `cnpm i jsonwebtoken`

3. 在`/router_handle/user.js`模块头部区域导入`jsonwebtoken`包

4. 在根目录下创建`config.js`文件，共享和还原Token的`jwtSecretKey`

5. 在登录模块中导入config配置文件，生成 Token 字符串：

   `const tokenStr = jwt.sign(user, config.jwtSecretKey, { expiresIn: 100h })`

   Token 有效期为10小时

6. 将生成的 Token 相应给客户端

   为了方便客户端使用Token，在服务端直接拼接上`Bearer`前缀.

代码：

```js
const user = { ...results[0], password: '', user_pic: '', root: '' };
const tokenStr = jwt.sign(user, config.jwtSecretKey, {
    expiresIn: '100h',
});
res.send({
    status: 0,
    message: '登录成功！',
    // 为了方便客户端使用 Token，在服务器端直接拼接上 Bearer 的前缀
    token: 'Bearer ' + tokenStr,
});
```





### 配置解析Token的中间件

1. 安装解析Token的中间件

   `cnpm i express-jwt`

2. 在`app.js`中注册路由之前，配置解析Token中间件

   ```js
   const config = require('./config')
   app.use(
     expressJWT({
       secret: config.jwtSecretKey,
       //设置算法，这里必须这样写
       algorithms: ['HS256'],
       credentialsRequired: true,
       //路径有api的不用验证  
     }).unless({
       path: [/^\/api\//],
     })
   );
   ```

3. 在app错误级别中间件中，捕获Token认证失败的错误

   ```js
   app.use(function (err, req, res, next) {
       if (err instanceof joi.ValidationError) return res.cc(err);
       // 捕获身份认证失败的错误
       if (err.name === 'UnauthorizedError') return res.cc('身份认证失败！');
       // 未知错误...
       res.cc(err);
   });
   ```

## 个人中心

### 获取用户的基本信息

1. 初始化路由模块

   创建`/router/userinfo.js`路由模块。

   代码：

   ```js
   const express = require('express')
   const router = express.Router()
   const userinfo_handle = require('../router_handle/userinfo')
   // 读用户的基本信息
   router.get('/userinfo',userinfo_handle)
   // 导出路由
   module.exports = router
   ```

2. 在`app.js`中，导入此路由，并使用

   ```js
   const userinfoRouter = require('./router/userinfo')
   // 以 my 开头的接口 是有权限的接口 需要进行Token认证
   app.use('/my', userinfoRouter)
   ```

3. 在`router_handle`中，新建`userinfo_handle`

   ```js
   // 导入数据库模块
   const db = require('../db/index')
   // 导出此方法
   exports.getUserinfo = (req,res) => {
       // 定义查找用户的 SQL 语句
       const sql = 'select id,username,nickname,user_pic,status from user where id=?'
       // 根据用户id查找用户
       db.query(sql,req.user.id,(err,results)=>{
           if(err) return res.cc(err.message)
           res.send({ status: 0, message: '数据获取成功', data:result[0] })
       })
   }
   ```

   注：在 Token 中保存的字符串会自动挂载到 `req.user`上，可以直接通过`req.user`使用。





















# 项目接口

登录接口：`/api/login`  Methods: `POST`

注册接口：`/api/reguser`  Methods: `POST`

获取用户信息：`/my/userinfo`  Methods: `GET`

更新用户信息：`/my/userinfo`  Methods: `POST`

修改密码：`/my/userinfopwd`  Methods: `POST`

 
