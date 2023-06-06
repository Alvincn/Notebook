# JavaScript

## 1.作用域

1. var定义的变量，没有块的概念，可以跨块访问, 不能跨函数访问。
2. let定义的变量，只能在块作用域里访问，不能跨块访问，也不能跨函数访问。
3. const用来定义常量，使用时必须初始化(即必须赋值)，只能在块作用域里访问，而且不能修改。注意：const定义的对象以及数组是可以改变的。

const ：

```javascript
const PI = 3.141592653589793;
PI = 3.14;      // 会出错
PI = PI + 10;   // 也会出错
```

您可以创建 const 对象：
```javascript
// 您可以创建 const 对象：
const car = {type:"porsche", model:"911", color:"Black"};

// 您可以更改属性：
car.color = "White";

// 您可以添加属性：
car.owner = "Bill";
```

但是您无法重新为常量对象赋值：

```
const car = {type:"porsche", model:"911", color:"Black"};
car = {type:"Volvo", model:"XC60", color:"White"};    // ERROR
```
您可以创建常量数组：

```
// 您可以创建常量数组：
const cars = ["Audi", "BMW", "porsche"];

// 您可以更改元素：
cars[0] = "Honda";

// 您可以添加元素：
cars.push("Volvo");
```

但是您无法新为常量数组赋值：

```
const cars = ["Audi", "BMW", "porsche"];
cars = ["Honda", "Toyota", "Volvo"];    // ERROR
```

### 全局作用域

*全局*（在函数之外）声明的变量拥有*全局作用域*。

```javascript
var carName = "porsche";

// 此处的代码可以使用 carName

function myFunction() {
  // 此处的代码也可以使用 carName
}
```

### 函数作用域

*局部*（函数内）声明的变量拥有*函数作用域*。

```javascript
// 此处的代码不可以使用 carName

function myFunction() {
  var carName = "porsche";
  // code here CAN use carName
}

// 此处的代码不可以使用 carName
```

### JavaScript 块作用域

通过 var 关键词声明的变量没有块*作用域*。

在块 *{}* 内声明的变量可以从块之外进行访问。

```
{ 
  var x = 10; 
}
// 此处可以使用 x
```

let 和const 有块级作用域。

```javascript
{ 
  let x = 10;
}
// 此处不可以使用 x
```

```javascript
{ 
  const x = 10;
}
// 此处不可以使用 x
```

### 重新声明变量

使用 var 关键字重新声明变量会带来问题。

在块中重新声明变量也将重新声明块外的变 量：

```javascript
var x = 10;
// 此处 x 为 10
{ 
  var x = 6;
  // 此处 x 为 6
}
// 此处 x 为 6
```

使用 let 和 const ：

在块中声明的变量不会影响外部。

```javascript
var x = 10;
// 此处 x 为 10
{ 
  let x = 6;
  // 此处 x 为 6
}
// 此处 x 为 10
```

在同一作用域中，不可以使用 let 与 const 重新声明声明过的变量：

```javascript
var x = 10;       // 允许
let x = 6;       // 不允许

{
  var x = 10;   // 允许
  let x = 6;   // 不允许
}
```

```javascript
let x = 10;       // 允许
let x = 6;       // 不允许

{
  let x = 10;   // 允许
  let x = 6;   // 不允许
}
```

在相同的作用域，或在相同的块中，通过 var 重新声明一个 let 变量是不允许的：

```
let x = 10;       // 允许
var x = 6;       // 不允许

{
  let x = 10;   // 允许
  var x = 6;   // 不允许
}
```

在不同的作用域或块中，通过 let 重新声明变量是允许的：

```
let x = 6;       // 允许

{
  let x = 7;   // 允许
}

{
  let x = 8;   // 允许
}
```

### 循环作用域

在循环中使用 var 会出现问题：

并且循环体并不会进行循环，只会循环一次 i = 10。

```javascript
var i = 7;
for (var i = 0; i < 10; i++) {
  // 一些语句
}
// 此处，i 为 10
```

一般在循环体中使用 let ：

```javascript
let i = 7;
for (let i = 0; i < 10; i++) {
  // 一些语句
}
// 此处 i 为 7
```

### 全局作用域

在全局作用域中，在块外声明变量，var 与 let 差不多，它们都拥有*全局作用域*：

```javascript
var x = 10;       // 全局作用域
let y = 6;       // 全局作用域
```

var 还有一个特点，变量提升，这是 let 和 const 不具有的：

```javascript
// 在此处，您可以使用 carName
var carName;
```

```javascript
// 在此处，您不可以使用 carName
let carName;
```

## 2.数组常用方法

### 1. Array.forEach()

forEach() 方法为每个数组元素调用一次函数（回调函数）。

**实例**

```javascript
var txt = "";
var numbers = [45, 4, 9, 16, 25];
numbers.forEach(myFunction);

function myFunction(value, index, array) {
  txt = txt + value + "<br>"; 
}
```

**注释：**该函数接受 3 个参数：

- 项目值
- 项目索引
- 数组本身

上面的例子只用了 value 参数。这个例子可以重新写为：

**实例**

```javascript
var txt = "";
var numbers = [45, 4, 9, 16, 25];
numbers.forEach(myFunction);

function myFunction(value) {
  txt = txt + value + "<br>"; 
}
```

### 2. Array.map()

map() 方法通过对每个数组元素执行函数来创建新数组。

map() 方法不会对没有值的数组元素执行函数。

map() 方法不会更改原始数组。

这个例子将每个数组值乘以2：

**实例**

```javascript
var numbers1 = [45, 4, 9, 16, 25];
var numbers2 = numbers1.map(myFunction);

function myFunction(value, index, array) {
  return value * 2;
}
```

请注意，该函数有 3 个参数：

- 项目值
- 项目索引
- 数组本身

当回调函数仅使用 value 参数时，可以省略索引和数组参数：

**实例**

```javascript
var numbers1 = [45, 4, 9, 16, 25];
var numbers2 = numbers1.map(myFunction);

function myFunction(value) {
  return value * 2;
}
```

### 3. Array.filter()

filter() 方法创建一个包含通过测试的数组元素的新数组。

这个例子用值大于 18 的元素创建一个新数组：

**实例**

```javascript
var numbers = [45, 4, 9, 16, 25];
var over18 = numbers.filter(myFunction);

function myFunction(value, index, array) {
  return value > 18;
}
```

请注意此函数接受 3 个参数：

- 项目值
- 项目索引
- 数组本身

在上面的例子中，回调函数不使用 index 和 array 参数，因此可以省略它们：

**实例**

```javascript
var numbers = [45, 4, 9, 16, 25];
var over18 = numbers.filter(myFunction);

function myFunction(value) {
  return value > 18;
}
```

### 4. Array.reduce()

reduce() 方法在每个数组元素上运行函数，以生成（减少它）单个值。

reduce() 方法在数组中从左到右工作。另请参阅 reduceRight()。

reduce() 方法不会减少原始数组。          

这个例子确定数组中所有数字的总和：

**实例**

```javascript
var numbers1 = [45, 4, 9, 16, 25];
var sum = numbers1.reduce(myFunction);

function myFunction(total, value, index, array) {
  return total + value;
}
```

请注意此函数接受 4 个参数：

- 总数（初始值/先前返回的值）
- 项目值
- 项目索引
- 数组本身

上例并未使用 index 和 array 参数。可以将它改写为：

**实例**

```javascript
var numbers1 = [45, 4, 9, 16, 25];
var sum = numbers1.reduce(myFunction);

function myFunction(total, value) {
  return total + value;
}
```

## 3. 日期

```javascript
var d = new Date();
```

需要注意的是：

日期月份是从 0-11 所以获取月份的时候需要加1

### new Date(year,  ...)

new Date(year, month, ...) 用指定日期和时间创建新的日期对象。

7个数字分别指定年、月、日、小时、分钟、秒和毫秒（按此顺序）：

**实例**

```
var d = new Date(2018, 11, 24, 10, 33, 30, 0);
```



一位和两位数年份将被解释为 19xx 年：

**实例**

```
var d = new Date(99, 11, 24);
```

### 将日期存储为毫秒

JavaScript 将日期存储为自 1970 年 1 月 1 日 00:00:00 UTC（协调世界时）以来的毫秒数。

零时间是 1970 年 1 月 1 日 00:00:00 UTC。

现在的时间是：1970 年 1 月 1 日之后的 1554166879383 毫秒。

### Date(milliseconds)

new Date(milliseconds) 创建一个零时加毫秒的新日期对象：

**实例**

```
var d = new Date(0);
```

1970年 1 月 1 日加上100 000 000 000毫秒，大约是 1973 年 3 月 3 日：

**实例**

```
var d = new Date(100000000000);
```

### 日期获取方法

获取方法用于获取日期的某个部分（来自日期对象的信息）。下面是最常用的方法（以字母顺序排序）：

| 方法              | 描述                                 |
| :---------------- | :----------------------------------- |
| getDate()         | 以数值返回天（1-31）                 |
| getDay()          | 以数值获取周名（0-6）                |
| getFullYear()     | 获取四位的年（yyyy）                 |
| getHours()        | 获取小时（0-23）                     |
| getMilliseconds() | 获取毫秒（0-999）                    |
| getMinutes()      | 获取分（0-59）                       |
| getMonth()        | 获取月（0-11）                       |
| getSeconds()      | 获取秒（0-59）                       |
| getTime()         | 获取时间（从 1970 年 1 月 1 日至今） |

### UTC 日期方法

UTC 日期方法用于处理 UTC 日期（通用时区日期，Univeral Time Zone dates）：

| 方法                 | 描述                                    |
| :------------------- | :-------------------------------------- |
| getUTCDate()         | 等于 getDate()，但返回 UTC 日期         |
| getUTCDay()          | 等于 getDay()，但返回 UTC 日            |
| getUTCFullYear()     | 等于 getFullYear()，但返回 UTC 年       |
| getUTCHours()        | 等于 getHours()，但返回 UTC 小时        |
| getUTCMilliseconds() | 等于 getMilliseconds()，但返回 UTC 毫秒 |
| getUTCMinutes()      | 等于 getMinutes()，但返回 UTC 分        |
| getUTCMonth()        | 等于 getMonth()，但返回 UTC 月          |
| getUTCSeconds()      | 等于 getSeconds()，但返回 UTC 秒        |

## 4. Math 对象方法

| 方法             | 描述                                                     |
| :--------------- | :------------------------------------------------------- |
| abs(x)           | 返回 x 的绝对值                                          |
| acos(x)          | 返回 x 的反余弦值，以弧度计                              |
| asin(x)          | 返回 x 的反正弦值，以弧度计                              |
| atan(x)          | 以介于 -PI/2 与 PI/2 弧度之间的数值来返回 x 的反正切值。 |
| atan2(y,x)       | 返回从 x 轴到点 (x,y) 的角度                             |
| ceil(x)          | 对 x 进行上舍入                                          |
| cos(x)           | 返回 x 的余弦                                            |
| exp(x)           | 返回 Ex 的值                                             |
| floor(x)         | 对 x 进行下舍入                                          |
| log(x)           | 返回 x 的自然对数（底为e）                               |
| max(x,y,z,...,n) | 返回最高值                                               |
| min(x,y,z,...,n) | 返回最低值                                               |
| pow(x,y)         | 返回 x 的 y 次幂                                         |
| random()         | 返回 0 ~ 1 之间的随机数                                  |
| round(x)         | 把 x 四舍五入为最接近的整数                              |
| sin(x)           | 返回 x（x 以角度计）的正弦                               |
| sqrt(x)          | 返回 x 的平方根                                          |
| tan(x)           | 返回角的正切                                             |

### JavaScript 随机整数

Math.random() 与 Math.floor() 一起使用用于返回随机整数。

**实例**

```javascript
Math.floor(Math.random() * 10);		// 返回 0 至 9 之间的数		
```

## 5.for 循环

### 1. for...in...

for in 遍历返回的是索引值。更适合遍历对象。

JavaScript for in 语句循环遍历对象的属性：

**语法**

```javascript
for (key in object) {
  // code block to be executed
}
```

**实例**

```javascript
const person = {fname:"John", lname:"Doe", age:25};

let text = "";
for (let x in person) {
  text += person[x];
}
```

### 2.for...of...

for of 返回的是元素值，更适合遍历数组。

JavaScript for of 语句循环遍历可迭代对象的值。

它允许您循环遍历可迭代的数据结构，例如数组、字符串、映射、节点列表等：

**语法**

```javascript
for (variable of iterable) {
  // code block to be executed
}
```

**实例**

```javascript
const cars = ["BMW", "Volvo", "Mini"];

let text = "";
for (let x of cars) {
  text += x;
}
```

**实例**

```javascript
let language = "JavaScript";

let text = "";
for (let x of language) {
text += x;
}
```

**所以for-in更适合遍历对象，通常是建议不要使用for-in遍历数组**。

**使用for-of遍历数组**

## 6. 数据类型

在 JavaScript 中有 5 种不同的可以包含值的数据类型：

- string
- number
- boolean
- object
- function

有 6 种类型的对象：

- Object
- Date
- Array
- String
- Number
- Boolean

以及 2 种不能包含值的数据类型：

- null
- undefined

### JavaScript 类型转换表

下表中列出了将不同 JavaScript 值转换为数字、字符串和布尔的结果：

| 原始值           | 转换为数字 | 转换为字符串      | 转换为逻辑 | 试一试                                                       |
| :--------------- | :--------- | :---------------- | :--------- | :----------------------------------------------------------- |
| false            | 0          | "false"           | false      | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_false) |
| true             | 1          | "true"            | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_true) |
| 0                | 0          | "0"               | false      | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_number_0) |
| 1                | 1          | "1"               | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_number_1) |
| "0"              | 0          | "0"               | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_string_0) |
| "000"            | 0          | "000"             | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_string_000) |
| "1"              | 1          | "1"               | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_string_1) |
| NaN              | NaN        | "NaN"             | false      | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_nan) |
| Infinity         | Infinity   | "Infinity"        | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_infinity) |
| -Infinity        | -Infinity  | "-Infinity"       | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_infinity_minus) |
| ""               | 0          | ""                | false      | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_string_empty) |
| "20"             | 20         | "20"              | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_string_number) |
| "twenty"         | NaN        | "twenty"          | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_string_text) |
| [ ]              | 0          | ""                | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_array_empty) |
| [20]             | 20         | "20"              | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_array_one_number) |
| [10,20]          | NaN        | "10,20"           | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_array_two_numbers) |
| ["twenty"]       | NaN        | "twenty"          | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_array_one_string) |
| ["ten","twenty"] | NaN        | "ten,twenty"      | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_array_two_strings) |
| function(){}     | NaN        | "function(){}"    | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_function) |
| { }              | NaN        | "[object Object]" | true       | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_object) |
| null             | 0          | "null"            | false      | [试一试](https://www.w3school.com.cn/tiy/t.asp?f=eg_js_type_convert_null) |
| undefined        | NaN        | "undefined"       | false      |                                                              |

## 7. this 关键字

this 是什么？

JavaScript this 关键词指的是它所属的对象。

它拥有不同的值，具体取决于它的使用位置：

- 在方法中，this 指的是所有者对象。
- 单独的情况下，this 指的是全局对象。
- 在函数中，this 指的是全局对象。
- 在函数中，严格模式下，this 是 undefined。
- 在事件中，this 指的是接收事件的元素。

像 call() 和 apply() 这样的方法可以将 this 引用到任何对象。

## 8.JSON

**把 JSON 文本转换为 JavaScript 对象**

JSON 的通常用法是从 web 服务器读取数据，然后在网页中显示数据。

为了简单起见，可以使用字符串作为输入演示。

首先，创建包含 JSON 语法的 JavaScript 字符串：

```javascript
var text = '{ "employees" : [' +
'{ "firstName":"Bill" , "lastName":"Gates" },' +
'{ "firstName":"Steve" , "lastName":"Jobs" },' +
'{ "firstName":"Alan" , "lastName":"Turing" } ]}';
```

然后，使用 JavaScript 的内建函数 JSON.parse() 来把这个字符串转换为 JavaScript 对象：

```javascript
var obj = JSON.parse(text);
```

最后，请在您的页面中使用这个新的 JavaScript 对象：

**实例**

```javascript
<p id="demo"></p>

<script>
document.getElementById("demo").innerHTML =
obj.employees[1].firstName + " " + obj.employees[1].lastName;
</script> 
```

## 9. 函数function

## 10. arguments 对象

JavaScript 函数有一个名为 arguments 对象的内置对象。

arguments 对象包含函数调用时使用的参数数组。

这样，您就可以简单地使用函数来查找（例如）数字列表中的最高值：

**实例**

```javascript
x = findMax(1, 123, 500, 115, 44, 88);

function findMax() {
    var i;
    var max = -Infinity;
    for (i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}
```

## 11. JavaScript Promise 对象

JavaScript Promise 对象包含生产代码和对消费代码的调用：

### Promise 语法

```javascript
let myPromise = new Promise(function(myResolve, myReject) {
// "Producing Code"（可能需要一些时间）

  myResolve(); // 成功时
  myReject();  // 出错时
});

// "Consuming Code" （必须等待一个兑现的承诺）
myPromise.then(
  function(value) { /* 成功时的代码 */ },
  function(error) { /* 出错时的代码 */ }
);
```

当执行代码获得结果时，它应该调用两个回调之一：

| 结果 | 调用                    |
| :--- | :---------------------- |
| 成功 | myResolve(result value) |
| 出错 | myReject(error object)  |

### Promise 对象属性

JavaScript Promise 对象可以是：

- Pending
- Fulfilled
- Rejected

Promise 对象支持两个属性：*state* 和 *result*。

当 Promise 对象 "pending"（工作）时，结果是 undefined。

当 Promise 对象 "fulfilled" 时，结果是一个值。

当一个 Promise 对象是 "rejected" 时，结果是一个错误对象。

| myPromise.state | myPromise.result |
| :-------------- | :--------------- |
| "pending"       | undefined        |
| "fulfilled"     | 结果值           |
| "rejected"      | error 对象       |

您无法访问 Promise 属性 state 和 result。

您必须使用 Promise 方法来处理 Promise。

### 如何使用 Promise

以下是使用 Promise 的方法：

```javascript
myPromise.then(
  function(value) { /* code if successful */ },
  function(error) { /* code if some error */ }
);
```

Promise.then() 有两个参数，一个是成功时的回调，另一个是失败时的回调。

两者都是可选的，因此您可以为成功或失败添加回调。

**实例**

```javascript
function myDisplayer(some) {
  document.getElementById("demo").innerHTML = some;
}

let myPromise = new Promise(function(myResolve, myReject) {
  let x = 0;

// 生成代码（这可能需要一些时间）

  if (x == 0) {
    myResolve("OK");
  } else {
    myReject("Error");
  }
});

myPromise.then(
  function(value) {myDisplayer(value);},
  function(error) {myDisplayer(error);}
);
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=js_promise_2)

### JavaScript Promise 实例

为了演示 Promise 的使用，我们将使用上一章中的回调实例：

- 等待超时
- 等待文件

### 等待超时

#### 使用回调的例子

```javascript
setTimeout(function() { myFunction("I love You !!!"); }, 3000);

function myFunction(value) {
  document.getElementById("demo").innerHTML = value;
}
```

#### 使用 Promise 的例子

```javascript
let myPromise = new Promise(function(myResolve, myReject) {
  setTimeout(function() { myResolve("I love You !!"); }, 3000);
});

myPromise.then(function(value) {
  document.getElementById("demo").innerHTML = value;
});
```

### 等待文件

#### 使用回调的例子

```javascript
function getFile(myCallback) {
  let req = new XMLHttpRequest();
  req.open('GET', "mycar.html");
  req.onload = function() {
    if (req.status == 200) {
      myCallback(req.responseText);
    } else {
      myCallback("Error: " + req.status);
    }
  }
  req.send();
}

getFile(myDisplayer);
```

#### 使用 Promise 的例子

```javascript
let myPromise = new Promise(function(myResolve, myReject) {
  let req = new XMLHttpRequest();
  req.open('GET', "mycar.htm");
  req.onload = function() {
    if (req.status == 200) {
      myResolve(req.response);
    } else {
      myReject("File not Found");
    }
  };
  req.send();
});

myPromise.then(
  function(value) {myDisplayer(value);},
  function(error) {myDisplayer(error);}
);
```

## 12. JavaScript Async

"async and await make promises easier to write"

*async* 使函数返回 Promise

*await* 使函数等待 Promise

### Async 语法

函数前的关键字 async 使函数返回 promise：

**实例**

```javascript
async function myFunction() {
  return "Hello";
}
```

等同于：

```javascript
async function myFunction() {
  return Promise.resolve("Hello");
}
```

以下是使用 Promise 的方法：

```javascript
myFunction().then(
  function(value) { /* 成功时的代码 */ },
  function(error) { /* 出错时的代码 */ }
);
```

**实例**

```javascript
async function myFunction() {
  return "Hello";
}
myFunction().then(
  function(value) {myDisplayer(value);},
  function(error) {myDisplayer(error);}
);
```

或者更简单，因为您期望正常值（正常响应，而不是错误）：

**实例**

```javascript
async function myFunction() {
  return "Hello";
}
myFunction().then(
  function(value) {myDisplayer(value);}
);
```

[亲自试一试](https://www.w3school.com.cn/tiy/t.asp?f=js_async_1)

### Await 语法

函数前的关键字 await 使函数等待 promise：

```javascript
let value = await promise;
```

await 关键字只能在 async 函数中使用。

**实例**

让我们慢慢来学习如何使用它。

#### 基础语法

```javascript
async function myDisplay() {
  let myPromise = new Promise(function(myResolve, myReject) {
    myResolve("I love You !!");
  });
  document.getElementById("demo").innerHTML = await myPromise;
}

myDisplay();
```

#### 等待超时

```javascript
async function myDisplay() {
  let myPromise = new Promise(function(myResolve, myReject) {
    setTimeout(function() { myResolve("I love You !!"); }, 3000);
  });
  document.getElementById("demo").innerHTML = await myPromise;
}

myDisplay();
```

#### 等待文件

```javascript
async function getFile() {
  let myPromise = new Promise(function(myResolve, myReject) {
    let req = new XMLHttpRequest();
    req.open('GET', "mycar.html");
    req.onload = function() {
      if (req.status == 200) {myResolve(req.response);}
      else {myResolve("File not Found");}
    };
    req.send();
  });
  document.getElementById("demo").innerHTML = await myPromise;
}

getFile();
```

# Ajax

## 创建 XMLHttpRequest 

```js
if(window.XMLHttpRequest){
    // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    var xmlhttp = new XMLHttpRequest()
}else{
    // IE6, IE5 浏览器执行代码
    xmlhttp = new ActiveXObject("Microsoft.XMLHTTP")
}
```

## 发送请求

GET请求：

```js
xmlhttp.open("GET", "ajax_info.txt", true)
xmlhttp.send();
```

| 方法                         | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| open(*method*,*url*,*async*) | 规定请求的类型、URL 以及是否异步处理请求。method：请求的类型；GET 或 POST url：文件在服务器上的位置 t async：true（异步）或 false（同步） |
| send(*string*)               | 将请求发送到服务器。string：仅用于 POST 请求                 |

在上面的例子中，您可能得到的是缓存的结果。

为了避免这种情况，请向 URL 添加一个唯一的 ID：

**实例**

```js
xmlhttp.open("GET","/try/ajax/demo_get.php?t=" + Math.random(),true); xmlhttp.send();
```

**POST请求：**

```js
xmlhttp.open("POST","/try/ajax/demo_post.php",true)
xmlhttp.send();
```

POST接受HTML表单：

```js
xmlhttp.open("POST","/try/ajax/demo_post2.php",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Henry&lname=Ford");
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

## 服务器响应

**responseText 属性**

如果来自服务器的响应并非 XML，请使用 responseText 属性。

responseText 属性返回字符串形式的响应，因此您可以这样使用：

```js
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

**responseXML 属性**

如果来自服务器的响应是 XML，而且需要作为 XML 对象进行解析，请使用 responseXML 属性：



请求 [cd_catalog.xml](https://www.runoob.com/try/demo_source/cd_catalog.xml) 文件，并解析响应：

```js
xmlDoc=xmlhttp.responseXML;
txt="";
x=xmlDoc.getElementsByTagName("ARTIST");
for (i=0;i<x.length;i++)
{
    txt=txt + x[i].childNodes[0].nodeValue + "<br>";
}
document.getElementById("myDiv").innerHTML=txt;
```

## 实例

异步加载 JSON 数据

```js
function loadXMLDoc()
{
  var xmlhttp;
  if (window.XMLHttpRequest)
  {
    // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
  }
  else
  {
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
  xmlhttp.onreadystatechange=function()
  {
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
      var myArr = JSON.parse(this.responseText);
      myFunction(myArr)
    }
  }
  xmlhttp.open("GET","/try/ajax/json_ajax.json",true);
  xmlhttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
  xmlhttp.send();
}
function myFunction(arr) {
  var out = "";
  var i;
  for(i = 0; i < arr.length; i++) {
    out += '<a href="' + arr[i].url + '">' + 
    arr[i].title + '</a><br>';
  }
 document.getElementById("myDiv").innerHTML=out;
}
```

`json_ajax.jso`

```js
[
  {
    "title": "JavaScript 教程",
    "url": "https://www.runoob.com/js/"
  },
  {
    "title": "HTML 教程",
    "url": "https://www.runoob.com/html/"
  },
  {
    "title": "CSS 教程",
    "url": "https://www.runoob.com/css/"
  }
]
```

# JS 高级

## this

1. this 是什么
   - 任何函数本质上都是通过某个对象来调用的，如果没有指定对象调用，则是window调用
   - 任何函数内部都有一个变量this
   - 它的值是调用函数的当前对象
2. 如何确定this的值
   - test(): window
   - p.test(): p
   - new test(): 新创建的对象
   - p.call(obj): obj

## 数据类型

JS有基础数据类型:number,string,boolean,symbol,undefined,null  引用数据类型：Object

typeof: 

- 可以判断 number string boolean undefined function 
- 不能判断 null 与 Object, Object 与 Array



# 杂项

## 1. 监听窗口改变

```javascript
 window.onresize = function () {  
            alert("111")
        }
```

当窗口改变时会触发事件

## 2.立即执行函数

```js
(function(){})();
```

## 深拷贝

首先我们先执行这样一段代码：

```
var a = 5;
var b = a;
b += 5;
console.log('a:'+a+'b:'+b);
var arr = [1,2,3];
var brr = arr;
brr.push(10);
console.log("arr :"+arr)
console.log("brr :"+brr)
```

运行结果：

![在这里插入图片描述](https://img.jbzj.com/file_images/article/202111/2021111515511856.png)

可以看出来改变其中一个引用类型另外一个也改变了，为了解决这种问题，我们引入了深拷贝。

我们通常使用`JSON.stringify`和`JSON.parse`实现深拷贝。数组和对象可以使用深拷贝，但是函数不能使用深拷贝。

由此，我们可以这样改造上边的代码以实现深拷贝：

```js
var brr = JSON.parse(JSON.stringify(arr))
```

example:

```js
  var arr = {
         name: '浪漫主义码农',
         age: 20,
         adress: ['jiangxi', 'changsha'],
         friends: {
             friend1: '张三',
             friend2: '李四'
         },
         function(){
             console.log("我是浪漫主义的对象")
         }
     }
     var brr=JSON.parse(JSON.stringify(arr))
     brr.name='法外狂徒张三'
     brr.adress[0]='长沙'
     console.log("arr为", arr)
     console.log("brr为", brr)
```

result:

![在这里插入图片描述](https://img.jbzj.com/file_images/article/202111/2021111515511858.png)

也可以使用 es6 的扩展运算符:

```js
var brr = {...arr}
```

example:

```js
  var arr = {
         name: '浪漫主义码农',
         age: 20,
         adress: ['jiangxi', 'changsha'],
         friends: {
             friend1: '张三',
             friend2: '李四'
         },
         function(){
             console.log("我是浪漫主义的对象")
         }
     }
     var brr={...arr}
     brr.name='法外狂徒张三'
     brr.adress[0]='长沙'
     console.log("arr为", arr)
     console.log("brr为", brr)
```

result:

![在这里插入图片描述](https://img.jbzj.com/file_images/article/202111/2021111515511859.png)

3.手写递归

```js
  //使用递归实现深拷贝
     function deepClone(obj) {
         //判断拷贝的obj是对象还是数组
         var objClone = Array.isArray(obj) ? [] : {};
         if (obj && typeof obj === "object") { //obj不能为空，并且是对象或者是数组 因为null也是object
             for (key in obj) {
                 if (obj.hasOwnProperty(key)) {
                     if (obj[key] && typeof obj[key] === "object") { //obj里面属性值不为空并且还是对象，进行深度拷贝
                         objClone[key] = deepClone(obj[key]); //递归进行深度的拷贝
                     } else {
                         objClone[key] = obj[key]; //直接拷贝
                     }
                 }
             }
         }
         return objClone;
     }

```

## 本地储存

**cookie（常用于获取储存登录信息的用户信息）**:

语法：

```javascript
document.cookie = " cookie1 = value1  cookie2 = value2; 

                    expires(设置cookie过期时间)= Sun, 31 Dec 2021 12:00:00 UTC；

                    path=/ (设置cookie路径)"
```



```javascript
document.cookie = "username = wsj666 password = 123456; 
				   expires = Sun, 31 Dec 2021 12:00:00 UTC;
				   path=/"
 
var x = document.cookie

console.log(x)    //  username = wsj666   password = 123456
```





为什么需要使用本地储存呢：

- 数据储存在用户浏览器中，在控制台中**Application**模块中查看
- 设置、读取方便，页面刷新后不会丢失数据
- 容量较大，`sessionSrorage`约为5M、`localStorage`约20M
- 只能储存字符串，可以将对象`JSON.stringify()`编码后储存

**Application**模块：

![image-20220717192056277](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220717192056277.png)

本地储存分为两种，一种是`sessionStorage`，另外一种是`localStorage`。

**window.sessionStorage**

1. 生命周期为关闭浏览器窗口，也就是说关闭浏览器会自动清空
2. 在同一个窗口(页面)下数据可以共享
3. 以键值对的形式存储使用

**window.localStorage**

1. 声明周期永久生效，除非手动删除 否则关闭页面也会存在
2. 可以多窗口（页面）共享（同一浏览器可以共享）
3. 以键值对的形式存储使用

这两种的区别就是`sessionStorage`关闭浏览器窗口就会自动清空，而`localStorage`不会，如果想要删除`localStorage`，可以使用`localStorage.removeItem(key)`和`localStorage.clear()`删除。

这两种的用法相同：

**sessionStorage**:关闭浏览器清空。

储存数据

```javascript
sessionStorage.setItem(key,value)
```

获取数据

```js
sessionStorage.getItem(key)
```

删除数据

```javascript
sessionStorage.removeItem(key)
```

清空数据

```javascript
sessionStorage.clear()
```

**localSorage**：不会清空

储存数据

```javascript
localSorage.setItem(key,value)
```

获取数据

```js
localSorage.getItem(key)
```

删除数据

```javascript
localSorage.removeItem(key)
```

清空数据

```javascript
localSorage.clear()
```

## 对一个数组进行去重操作

1. 利用 set 进行去重

```js
let arr = ['赵四','刘能','宋小宝','赵本山','赵本山','宋小宝']
// 使用 set 对数组进行去重
let setarr = new Set( arr )
// 现在这个 newarr 是一个对象，并不是一个数组，再将此对象转化为数组
let newarr = Array.from( setarr )
// 这样这个 newarr 就是我们需要的去重后的数组
```

2. 

```js
let arr = ['赵四','刘能','宋小宝','赵本山','赵本山','宋小宝']
let newarr = [...new Set(arr)]
```

## 使用JS设置Css变量

在 JavaScript 中获取或者修改 CSS 变量和操作普通 CSS 属性是一样的：

```js
// 获取一个 Dom 节点上的 CSS 变量
element.style.getPropertyValue("--my-var");

// 获取任意 Dom 节点上的 CSS 变量
getComputedStyle(element).getPropertyValue("--my-var");

// 修改一个 Dom 节点上的 CSS 变量
element.style.setProperty("--my-var", jsVar + 4);
```

## 屏幕宽高

网页可见区域宽：document.body.clientWidth

网页可见区域高：document.body.clientHeight

网页可见区域宽：document.body.offsetWidth (包括边线的宽)

网页可见区域高：document.body.offsetHeight (包括边线的宽)

网页正文全文宽：document.body.scrollWidth

网页正文全文高：document.body.scrollHeight

网页被卷去的高：document.body.scrollTop

网页被卷去的左：document.body.scrollLeft

网页正文部分上：window.screenTop

网页正文部分左：window.screenLeft

屏幕分辨率的高：window.screen.height

屏幕分辨率的宽：window.screen.width

屏幕可用工作区高度：window.screen.availHeight

屏幕可用工作区宽度：window.screen.availWidth

获取窗口宽度：window.innerWidth

获取窗口高度：window.innerHeight





