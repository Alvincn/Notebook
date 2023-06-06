# Less 安装

通过`npm`安装：

```bash
npm install less -g
```

不想安装到全局可以这样：

```bash
npm i less --save-dev
```

# Less demo

## 变量（Variables）

无需多说，看代码一目了然：

```less
@width: 10px;
@height: @width + 10px;

#header {
  width: @width;
  height: @height;
}
```

编译为：

```css
#header {
  width: 10px;
  height: 20px;
}
```

## 混合（Mixins）

混合（Mixin）是一种将一组属性从一个规则集包含（或混入）到另一个规则集的方法。假设我们定义了一个类（class）如下：

```css
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```

如果我们希望在其它规则集中使用这些属性呢？没问题，我们只需像下面这样输入所需属性的类（class）名称即可，如下所示：

```less
#menu a {
  color: #111;
  .bordered();
}

.post a {
  color: red;
  .bordered();
}
```

##  嵌套（Nesting）

Less 提供了使用嵌套（nesting）代替层叠或与层叠结合使用的能力。假设我们有以下 CSS 代码：

```css
#header {
  color: black;
}
#header .navigation {
  font-size: 12px;
}
#header .logo {
  width: 300px;
}
```

用 Less 语言我们可以这样书写代码：

```less
#header {
  color: black;
  .navigation {
    font-size: 12px;
  }
  .logo {
    width: 300px;
  }
}
```

你还可以使用此方法将伪选择器（pseudo-selectors）与混合（mixins）一同使用。下面是一个经典的 clearfix 技巧，重写为一个混合（mixin） (`&` 表示当前选择器的父级）：

```less
.clearfix { 
  display: block;
  zoom: 1;

  &:after {
    content: " ";
    display: block;
    font-size: 0;
    height: 0;
    clear: both;
    visibility: hidden;
  }
}
```

**@规则嵌套和冒泡**

@ 规则（例如 `@media` 或 `@supports`）可以与选择器以相同的方式进行嵌套。@ 规则会被放在前面，同一规则集中的其它元素的相对顺序保持不变。这叫做冒泡（bubbling）。

```less
.component {
  width: 300px;
  @media (min-width: 768px) {
    width: 600px;
    @media  (min-resolution: 192dpi) {
      background-image: url(/img/retina2x.png);
    }
  }
  @media (min-width: 1280px) {
    width: 800px;
  }
}
```

编译为：

```css
.component {
  width: 300px;
}
@media (min-width: 768px) {
  .component {
    width: 600px;
  }
}
@media (min-width: 768px) and (min-resolution: 192dpi) {
  .component {
    background-image: url(/img/retina2x.png);
  }
}
@media (min-width: 1280px) {
  .component {
    width: 800px;
  }
}
```

## 运算（Operations）

算术运算符 `+`、`-`、`*`、`/` 可以对任何数字、颜色或变量进行运算。

计算的结果以最左侧操作数的单位类型为准。如果单位换算无效或失去意义，则忽略单位。

无效的单位换算例如：px 到 cm 或 rad 到 % 的转换。

```less
// 所有操作数被转换成相同的单位
@conversion-1: 5cm + 10mm; // 结果是 6cm
@conversion-2: 2 - 3cm - 5mm; // 结果是 -1.5cm

// conversion is impossible
@incompatible-units: 2 + 5px - 3cm; // 结果是 4px

// example with variables
@base: 5%;
@filler: @base * 2; // 结果是 10%
@other: @base + @filler; // 结果是 15%
```

乘法和除法不作转换。因为这两种运算在大多数情况下都没有意义，一个长度乘以一个长度就得到一个区域，而 CSS 是不支持指定区域的。Less 将按数字的原样进行操作，并将为计算结果指定明确的单位类型。

```less
@base: 2cm * 3mm; // 结果是 6cm
```

你还可以对颜色进行算术运算：

```less
@color: #224488 / 2; //结果是 #112244
background-color: #112244 + #111; // 结果是 #223355
```

 **calc() 特例**

为了与 CSS 保持兼容，`calc()` 并不对数学表达式进行计算，但是在嵌套函数中会计算变量和数学公式的值。

```less
@var: 50vh/2;
width: calc(50% + (@var - 20px));  // 结果是 calc(50% + (25vh - 20px))
```

##  转义（Escaping）

转义（Escaping）允许你使用任意字符串作为属性或变量值。任何 `~"anything"` 或 `~'anything'` 形式的内容都将按原样输出，除非 [interpolation](https://less.bootcss.com/features/#variables-feature-variable-interpolation)。

```less
@min768: ~"(min-width: 768px)";
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
```

编译为：

```less
@media (min-width: 768px) {
  .element {
    font-size: 1.2rem;
  }
}
```

注意，从 Less 3.5 开始，可以简写为：

```less
@min768: (min-width: 768px);
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
```

## 函数（Functions）

Less 内置了多种函数用于转换颜色、处理字符串、算术运算等。这些函数在[Less 函数手册](https://less.bootcss.com/functions/)中有详细介绍。

函数的用法非常简单。下面这个例子将介绍如何利用 percentage 函数将 0.5 转换为 50%，将颜色饱和度增加 5%，以及颜色亮度降低 25% 并且色相值增加 8 等用法：

```less
@base: #f04615;
@width: 0.5;

.class {
  width: percentage(@width); // returns `50%`
  color: saturate(@base, 5%);
  background-color: spin(lighten(@base, 25%), 8);
}
```

## 命名空间和访问符

(不要和 [CSS `@namespace`](http://www.w3.org/TR/css3-namespace/) 或 [namespace selectors](http://www.w3.org/TR/css3-selectors/#typenmsp) 混淆了)。

有时，出于组织结构或仅仅是为了提供一些封装的目的，你希望对混合（mixins）进行分组。你可以用 Less 更直观地实现这一需求。假设你希望将一些混合（mixins）和变量置于 `#bundle` 之下，为了以后方便重用或分发：

```less
#bundle() {
  .button {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover {
      background-color: white;
    }
  }
  .tab { ... }
  .citation { ... }
}
```

现在，如果我们希望把 `.button` 类混合到 `#header a` 中，我们可以这样做：

```less
#header a {
  color: orange;
  #bundle.button();  // 还可以书写为 #bundle > .button 形式
}
```

注意：如果不希望它们出现在输出的 CSS 中，例如 `#bundle .tab`，请将 `()` 附加到命名空间（例如 `#bundle()`）后面。

## 映射（Maps）

从 Less 3.5 版本开始，你还可以将混合（mixins）和规则集（rulesets）作为一组值的映射（map）使用。

```less
#colors() {
  primary: blue;
  secondary: green;
}

.button {
  color: #colors[primary];
  border: 1px solid #colors[secondary];
}
```

输出符合预期：

```css
.button {
  color: blue;
  border: 1px solid green;
}
```

##  作用域（Scope）

Less 中的作用域与 CSS 中的作用域非常类似。首先在本地查找变量和混合（mixins），如果找不到，则从“父”级作用域继承。

```less
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}
```

与 CSS 自定义属性一样，混合（mixin）和变量的定义不必在引用之前事先定义。因此，下面的 Less 代码示例和上面的代码示例是相同的：

```less
@var: red;

#page {
  #header {
    color: @var; // white
  }
  @var: white;
}
```

##  导入（Importing）

“导入”的工作方式和你预期的一样。你可以导入一个 `.less` 文件，此文件中的所有变量就可以全部使用了。如果导入的文件是 `.less` 扩展名，则可以将扩展名省略掉：

```css
@import "library"; // library.less
@import "typo.css";
```

# Less内置函数

## 逻辑函数(Logical Functions)

**Usage：**

```less
if((逻辑表达式),真时的值，假时的值)
```

**Examples：**

```less
@some:foo;
div{
    //判断如果2>1，就是3px，反之为0px
    margin:if((2>1),3px,0px);
    //判断@some是不是一种颜色，是的话就是some,反之为black
    color:if((iscolor(@some)),@some,black);
}
```

**Result:**

```css
div{
    margin:0;
    color:black;
}
```

同时也支持这样：

```less
@bg:black;
@bg-light:boolean(luma(@bg) > 50%);
div{
    background:@bg;
    color: if(@bg-light,black,white)
}
```

Result:

```css
div{
    background:black;
    color:white;
}
```

## 字符串函数(String Functions)

1. escape():

   将输入字符串的url特殊字符进行编码处理。

   未编码：, , , , , , , , and .`,``/``?``@``&``+``'``~``!``$`；

   转义的编码：, , , , , , , , , , , , , and .`\<space\>``#``^``(``)``{``}``|``:``>``<``;``]``[``=`

   Example:

   ```less
   escape('a = 1')
   ```

   Output:

   ```css
   a%3D1
   ```

2. e()

   字符串转义，用~"值"符号代替。

   参数：- 要转义的字符串。`string`

   返回：- 不带引号的转义字符串。`string`

   Example:

   ```less
   @mscode: "ms:alwaysHasItsOwnSyntax.For.Stuff()" 
   filter: e(@mscode);
   ```

   Result:

   ```css
   filter: ms:alwaysHasItsOwnSyntax.For.Stuff();
   ```

3. %()

   函数%(string,arguments…) 格式化一个字符串。

   参数：

   ​	`string`:占位符的格式字符串

   ​	`anything`:用于替占位符的值

   该函数设置字符串的格式。`%(string, arguments ...)`

   第一个参数是带占位符的字符串。所有占位符都以百分比符号开头，后跟字母 ,,,,, 或 。其余参数包含用于替换占位符的表达式。如果需要打印百分比符号，请将其转义为 另一个百分比 。`%``s``S``d``D``a``A``%%`

   小写：占位符

   大写：使用`utf-8`编码转义。

   Example:

   ```less
   format-a-d: %("repetitions: %a file: %d", 1 + 2, "directory/file.less");
   format-a-d-upper: %('repetitions: %A file: %D', 1 + 2, "directory/file.less");
   format-s: %("repetitions: %s file: %s", 1 + 2, "directory/file.less");
   format-s-upper: %('repetitions: %S file: %S', 1 + 2, "directory/file.less");
   ```

   Result:

   ```css
   format-a-d: "repetitions: 3 file: "directory/file.less"";
   format-a-d-upper: "repetitions: 3 file: %22directory%2Ffile.less%22";
   format-s: "repetitions: 3 file: directory/file.less";
   format-s-upper: "repetitions: 3 file: directory%2Ffile.less";
   ```

4. replace()

   `replace(string,pattern,replacement,flags(可选))`

   参数：

   ​	`string`：要搜索的字符串。

   ​	`pattern`：要搜索的字符串或者正则表达式。

   ​	`replacement`：搜索字符串模式'g‘。

   ​	`flags`：（可选）正则表达式标志。

   返回：
   	包含替换值的字符串。

   Example：

   ```less
   replace("Hello, Mars?", "Mars\?", "Earth!");
   replace("One + one = 4", "one", "2", "gi");
   replace('This is a string.', "(string)\.$", "new $1.");
   replace(~"bar-1", '1', '2');
   ```

   Result：

   ```css
   "Hello, Earth!";
   "2 + 2 = 4";
   'This is a new string.';
   bar-2;
   ```
## 列表函数(List Functions)

1. `length()`

   返回集合中值的个数。

   Example：

   ```less
   length(1px solid #0080ff)
   ```

   Output:

   ```css
   3
   ```

   Example:

   ```less
   @list: "banana", "tomato", "potato", "peach";
   n: length(@list);
   ```

   Output:

   ```css
   n: 4;
   ```

2. `extract()`

   `extract(list, index)`

   返回数组中指定索引的值；

   参数：

   ​	`list`：数组

   ​	`index`：索引

   Example:

   ```less
   @list: apple, pear, coconut, orange;
   value: extract(@list, 3);
   ```

   Output:

   ```css
   value: coconut;
   ```

3. `range()`

   生成一个跨越一系列值得列表。

   `range(start,end,step)`

   参数：

   ​	`start`: 起始值。

   ​	`end`: 结束值。

   ​	`step`: 每次递增的数字。

   Example:

   ```less
   value:range(4);
   ```

   Output:

   ```css
   value: 1, 2, 3, 4
   ```

   Example:

   ```less
   value: range(10px, 30px, 10);
   ```

   Output:

   ```css
   value: 10px, 20px, 30px;
   ```

4. `each()`

   将规则集的计算绑定到列表的每个成员。

   `each(list,rules)`

   参数：

   ​	`list`: 集合

   ​	`rules`: 匿名规则集

   Example:

   ```less
   @selectors: blue, green, red;
   each(@selectors,{
       .sel-@{value} {
           color: b;
       }
   })
   ```

   Output:

   ```css
   .sel-blue {
     color: b;
   }
   .sel-green {
     color: b;
   }
   .sel-red {
     color: b;
   }
   ```

   缺省情况下，每个规则集都按列表成员绑定到 、 和变量。对于大多数列表，将分配相同的值（数字位置，从 1 开始）。但是，您也可以将规则集*本身*用作结构化列表。如：`@value``@key``@index``@key``@index`

   ```less
   @set: {
     one: blue;
     two: green;
     three: red;
   }
   .set {
     each(@set, {
       @{key}-@{index}: @value;
     });
   }
   ```

   这将输出：

   ```css
   .set {
     one-1: blue;
     two-2: green;
     three-3: red;
   }
   ```

   当然，由于您可以为每个规则集调用使用保护来调用 mixin，因此这具有非常强大的功能。`each()`

   匿名 mixin 使用的形式或以常规 mixin 开头或就像常规 mixin 一样。在 中，您可以按如下方式使用它：`#()``.()``.``#``each()`

   ```less
   .set-2() {
     one: blue;
     two: green;
     three: red;
   }
   .set-2 {
     // Call mixin and iterate each rule
     each(.set-2(), .(@v, @k, @i) {
       @{k}-@{i}: @v;
     });
   }
   ```

   这将按预期输出：

   ```css
   .set-2 {
     one-1: blue;
     two-2: green;
     three-3: red;
   }
   ```

5. **使用、创建循环`for range each`**

   Example：

   ```less
   each(range(4), {
     .col-@{value} {
       height: (@value * 50px);
     }
   });
   ```

   Output：

   ```css
   .col-1 {
     height: 50px;
   }
   .col-2 {
     height: 100px;
   }
   .col-3 {
     height: 150px;
   }
   .col-4 {
     height: 200px;
   }
   ```

## 数学函数(Math Functions)

1. `ceil()`

   向上取整。

   Example:

   ```less
   width: ceil(2.4px);
   ```

   Output:

   ```css
   width: 3px;
   ```

2. `floor()`

   向下取整。

   Example:

   ```less
   height: floot(2.99999px);
   ```

   Output:

   ```css
   height: 2px;
   ```

3. `percentage()`

   将浮点数转换为百分比。

   Example:

   ```less
   width: percentage(0.5);
   ```

   Output:

   ```css
   width: 50%;
   ```

4. `round()`

   四舍五入和保留小数点。

   参数：

   ​	`number`: 浮点数；

   ​	`decimalPlaces`: 可选参数，要保留的小数点位数。

   Example:

   ```less
   round(1.67);
   round(1.67, 1)
   ```

   Output:

   ```css
   2;
   1.7;
   ```

5. `sqrt()`

   计算数字的平方根，保持单位不变。

   Example:

   ```less
   sqrt(25cm);
   sqrt(18.6%)
   ```

   Output:

   ```css
   5cm;
   4.312771730569565%;
   ```

6. `abs()`

   计算数字的绝对值，保持单位不变。

   Example:

   ```less
   abs(25cm);
   abs(-18.6%);
   ```

   Output:

   ```css
   25cm;
   18.6%;
   ```

7. `sin()`

   计算正弦函数。

   Example：

   ```less
   width: sin(1); 
   //1弧度角的正弦值
   width: sin(1deg);
   //1角度角的正弦值
   width: sin(1grad); 
   //1百分度角的正弦值
   ```

   Output：

   ```css
   0.8414709848078965; // sine of 1 radian
   0.01745240643728351; // sine of 1 degree
   0.015707317311820675; // sine of 1 gradian
   ```

8. `asin()`

   反正弦函数。

   Example:

   ```less
   width: asin(-0.84147098);
   width: asin(0);
   width: asin(2);
   ```

   Output:

   ```css
   -1rad
   0rad
   NaNrad
   ```

9. `cos()`

   余弦函数。

   Example:

   ```less
   cos(1) // cosine of 1 radian
   cos(1deg) // cosine of 1 degree
   cos(1grad) // cosine of 1 gradian
   ```

   Output:

   ```css
   0.5403023058681398 // cosine of 1 radian
   0.9998476951563913 // cosine of 1 degree
   0.9998766324816606 // cosine of 1 gradian
   ```

10. `acos()`

    反余弦函数。

    Example:

    ```less
    acos(0.5403023058681398)
    acos(1)
    acos(2)
    ```

    Output:

    ```csss
    1rad
    0rad
    NaNrad
    ```

11. `tan()`

    正切函数。

    Example:

    ```less
    tan(1) // tangent of 1 radian
    tan(1deg) // tangent of 1 degree
    tan(1grad) // tangent of 1 gradian
    ```

    Output:

    ```css
    1.5574077246549023   // tangent of 1 radian
    0.017455064928217585 // tangent of 1 degree
    0.015709255323664916 // tangent of 1 gradian
    ```

12. `atan()`

    Example:

    ```less
    atan(-1.5574077246549023)
    atan(0)
    round(atan(22), 6) // arctangent of 22 rounded to 6 decimal places
    ```

    Output:

    ```css
    -1rad
    0rad
    1.525373rad;
    ```

13. `pi()`

    返回π(pi)。

    Example:

    ```less
    pi()
    ```

    Output:

    ```css
    3.141592653589793
    ```

14. `pow()`

    乘方运算。

    参数：

    ​	`number`: 要乘方的数字；

    ​	`number`: 乘方的倍数。

    Example:

    ```less
    pow(0cm, 0px)
    pow(25, -2)
    pow(25, 0.5)
    pow(-25, 0.5)
    pow(-25%, -0.5)
    ```

    Output:

    ```css
    1cm
    0.0016
    5
    NaN
    NaN%
    ```

15. `mod()`

    取余操作。

    Example:

    ```less
    width: mod(3px,2);
    ```

    Output:

    ```css
    1px;
    ```

16. `min()`

    最小值计算。

    Example:

    ```less
    width: min(3px, 2px, 6px);
    ```

    Output:

    ```css
    width: 2px;
    ```

17. `max()`

    最大值运算。

    Example:

    ```less
    width: max(3px, 2px, 6px);
    ```

    Output:

    ```css
    width: 6px;
    ```

## 类型函数(Type Functions)

1. `isnumber()`

   判断参数是否是数字，是返回true，反之false。

   Example:

   ```less
   isnumber(#ff0);     // false
   isnumber(blue);     // false
   isnumber("string"); // false
   isnumber(1234);     // true
   isnumber(56px);     // true
   isnumber(7.8%);     // true
   isnumber(keyword);  // false
   isnumber(url(...)); // false
   ```

2. `isstring()`

   判断是否是字符串。

   Example:

   ```less
   isstring(#ff0);     // false
   isstring(blue);     // false
   isstring("string"); // true
   isstring(1234);     // false
   isstring(56px);     // false
   isstring(7.8%);     // false
   isstring(keyword);  // false
   isstring(url(...)); // false
   ```

3. `iscolor()`

   判断是否是颜色。

   Example:

   ```less
   iscolor(#ff0);     // true
   iscolor(blue);     // true
   iscolor("string"); // false
   iscolor(1234);     // false
   iscolor(56px);     // false
   iscolor(7.8%);     // false
   iscolor(keyword);  // false
   iscolor(url(...)); // false
   ```

4. `iskeyword()`

   判断是否是关键字。

   Example:

   ```less
   iskeyword(#ff0);     // false
   iskeyword(blue);     // false
   iskeyword("string"); // false
   iskeyword(1234);     // false
   iskeyword(56px);     // false
   iskeyword(7.8%);     // false
   iskeyword(keyword);  // true
   iskeyword(url(...)); // false
   ```

   ```less
   //如果一个值是一个关键字，返回'真(true)',否则返回'假(false)'.
   .m(@x) when (iskeyword(@x)) {
   	x:@x	
   }
   div{
       .m(123);
       .m("ABC");
       .m(red);
       .m(ABC); //x:ABC
   }
   
   ```

5. `isurl()`

   判断是否是url地址。

   Example:

   ```less
   isurl(#ff0);     // false
   isurl(blue);     // false
   isurl("string"); // false
   isurl(1234);     // false
   isurl(56px);     // false
   isurl(7.8%);     // false
   isurl(keyword);  // false
   isurl(url(...)); // true
   ```

   ```less
   //如果一个值是一个url地址，返回'真(true)',否则返回'假(false)'.
   .m(@x) when (isurl(@x)) {
   	x:@x
   }
   div{
       .m(ABC);
       .m(url(arr.jpg)); //x:url(arr.jpg)
   }
   ```

6. `ispixel()`

   如果值是以`px`为单位的数字，返回true，反之为false。

   Example:

   ```less
   ispixel(#ff0);     // false
   ispixel(blue);     // false
   ispixel("string"); // false
   ispixel(1234);     // false
   ispixel(56px);     // true
   ispixel(7.8%);     // false
   ispixel(keyword);  // false
   ispixel(url(...)); // false
   ```

7. `isem()`

   如果值是以`em`为单位的数字，返回true，反之为false。

   Example:

   ```less
   isem(#ff0);     // false
   isem(blue);     // false
   isem("string"); // false
   isem(1234);     // false
   isem(56px);     // false
   isem(7.8em);    // true
   isem(keyword);  // false
   isem(url(...)); // false
   ```

8. `ispercentage()`

   如果值是以`%`为单位的数字，返回true，反之为false。

   Example:

   ```less
   ispercentage(#ff0);     // false
   ispercentage(blue);     // false
   ispercentage("string"); // false
   ispercentage(1234);     // false
   ispercentage(56px);     // false
   ispercentage(7.8%);     // true
   ispercentage(keyword);  // false
   ispercentage(url(...)); // false
   ```

9. `isunit()`

   如果值是带指定单位的数字，返回true，否则返回false。

   如果第一个参数的单位是第二个参数就返回true。

   参数：

   ​	`value`: 要判断的数字；

   ​	`unit`: 要查找的单位。

   Example:

   ```less
   isunit(11px, px);  // true
   isunit(2.2%, px);  // false
   isunit(33px, rem); // false
   isunit(4rem, rem); // true
   isunit(56px, "%"); // false
   isunit(7.8%, '%'); // true
   isunit(1234, em);  // false
   isunit(#ff0, pt);  // false
   isunit("mm", mm);  // false
   ```

10. `isruleset()`

    如果参数是规则集，就是true，反之为false

    Example:

    ```less
    @rules: {
        color: red;
    }
    
    isruleset(@rules);   // true
    isruleset(#ff0);     // false
    isruleset(blue);     // false
    isruleset("string"); // false
    isruleset(1234);     // false
    isruleset(56px);     // false
    isruleset(7.8%);     // false
    isruleset(keyword);  // false
    isruleset(url(...)); // false
    ```

## 颜色值定义函数

1. `rgb()`

    通过十进制红、绿、蓝（RGB）创建不透明的颜色对象。

   参数：

   ​	`red`: 0-255 的整数或 0-100% 百分数；

   ​	`green`: 0-255 的整数或 0-100% 百分数；

   ​	`blue`:  0-255 的整数或 0-100% 百分数；

   Example: `rgb(90, 129, 32)`

   Output: `#5a8120`

2. `rgba()`

   通过十进制红、绿、蓝（RGB），以及alpha四种值（RGBA）创建带alpha透明的颜色对象

   参数：

   ​	`red`: 0-255 的整数或 0-100% 百分数；

   ​	`green`: 0-255 的整数或 0-100% 百分数；

   ​	`blue`:  0-255 的整数或 0-100% 百分数；

   ​	`alpha`: 0-1的分数或 0-100% 百分数；

   Example: `rgba(90, 129, 32, 0.5)`

   Output: `rgba(90, 129, 32, 0.5)`

3. `argb()`

   创建格式为#AARRGGBB的十六进制颜色 ，用于IE滤镜，.net和安卓开发

   Example: `argb(rgba(90, 23, 148, 0.5));`

   Output: `#805a1794`

4. `hls()`

   通过色相，饱和度，亮度（HLS）三种值创建不透明的颜色对象。

   Example: `hsl(90, 100%, 50%)`

   Output: `#80ff00`

5. `hsla()`

   通过色相，饱和度，亮度，以及alpha四种值（HLSA）创建带alpha透明的颜色对象.

   Example: `hsla(90, 100%, 50%, 0.5)`

   Output: `rgba(128, 255, 0, 0.5)`

6. `hsv()`

   通过色相，饱和度，色调（HSV）创建不透明的颜色对象。

   Example: `hsv(90, 100%, 50%)`

   Output: `#408000`

7. `hsva()`

   通过色相，饱和度，亮度，以及alpha四种值（HSVA）创建带alpha透明的颜色对象

   Example: `hsva(90, 100%, 50%, 0.5)`

   Output: `rgba(64, 128, 0, 0.5)`

## 颜色值通道提取函数

1. `hue()`

   从HSL色彩空间中提取色相值。

   Example: `hue(hsl(90, 100%, 50%))`

   Output: `90`

2. `saturation()`

   从HSL色彩空间中提取饱和度。

   Example: `saturation(hsl(90, 100%, 50%))`

   Output: `100%`

3. `lightness()`

   从HSL色彩空间中提取亮度值。

   Example: `lightness(hsl(90, 100%, 50%))`

   Output: `50%`

4. `hsvhue()`

   从HSV色彩空间中提取色相值。

   Example: `hsvhue(hsv(90, 100%, 50%))`

   Output: `90`

5. `hsvsaturation()`

   从HSV色彩空间中提取饱和度值。

   Example: `hsvsaturation(hsv(90, 100%, 50%))`

   Output: `100%`

6. `hsvvalue()`

   从HSV色彩空间中提取色调值。

   Example: `hsvvalue(hsv(90, 100%, 50%))`

   Output: `50%`

7. `red()`

   提取颜色对象的红色值。

   Example: `red(rgb(10, 20, 30))`

   Output: `10`

8. `green()`

   提取颜色对象的绿色值。

   Example: `green(rgb(10, 20, 30))`

   Output: `20`

9. `blue()`

   提取颜色对象的蓝色值。

   Example: `blue(rgb(10, 20, 30))`

   Output: `30`

10. `alpha()`

    提取颜色对象的透明度。

    Example: `alpha(rgba(10, 20, 30, 0.5))`

    Output: `0.5`

11. `luma()`

    计算颜色对象luma的值（亮度的百分比表示法）

    Example: `luma(rgb(100, 200, 30))`

    Output: `44%`

12. `luminance()`

    计算没有伽玛校正的亮度值。

    Example: `luminance(rgb(100, 200, 30))`

    Output: `65%`

## 颜色操作函数

1. `saturate()`

   增加一定数值的颜色饱和度。

   Example: `saturate(hsl(90, 80%, 50%), 20%)`

   Output: `#80ff00 // hsl(90, 100%, 50%)`

   ![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA1lJREFUeF7tmD+MzFEQx2ePdWfPuT0UhGhcIQoNmmu4KK4j2Sg0Go3gGoVChUohQkFE40+0solSIZxCc1cpVK6gEBFyG7eWc9zJ+8n8Mt7O+5nd5Ccv82bLX+bNe/P9vPnztnLp/bZVsF9SClQMelK8s2ANenrMDXqCzA26QU9RgQRjtp5u0BNUIMGQLdMNeoIKJBiyZbpBT1CBBEO2TDfoCSqQYMiW6QY9QQUSDNky3aAnqECCIUeZ6a+a36A53cpx7D9Rg6nLG6E6VMm/dRZWoHm2BW+eL2Xftu6pwrE7ddiya23PGH1fzsHJx5th54F1f/n6NP8THp1qwY591a7zOMOZ6214dnUxX8P56PlwJSyIDroPHGOm4DlI/YJ/N/sD7h793CUtBebv51/C5e+r8OTiF5h72Ony07hZh72N9SWg699lVNCpeCgWQqGZjBk1fmgQGrfqWfSY9ZPnR+DguQ0iRSjM0DrM7g+vl4OVhzsjXl48Y21sQHSm/2EULXTMNATT/riSle/R7WvyrKJZhCLTLPQz0G8B3BpfdIQ+cWb4z+WaboGf6Zwf/9z9tJ2yLkBU0F2QoXKLgGl20hKM67jsp+JxFcP5Xnj7K+/HoawPXRL8Tn3TOGLr7dFB5wYiWiJpuS2CPnu/k0GkWYlwHNSJ08PBPuzOwIEPQQ/NGHjZDHpBzaLihXp6bdNA3r9D0I9cG4UXN9rsYOW2dxfh8IUReHplMbPhLgbXi4vagd/7j98bg7kHHcC2ZOU9AB5Lot8zcXBzF2F8cjCHHurpFCi3Ffp/eftrVg2oH79N0AFMMgPgfkV+yurVUr9RlXduCuayv2h6R4BoUzTNcxM2ruP+GwhBdwPj/MwS7J4aynSXvAqkgMqwiwp60XuXDkncM8qJE+r9oUGuqBdzfbgIOvdOj/G55rSICjrC8f/Z4sTzwXM2HFTf7l/POnpheoHOVYoysrYfn1FC7ycQWyNXwKDLtVJjadDVoJQHYtDlWqmxNOhqUMoDMehyrdRYGnQ1KOWBGHS5VmosDboalPJADLpcKzWWBl0NSnkgBl2ulRpLg64GpTwQgy7XSo2lQVeDUh6IQZdrpcbSoKtBKQ/EoMu1UmNp0NWglAdi0OVaqbE06GpQygP5DZ/K0e2EfKEBAAAAAElFTkSuQmCC) ➜ ![80ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAwxJREFUeF7tmL9rFUEQx78pUvgaC1NYpQrYaZPOgFpI7IQgWEQQbEJK/4CIP0qL1DapkkKRYGFhKhG0EIKgIZBAsAioKSIiiBYWCesy3GTf3ssNCWGd/V53y9y9ne9nvjN7b+j+HvbAqyoFhgi9Kt7/kiX0+pgTeoXMCZ3Qa1Sgwpw50wm9QgUqTJlOJ/QKFagwZTqd0CtUoMKU6XRCr1CBClOm0wm9QgUqTJlOJ3TfCnxaApZvxRzHZ4DJeWD4FNC27lWN4p2ugaSwBMrv78DyNLC1ElfOXgBuPAVGzjXYtt8BCxPNvUD/9iG/HiJX7gKrT5pn7rwFRi8eLIV0f1ceApfmyi6XoqGngoqU2qUpcIlJwb95BLy+d9DhITa3/vdPP3B5rwbftr/SwRcLXQs/tQicnwbErRqoQBubBKaWIhpxvYiv36WBtK0LTPmd06NNEUjB/dwGnt8Edj4Csr/0Od1pSvL+fwFd3CWu/rUT27eGIcIHcUX8AGh8FnhxO8LR19XHwNpi/3p4z4/PsSvoApGCk+La3YhjQe57ZwDddXKjoBTwxUIPAqVzWEQTwG0ia0CXHwAvZ7pDv74AfHkfZ7kupN3N6OxwhYL7uhoPhXrU5LpTKaD1PoqGrmeubFo7S0AEF2tnpa4c7jXt+bD2rgtpEPT1Z/1nBEI/YonnxE9nem+kmd/HBb0NHJ1+RKBdHhfAun1q5wcXjl1roLfN9PAtrj+/DnN62+Eu7R5br2J750zvQrNjTO6knnP/oNO7FMJxnt6laPRoSU/vuhA6pnuiYcXO9EHfyvqTTYuvldPCW6Fbv/1TYrrrnCjNjj9WLHTZvzg5d5CTtRR86jQr9PDeLv/y5Q6apQMPey4eesfiZZhBAUI3iOUllNC9kDTkQegGsbyEEroXkoY8CN0glpdQQvdC0pAHoRvE8hJK6F5IGvIgdINYXkIJ3QtJQx6EbhDLSyiheyFpyIPQDWJ5CSV0LyQNeRC6QSwvoYTuhaQhD0I3iOUllNC9kDTkQegGsbyEEroXkoY89gHJwDztzflPRwAAAABJRU5ErkJggg==)

2. `desaturate()`

   降低一定数值的颜色饱和度。

   Example: `desaturate(hsl(90, 80%, 50%), 20%)`

   Output: `#80cc33 // hsl(90, 60%, 50%)`

   ![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA1lJREFUeF7tmD+MzFEQx2ePdWfPuT0UhGhcIQoNmmu4KK4j2Sg0Go3gGoVChUohQkFE40+0solSIZxCc1cpVK6gEBFyG7eWc9zJ+8n8Mt7O+5nd5Ccv82bLX+bNe/P9vPnztnLp/bZVsF9SClQMelK8s2ANenrMDXqCzA26QU9RgQRjtp5u0BNUIMGQLdMNeoIKJBiyZbpBT1CBBEO2TDfoCSqQYMiW6QY9QQUSDNky3aAnqECCIUeZ6a+a36A53cpx7D9Rg6nLG6E6VMm/dRZWoHm2BW+eL2Xftu6pwrE7ddiya23PGH1fzsHJx5th54F1f/n6NP8THp1qwY591a7zOMOZ6214dnUxX8P56PlwJSyIDroPHGOm4DlI/YJ/N/sD7h793CUtBebv51/C5e+r8OTiF5h72Ony07hZh72N9SWg699lVNCpeCgWQqGZjBk1fmgQGrfqWfSY9ZPnR+DguQ0iRSjM0DrM7g+vl4OVhzsjXl48Y21sQHSm/2EULXTMNATT/riSle/R7WvyrKJZhCLTLPQz0G8B3BpfdIQ+cWb4z+WaboGf6Zwf/9z9tJ2yLkBU0F2QoXKLgGl20hKM67jsp+JxFcP5Xnj7K+/HoawPXRL8Tn3TOGLr7dFB5wYiWiJpuS2CPnu/k0GkWYlwHNSJ08PBPuzOwIEPQQ/NGHjZDHpBzaLihXp6bdNA3r9D0I9cG4UXN9rsYOW2dxfh8IUReHplMbPhLgbXi4vagd/7j98bg7kHHcC2ZOU9AB5Lot8zcXBzF2F8cjCHHurpFCi3Ffp/eftrVg2oH79N0AFMMgPgfkV+yurVUr9RlXduCuayv2h6R4BoUzTNcxM2ruP+GwhBdwPj/MwS7J4aynSXvAqkgMqwiwp60XuXDkncM8qJE+r9oUGuqBdzfbgIOvdOj/G55rSICjrC8f/Z4sTzwXM2HFTf7l/POnpheoHOVYoysrYfn1FC7ycQWyNXwKDLtVJjadDVoJQHYtDlWqmxNOhqUMoDMehyrdRYGnQ1KOWBGHS5VmosDboalPJADLpcKzWWBl0NSnkgBl2ulRpLg64GpTwQgy7XSo2lQVeDUh6IQZdrpcbSoKtBKQ/EoMu1UmNp0NWglAdi0OVaqbE06GpQygP5DZ/K0e2EfKEBAAAAAElFTkSuQmCC) ➜ ![80cc33](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAy9JREFUeF7tmL1rVEEUxW/EjxjEhLBCEkSNiMYmFrGxCmohYuXiX6AWFnZWgoKgYGVnYWH8B5TtQrBQSeMHpjGFXyQGRVYhGtYlrJENUebBfUyGmfXl7VsYOOdV2ZeZeXPP7565972uGzNjf4UXlAJdhA7FOwmW0PGYEzogc0IndEQFAGNmTSd0QAUAQ6bTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wkdUAHAkKN1+o/PK/Lo6if5/vF3gmXg4HY5d3u/lPZ2p5iaf9bk8Z2vMlNZTO+dnzgke47sKAzl9P1v8uxeNV3vwLGdUr41LD29m9N7s1NLUrm+kP727bWwDRWwUJTQXeAapy2mD7iOKwp849eqVK4tyNyL+jqpbfChfcQMPkro6pyj5V1y6spuaa6speKXbw7L6Ol+0TEqbu/A1tT1Om/Ltk1t+cJAf/e0JmNnS8k6djJqYhnos5NLMnqmX8zz7ETRvba1iQ5Mjhr68UtDMn5xMAlbj1kVUn/bY768WZYHFz6IewTrfdXP/b97hNtr2pqH1rfHaGKYe2456gC/XEtGCT10rKqDTaRay203+QR3gZq5Ct387Tu+fYnkKzF6z1eOiioxuaj+Z1KU0M2eXXeaeypk6Ah1oTdqq4nzzWUnx/yrugyO9Mjrh4tJk+b2CvMv6zIy3pfM8+3DPQl80IsqMTDQ1Z2hmn74RF8mp1ffNpKu2gfAbsCy1l4bbmiOnZChMtEJkBtZMzqnq2jLP5vraqI6zgA8eXlIntytJq9qrWr63PN6odDt3qIVULcRbbeh3AjQLGOjhW5ek2w3ue43XbVxsa97VyD20ZzneH8/XZPSvu7024DP6eYZ5tJvA3R6lrTzjHE/dthDFF6o2cvyLq+NnKn59gcgfY4mTWgfdvfvq/lmHb6n54DfqpHT5VzwIaHdDt6u8b7k0cTK0qD55vu+2uWQoGNTojveOxYpF04VIHTAZCB0QgdUADBkOp3QARUADJlOJ3RABQBDptMJHVABwJDpdEIHVAAwZDqd0AEVAAyZTid0QAUAQ6bTCR1QAcCQ6XRCB1QAMGQ6HRD6PwJTp+2d3Y7RAAAAAElFTkSuQmCC)

3. `lighten()`

   增加一定数值的颜色亮度。

   Example: `lighten(hsl(90, 80%, 50%), 20%)`

   Output: `#b3f075 // hsl(90, 80%, 70%)`

   ![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA1lJREFUeF7tmD+MzFEQx2ePdWfPuT0UhGhcIQoNmmu4KK4j2Sg0Go3gGoVChUohQkFE40+0solSIZxCc1cpVK6gEBFyG7eWc9zJ+8n8Mt7O+5nd5Ccv82bLX+bNe/P9vPnztnLp/bZVsF9SClQMelK8s2ANenrMDXqCzA26QU9RgQRjtp5u0BNUIMGQLdMNeoIKJBiyZbpBT1CBBEO2TDfoCSqQYMiW6QY9QQUSDNky3aAnqECCIUeZ6a+a36A53cpx7D9Rg6nLG6E6VMm/dRZWoHm2BW+eL2Xftu6pwrE7ddiya23PGH1fzsHJx5th54F1f/n6NP8THp1qwY591a7zOMOZ6214dnUxX8P56PlwJSyIDroPHGOm4DlI/YJ/N/sD7h793CUtBebv51/C5e+r8OTiF5h72Ony07hZh72N9SWg699lVNCpeCgWQqGZjBk1fmgQGrfqWfSY9ZPnR+DguQ0iRSjM0DrM7g+vl4OVhzsjXl48Y21sQHSm/2EULXTMNATT/riSle/R7WvyrKJZhCLTLPQz0G8B3BpfdIQ+cWb4z+WaboGf6Zwf/9z9tJ2yLkBU0F2QoXKLgGl20hKM67jsp+JxFcP5Xnj7K+/HoawPXRL8Tn3TOGLr7dFB5wYiWiJpuS2CPnu/k0GkWYlwHNSJ08PBPuzOwIEPQQ/NGHjZDHpBzaLihXp6bdNA3r9D0I9cG4UXN9rsYOW2dxfh8IUReHplMbPhLgbXi4vagd/7j98bg7kHHcC2ZOU9AB5Lot8zcXBzF2F8cjCHHurpFCi3Ffp/eftrVg2oH79N0AFMMgPgfkV+yurVUr9RlXduCuayv2h6R4BoUzTNcxM2ruP+GwhBdwPj/MwS7J4aynSXvAqkgMqwiwp60XuXDkncM8qJE+r9oUGuqBdzfbgIOvdOj/G55rSICjrC8f/Z4sTzwXM2HFTf7l/POnpheoHOVYoysrYfn1FC7ycQWyNXwKDLtVJjadDVoJQHYtDlWqmxNOhqUMoDMehyrdRYGnQ1KOWBGHS5VmosDboalPJADLpcKzWWBl0NSnkgBl2ulRpLg64GpTwQgy7XSo2lQVeDUh6IQZdrpcbSoKtBKQ/EoMu1UmNp0NWglAdi0OVaqbE06GpQygP5DZ/K0e2EfKEBAAAAAElFTkSuQmCC) ➜ ![b3f075](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAvdJREFUeF7tmD9IllEUxo8QkUMORi0S5ODQHyiaChrKQARpEkpqcrClsTJqaqlIClrLwakQl0ARXJKgoCZB6B9EODnUIEFDEUFxXzgf57ue+36f+sZ3P5/nXRy+++88v/ucc64dc9+v/xV+UAp0EDoU7yJYQsdjTuiAzAmd0BEVAIyZNZ3QARUADJlOJ3RABQBDptMJHVABwJDpdEIHVAAwZDqd0AEVAAyZTif01ivwY+2nPBibl6UXK3L1yZCcPn+okkNNT7yRp3dfF2tdunVKRsZPVrJuOy6SndM3At2OVfE9oC9nPsjDy/M1Pjomnt97ZJ/cmDonPX3d4q1tAduxH9+uyvjgs3X8c71cbQ29GbF///ojkzcXZWFquc7hKagKs2tPZy3jeG4m9Apz3EacHqCH7+CJnuKvOvr42V65Njkku7s76xxry4Wmex0b5mtZKXOozrNr6b5VlqMKJV23VPZODyfW1GxheqIoEIWWygR3Zi/Iq+efCvd78AZHj8rYvX7ZuWtH3Ta6Xvw7oW/xijaqpbHgtkELW9vfU9BvzwzL7OOlolmcWLhYyxQ63rtc9lx2TtgzPkPuzWLWTrcA1U22lqYEt1C8crH6eU3uj87JyrtvTUPX/b0s4EHPGXzW0FPwYqdpcvEuhgc95dqU08tcXlZmGpWjLSbFTU9vG+gpd9rIPTiNoDdT070GsUxxPWsYo0/ATRP6DxOzhm5TqaZQTe9793fJ4vR76R85XGu4mnW6LQte924vQurJpyzCpfqy/FWOnTlQwxO/DMIrIqcva+ieUNqZWxjxOPvkSj0Bbeaw8+OU3CjDlDWeqTLU6guQNfQrjwYKF4WnldcYxf9pC2Pit3LZuz8G79VgrfNxA2nBxeuUjW018LB/dtBzEGW7n4HQtzthJz5CJ3RABQBDptMJHVABwJDpdEIHVAAwZDqd0AEVAAyZTid0QAUAQ6bTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wGh/wMWnv7PO+0WTQAAAABJRU5ErkJggg==)

4. `darken()`

   降低一定数值的颜色亮度。

   Example: `darken(hsl(90, 80%, 50%), 20%)`

   Output: `#4d8a0f // hsl(90, 80%, 30%)`

   ![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA1lJREFUeF7tmD+MzFEQx2ePdWfPuT0UhGhcIQoNmmu4KK4j2Sg0Go3gGoVChUohQkFE40+0solSIZxCc1cpVK6gEBFyG7eWc9zJ+8n8Mt7O+5nd5Ccv82bLX+bNe/P9vPnztnLp/bZVsF9SClQMelK8s2ANenrMDXqCzA26QU9RgQRjtp5u0BNUIMGQLdMNeoIKJBiyZbpBT1CBBEO2TDfoCSqQYMiW6QY9QQUSDNky3aAnqECCIUeZ6a+a36A53cpx7D9Rg6nLG6E6VMm/dRZWoHm2BW+eL2Xftu6pwrE7ddiya23PGH1fzsHJx5th54F1f/n6NP8THp1qwY591a7zOMOZ6214dnUxX8P56PlwJSyIDroPHGOm4DlI/YJ/N/sD7h793CUtBebv51/C5e+r8OTiF5h72Ony07hZh72N9SWg699lVNCpeCgWQqGZjBk1fmgQGrfqWfSY9ZPnR+DguQ0iRSjM0DrM7g+vl4OVhzsjXl48Y21sQHSm/2EULXTMNATT/riSle/R7WvyrKJZhCLTLPQz0G8B3BpfdIQ+cWb4z+WaboGf6Zwf/9z9tJ2yLkBU0F2QoXKLgGl20hKM67jsp+JxFcP5Xnj7K+/HoawPXRL8Tn3TOGLr7dFB5wYiWiJpuS2CPnu/k0GkWYlwHNSJ08PBPuzOwIEPQQ/NGHjZDHpBzaLihXp6bdNA3r9D0I9cG4UXN9rsYOW2dxfh8IUReHplMbPhLgbXi4vagd/7j98bg7kHHcC2ZOU9AB5Lot8zcXBzF2F8cjCHHurpFCi3Ffp/eftrVg2oH79N0AFMMgPgfkV+yurVUr9RlXduCuayv2h6R4BoUzTNcxM2ruP+GwhBdwPj/MwS7J4aynSXvAqkgMqwiwp60XuXDkncM8qJE+r9oUGuqBdzfbgIOvdOj/G55rSICjrC8f/Z4sTzwXM2HFTf7l/POnpheoHOVYoysrYfn1FC7ycQWyNXwKDLtVJjadDVoJQHYtDlWqmxNOhqUMoDMehyrdRYGnQ1KOWBGHS5VmosDboalPJADLpcKzWWBl0NSnkgBl2ulRpLg64GpTwQgy7XSo2lQVeDUh6IQZdrpcbSoKtBKQ/EoMu1UmNp0NWglAdi0OVaqbE06GpQygP5DZ/K0e2EfKEBAAAAAElFTkSuQmCC) ➜ ![4d8a0f](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAyJJREFUeF7tmLtuE1EQhmdZO4lX4RZhCUQRCpQUXBMaJFpElwIhECCegEfhAXgCBA9Ah0LJpSFUFDEpHCQrhbkoECzb8QXNRrOaHM5unJWjbDT/VpZ8zpwz/3f+ObMbLD07OSQ8phQIAN0U7zhZQLfHHNANMgd0QLeogMGccacDukEFDKYMpwO6QQUMpgynA7pBBQymDKcDukEFDKYMpwO6QQUMpgynA/rRVqC/PaQvr9u0/rFLi48iOr9QPpSEtpoD+vSiRb83+nTiXEg3nkQ0XT12KHvxLXoknF5b7tDqmzbN3pygS0tTFJYDr4Bp0LutIa28bFGz1kvm3Xo6TTMXwn2B0PFlohvHXQvQ9yXxzmDtmjzQfcDTgGVtzwfcF+dnvU/vnm8V0uGy30I73RU6D3SBUJ0r0eLjiMIyJVfA/J0pmrs9OdJRbHzeppVXrQRm5VSQxNH7knGy3kTkr0ojLXpAgwoNXQRkOL7y7rr46r0KbTb6u+50ge4Do6HLFaJ11qVb/tdz9IG6dj+ir293+gn97HVQD4hrZtjCQhdBWeQzF0txydQC6rLvy1AaubSyrJ2YNkbuY+1q3SDKHnj9hYcR1T90AD3vKRYxT8+GceO22Rj8B12cpxslOSi8rg8Od9Py7NXdS3weV50vJY1gGnTp0FHec1CXkt35M0xeddwSzWHl1UyXW1/37kJIu9NlnLtlhnz2csn7KqidDug5YMuUNPF1yOsPIvr1rReX0izoGpa+nwXW5PEgbu6aq724SfPd8S70tDud43DTBqfngD8KdAbx98cgbu703eyWdw1dw3LB1N9341gyRjeIUs6zuves2OjecxwCnuLrwDXgrEYua5zAcg8a9wlcBfhjjkBPe993P77A6Tkhu9N80PVh4N8s/pW7Faott3fB4v98nb5uyNzuna+C72u92P163Chf2wB9TNARZrwKFPY9fbxpIppWANANngdAB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTPkfmjDDCzxq0b4AAAAASUVORK5CYII=)

5. `fadein()`

   降低颜色的透明度（或增加不透明度），令其更不透明。

   Example: `fadein(hsla(90, 90%, 50%, 0.5), 10%)`

   Output: `rgba(128, 242, 13, 0.6) // hsla(90, 90%, 50%, 0.6)`

6. `fadeout()`

   增加颜色的透明度（或降低不透明度），令其更透明。

   Example: `fadeout(hsla(90, 90%, 50%, 0.5), 10%)`

   Output: `rgba(128, 242, 13, 0.4) // hsla(90, 90%, 50%, 0.4)`

7. `fade()`

   给颜色（包括不透明的颜色）设定一定数值的透明度。

   Example: `fade(hsl(90, 90%, 50%), 10%)`

   Output: `rgba(128, 242, 13, 0.1) //hsla(90, 90%, 50%, 0.1)`

8. `spin()`

   任意方向旋转颜色的色相角度。

   Example:

   ```less
   spin(hsl(10, 90%, 50%), 30)
   spin(hsl(10, 90%, 50%), -30)
   ```

   Output:

   ```css
   #f2a60d // hsl(40, 90%, 50%)
   #f20d59 // hsl(340, 90%, 50%)
   ```

   ![f2330d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAv9JREFUeF7tmE9r1GAQxmf/pM0u/oHFHioirKCeRMSLd6EfQfRWar+DiGcRP4A3FY/iUfEiePfuSaT2IkopLkuR7rYbd+WNTpxO3zdNwhYi8+xxeTPJPL95ZiZp7Fw/OSP8TCnQAHRTvNNkAd0ec0A3yBzQAd2iAgZzxkwHdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB/T6KDBIZnR3c0zvd5L0od5d7tKNE636PGDOk3z4+YtWPu3SzVNtetaPqddu1Oq5a+n00ZTo/tcxPd+eZGI56Fe7rUP/ry1F9OhcTJ3mP10ff9+nh9/2sj984r8aTGh9c5ydudJp0osLHboYi0BEpM89OLtI95YXciECeoUa/zye0uqXEX0cTQ84XMPk0BK87hB8RoL3FZU7p8Fr4BzrKPCAXgE6i6YhPNnap5XT7cyNEgq3fwf99TCh1TNRemdfATnoLwcTut2L0g4hC+VpP6ZbvejAdfwf3y/UFThVQC8J3efm0GxkcfNmfhEAXBguDrd433WyOOSOIYvPPev6UkR3NkaY6UXZl4HOZ3VRyGLwtW3dAfjZfCDl6JBjgd0fGjkuJha5otSJKNTeZQgpNgPQ7VWe13NYtn3fbsDx86Bf67ay3YPjy8IA9DlB14uaBq5vI+GGzsqYDI9bdh708wvN9NVMz/giI6WEHHM/WstXtpDTJcAyLmLX5m3dGvKb4Z9XOnkfPdMdDUCfU036oMu26Xs351u/HSZ0KW5mG77P6S6++/HHHp/TfddxYXAh/EhmWXvnLoL2XrEIfNB9M1iG5yUs9G4tHasXPY6j23RoSfMB9qVaphtVlKrSZf9Ney8K/agFzank+4ATAqTB671Af+hxI6S/2Dg0GirROaaLagn9mHJF2L8KALrBUgB0QDeogMGU4XRAN6iAwZThdEA3qIDBlOF0QDeogMGU4XRAN6iAwZThdEA3qIDBlOF0QDeogMGU4XRAN6iAwZThdEA3qIDBlOF0g9B/A5A7seNhIBSlAAAAAElFTkSuQmCC) ➜ ![f2a60d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAwJJREFUeF7tmD1oFFEUhe9usslGtNFKEMRCO2Mr2Bm0sbLQThR/sFBiJSI2aUTUTkwXxSBYKGhhJ6YUBAtROwVFGyFBISpmNz+z8kbucH375pcNjJyTbjNv7sw93z333TeNHw829YR/UAo0CB2Kd5wsoeMxJ3RA5oRO6IgKAObMPZ3QARUATJlOJ3RABQBTptMJHVABwJTpdEIHVAAwZTqd0AEVAEyZTif0eilw/cmyXH3UjV/qypFRuXR4pF4vmPI2D1+syOnpjpycaMm1Y20Zq9lr19bpKpzqqtD9/+/e3pR7k2Oyc2uzdEF8/9WTU7c7Mvd2Nbn32dQG2btrKP69tCxy+X5H7s6tBK+nPZDQS6P4V2zr8Jfv1+Tg1O++iFXAp8VS6CHg+mBbGKH0CL0CdOvAmXNtObqvFUdxoL4sRMnvD18jOXFrSd59jkq1fxs/bdtQcFpQ27Y0E9fntW1CLwk9z4E2XAheqGVPjA/LnfNt2byxEd9eBIrOE6FO48ezs4criD07huTCDPf0wujLQLdrteVa99uHWncqJNdFPs33+oZF29ptp9HYLq6bI6z7QwnmdYTCogx4YS0HubT2bnO3wLPE1Vjzi1EhUM7ZZw60kgEvC/q3n71kxtB19t0JvUS15kH326k9FqUNYHl7s7Z817qnz7blxuNuPLVnQX/9cS0+mlXZPkrIMfCl/5XTfaD+FG2v6zXf6e5oZ9u7HRLdycCHnrWnP3+zSuiDKsk0p/sTtX82t/cpdN0G7LHOuloHPC0EbclPX/39wBLqEFoIoZmC7b1iFYSgZ52b3WMciMlDI30fU/aPD8vCYhS/iX7ECU34/hk8bY0tnqw4Lh739BIFUBW6+0xrp3fXqm8eH5WLs13RQU67g19EoQ88PtQia+yJgNBLQOfS9VWgloPc+qbM6IQOWAOETuiACgCmTKcTOqACgCnT6YQOqABgynQ6oQMqAJgynU7ogAoApkynEzqgAoAp0+mEDqgAYMp0OqEDKgCYMp1O6IAKAKb8B0RF5Fn5h+/6AAAAAElFTkSuQmCC)

   ![f2330d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAv9JREFUeF7tmE9r1GAQxmf/pM0u/oHFHioirKCeRMSLd6EfQfRWar+DiGcRP4A3FY/iUfEiePfuSaT2IkopLkuR7rYbd+WNTpxO3zdNwhYi8+xxeTPJPL95ZiZp7Fw/OSP8TCnQAHRTvNNkAd0ec0A3yBzQAd2iAgZzxkwHdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB/T6KDBIZnR3c0zvd5L0od5d7tKNE636PGDOk3z4+YtWPu3SzVNtetaPqddu1Oq5a+n00ZTo/tcxPd+eZGI56Fe7rUP/ry1F9OhcTJ3mP10ff9+nh9/2sj984r8aTGh9c5ydudJp0osLHboYi0BEpM89OLtI95YXciECeoUa/zye0uqXEX0cTQ84XMPk0BK87hB8RoL3FZU7p8Fr4BzrKPCAXgE6i6YhPNnap5XT7cyNEgq3fwf99TCh1TNRemdfATnoLwcTut2L0g4hC+VpP6ZbvejAdfwf3y/UFThVQC8J3efm0GxkcfNmfhEAXBguDrd433WyOOSOIYvPPev6UkR3NkaY6UXZl4HOZ3VRyGLwtW3dAfjZfCDl6JBjgd0fGjkuJha5otSJKNTeZQgpNgPQ7VWe13NYtn3fbsDx86Bf67ay3YPjy8IA9DlB14uaBq5vI+GGzsqYDI9bdh708wvN9NVMz/giI6WEHHM/WstXtpDTJcAyLmLX5m3dGvKb4Z9XOnkfPdMdDUCfU036oMu26Xs351u/HSZ0KW5mG77P6S6++/HHHp/TfddxYXAh/EhmWXvnLoL2XrEIfNB9M1iG5yUs9G4tHasXPY6j23RoSfMB9qVaphtVlKrSZf9Ney8K/agFzank+4ATAqTB671Af+hxI6S/2Dg0GirROaaLagn9mHJF2L8KALrBUgB0QDeogMGU4XRAN6iAwZThdEA3qIDBlOF0QDeogMGU4XRAN6iAwZThdEA3qIDBlOF0QDeogMGU4XRAN6iAwZThdEA3qIDBlOF0g9B/A5A7seNhIBSlAAAAAElFTkSuQmCC) ➜ ![f20d59](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxlJREFUeF7tmL9rVEEQx+dyF/NOUCRgEbCw0M4QEBWDWClp0gt2ov4PIhZiIRLBwiKVUewE/wRRO4kgCElMIUlhF1CTSArvV+5O9sEcc5udd48kFzc33yvv9u2b+X7mO7N7ha1jN9uEjykFCoBuineaLKDbYw7oBpkDOqBbVMBgzpjpgG5QAYMpw+mAblABgynD6YBuUAGDKcPpgG5QAYMpw+mAblABgynD6YAejwIb7Rrdqc7Th+21NKh3R6/T5eLJvgVYoSbdr36lV41Vmksm6cbw6b69639vHKXTJQAWyEGfKI52wPD3t4fP0JPkPJWp2NHSL5jxoRP0unyFzg4dV/XWoM/Uv9Hj2tKO5/wi9Nf1u0j3UjhRQl9pbdGtyidaav3pcrgGQIL3gbM4vcDvFnqoQPmdsXaMKKF/bv6iqb/vyQc1W/9OU6WxjmPfNn7Q3ep8V/vnwrhWGqOXyWT6G4+JByPjdO/IuaBJQtD5uy/N32qnCMXKcXEMo4WRvRhz35+NDnrIzZp4LDjPfNn+pcsYQlZHeJ5cpIXmZtdMzwM9a++frUrPsbLvRHNseKihZ7lazlQuDi6e9XatMz5CGnHBaKNC7s3QZVfyi7GfB9AcjHcsiQ66i1Br7zJ62REYknYW8KG/aKykhzMNVC/o8jahFYY8gAJ6jtLMgu6LLNu4/E1z+mxyiZ7WltM2Lmd8niub3F8+K4vNpfemfJXmGquE9p4DNi/RoEtxQ3NeQtFm+sNkgh5VF3YF3cUXmuF+an5nwUEuB/wQdOnE0N2ct82a81wI/hoHRc5hXrfY2kzv/3y/DzndxfVxe42mS6fSELRukCPtA1tyaGa630J9hbida+tkZ5CAsw5y2jq5l3ZPj/W65vIdOOguKR98CIAE6g50z5ILNFNfTv/2DY0GLg6/y4SgZ3WiA7NzxouihB6DMIMcA6APMl0lN0AHdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU43CP0fN33dTyk9vTwAAAAASUVORK5CYII=)

9. `mix()`

   根据比例混合两种颜色，包括计算不透明度。

   Example:

   ```less
   mix(#ff0000, #0000ff, 50%)
   mix(rgba(100,0,0,1.0), rgba(0,100,0,0.5), 50%)
   ```

   Output:

   ```css
   #800080
   rgba(75, 25, 0, 0.75)
   ```

   ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) + ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ➜ ![800080](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAvNJREFUeF7tmD9IHFEQxkcOLtVVYmXjFWkS/FeKRTBJI4JgG/BSxEorK7sgKUJSpQhaJUUukC4Igl0SE1QEG0VRy7vGyvKqHBwJ78E8xpfdO7k74oPv226Xt7sz32++N7M7sC7rf4QHlAIDhA7F2ydL6HjMCR2QOaETOqICgDmzpxM6oAKAKdPphA6oAGDKdDqhAyoAmDKdTuiACgCmTKcTOqACgCnT6YQOqABgykk6ffl8WYYeDAUc1xfXsvlw8wae+Q/zMvliMlxrXDVkq7IltR+1cG1scUzmNuakWCr6a61mSw7eHsjuy92wpvy4LAvVBSkNl8K144/Hsr20feN9/YophRpLDnosropkwcfAdY0FHwPXNRZ8FnBdZ8H3K6YUgLsYkoJuQanolW8VKT8pS7PRlJ2VHa+bulcLwRaB3qeg2t2nz9ZCqP+sB9drAbkdQN/Xa0yEnqGAQi/cK4RteObVjEyvTUvrd8tDH7w/6M/doVu1dWzte032Xu8FeO68+rTq1ytkBTr7fta3kaxdJC6WXmPSGFIAn5TTLZhYHHWZulqhnH4+9UvV2Q7g/pv9f9zp1th7jzaOZHxx3PdyWxhaZLaotFh6iSmeSe4SfnLQs/qs7dXxdp8F/fLrpd8NCsWC2N5soZ99OZPRZ6N+yOsEvR8xEXpOmavLLKwY8sijET+1/y+nu1DjAuomJkLPga5i2h5rXeZc6w4HPW8Kd649+XQStve8nn747lCmVqf89t6up088n/CDZK8xsad3gG5dHLu//queO73bQmg3vWshtJveFXJWO+k2prvs4/bdSfX0vG9rF7AthLzByrrRgrEJ2+fc5lu+nzEReo4CnYYmvS0Gn/XXLgYfzwHuWd3+tcv6A3ibmFIAn5TTUxAEIQZCR6Ac5UjohA6oAGDKdDqhAyoAmDKdTuiACgCmTKcTOqACgCnT6YQOqABgynQ6oQMqAJgynU7ogAoApkynEzqgAoAp0+mEDqgAYMp0OiD0v7mOJS5yP9UlAAAAAElFTkSuQmCC)

10. `greyscale()`

    完全移除颜色的饱和度，与desaturate（@color，100%）函数效果相同。

    Example: `greyscale(hsl(90, 90%, 50%))`

    Output: `#808080 // hsl(90, 0%, 50%)`

    ![80f20d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxJJREFUeF7tmL9rU1EUx89L+2ISEH91EJFCoR0rCBl0UlHqXkRwK22HQoeOUrp0EfF/0OIoIsHBxYKIm4OLOrZTERRRoVKStGkTua+c5+nJvc+kSaF4vhnDfffe8/2c7znnvWj598kW4WdKgQjQTfFOggV0e8wB3SBzQAd0iwoYjBk9HdANKmAwZDgd0A0qYDBkOB3QDSpgMGQ4HdANKmAwZDgd0A0qYDBkOB3Q/08FPj1vUGW2ngRXno7p9sMCxcWji3Xj/R6tTFRp9OYgTT4pUOlsdHSHHWLnY+t0CSoEq/qrRZWZOq2/2U1CPz+eoztPizQ0lkulYAD8B0P/+nEfjPxNr5Zo+MrAgf/0PW4snaBr9/OZUgP6ITJRC62BOZdq4LxGg3/3aIfePtg+4PAfa016MVWjb5+bbbeT4EP3+Bd4QO8SeqNG9HqxTh9WGjT5uECX7sbEIkqgDJNLqDuGXc9Q5F4SlIO+trpLV+f3HSsTiCvB5pe/icH34CTwVRQZJqD3AJ1dx1C2vjeT8n3qYq4tMdwxDMWBK8/k6eVcu5sZoLyWTA5d/mVflskRqghufXk2pmf3aujp3bDXfZifZWAh8aXDri/l6dVCva2E+6DLcq9dLQc/XxXiiuOLD4NcN9SJSIspBZSQpON0WY2LUVoRQn1Y7iXP8M0CGvqFywPpbOBrKYDeIXTp4lBPL52L0v7dC3Q5qGlAslXwK56Gfno4l7wB6B6Pnt4hbF7Ggun3aXaeS4TRW4MpdFmuNSi3Jw+F2umykvhKPu+V1dPd/oDeJWDfct+k7nN/1vTOEEPTu5wZfO/m7l5ZfZ4TofqzlZZ335ko7x0mhASlH5FlNPSuLYUOQc8avmSFCa3zAcYg1yHgrGVZgxw/p8FrZ/UK3Z2j76FbgU5S10bOjETJZ184vQ+JgC36o8Cx/fben/Cwi08BQDeYF4AO6AYVMBgynA7oBhUwGDKcDugGFTAYMpwO6AYVMBgynA7oBhUwGDKcDugGFTAYMpwO6AYVMBgynA7oBhUwGDKcDugGFTAY8h9+ffjt05Lh2gAAAABJRU5ErkJggg==) ➜ ![808080](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAsJJREFUeF7tmCGOMkEQhWsFhpBgECgUBoPCE4LAE07ASTgJJyB4BCF4FAaDQiGQBIPYPzVJTWo73T8kTLKdfW/c9vbMVL2vXlcxX4vF4lt4QSnwRehQvItkCR2POaEDMid0QkdUADBn9nRCB1QAMGU6ndABFQBMmU4ndEAFAFOm0wkdUAHAlOl0QgdUADBlOp3QARUATDlLp+/3e9ntdiWO0Wgkw+HwB57L5SLL5bJcGwwGMplMpFarlWu3201Wq5Vcr9dird1uy2w2k1arVe55Pp+y2WzkcDiUa/P5XDqdzo/3VRVTDjWWHfRQXBPJgw+B2x4PPgRuezz4GHDb58FXFVMOwDWGrKA/Hg9Zr9dyPp/FRD8ej8Vat9uV6XRa6GZ7rBB8EYT3WSEoYLtPn9Pv98WebYXQbDZL18fu+zQmQo8oYNDv93t5DJtjG41GAV336JGtlx3V3rEhUH9CmGNtj/0dO0XCIqsiJkJPKGDuC/9tLjNXG5R6vV5sDQH6U8M/yxysa9bLrQh0zYrMF1VVMRF6QoFYn/W9OjzuU9D18bHeb8Xji+IV9CpjygF8Vj3dD1+p/ql7dGp/5XRzfqqn93q9t5yukOwXwKcx5QA8u0HOXOx7rHeZiq6XQk9N4epaLQgd2nwf9s7XQhiPx7Ldboufav/r6TpU6rM+jUkHx1yurJweO7pD9+tv7NT0boWgR344qfu+b+4/nU7Fvtj0bpCrisl/G/ht+FlBTw1fKpI/zlODlXdjao8+y3p46n3+FKkypt+Gbe/PCroG9WpossBDqO98tdN7w69tIdR3v9rFvgC+E1MO4LODnoMofz0GQv/rhCP5ETqhAyoAmDKdTuiACgCmTKcTOqACgCnT6YQOqABgynQ6oQMqAJgynU7ogAoApkynEzqgAoAp0+mEDqgAYMp0OqEDKgCYMp0OCP0fY+0vLuYuMPEAAAAASUVORK5CYII=)

11. `contrast()`

    旋转两种颜色相比较，得出哪种颜色的对比度更大就倾向于对比度最大的颜色。

    Example:

    ```less
    p {
        a: contrast(#bbbbbb);
        b: contrast(#222222, #101010);
        c: contrast(#222222, #101010, #dddddd);
        d: contrast(hsl(90, 100%, 50%), #000000, #ffffff, 30%);
        e: contrast(hsl(90, 100%, 50%), #000000, #ffffff, 80%);
    }
    ```

    Output:

    ```css
    p {
        a: #000000 // black
        b: #ffffff // white
        c: #dddddd
        d: #000000 // black
        e: #ffffff // white
    }
    ```

## 颜色混合函数

1. `multiply()`

   分别将两种颜色的红绿蓝三种值做乘法运算，然后再除以255，输出结果更深的颜色（对应ps中的“变暗/正片叠底”）。

   **Examples**:

   ```less
   multiply(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC)

   ```less
   multiply(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![331400](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAz1JREFUeF7tmD1oVEEQxyeFKUQ7CQTFIiCCYuUHCNFCrbRIEUkjGiGCxiDYJRZRNIVJIwFJtNAiUZtgCj+wMinUShsLFVQ4/OA89RQLRfBSKPNglsm6+3h3cmRl/q8Jebdvduf/m//svteyeRX9JlymFGgBdFO8s2QB3R5zQDfIHNAB3aICBnPGng7oBhUwmDKcDugGFTCYMpwO6AYVMJgynA7oBhUwmDKcDugGFTCYMpwO6AYVMJhyck4/NnSOek8M0rLWVofj8YM5Ot69x/2/t+cgDY1N0PIVK9290ssX1NO5MYhwePwKdR3oo7wx/ODk7H3atnM3/fzxnUYHB+jezDUXT36TG6FYMo+M+Vwp05mBQ/Tk4XxSpZUcdF9cUUuD98WNgfALKA+6LiQfemxNOl5sTSmCTw76heu36dWzp3R59HTGcubRc+pYv4G0eCwwXyMnj2R/Qw4NQciDLvNwPA1dF44Unsy3UKvR1MUxeld67TqPzKHnv3XjqltrCpZPDroWZeuOXXR2Ypra2leT3+L1OAEWch4LvmnL9qxwYtAFUPltidra19BC7Zdr7/KbLgTdFTh+9eOHbEvii4uAC7bo2peiCJKDrsXK2z+1M3lcXhsNFYXEFid/+1qluTs3af/h/kXQxdU6vg+08v5Ndmbwt4W8eZcCtsz5X0CXNiotX7d9LV6sjcbEF8dyDD64re1YlzlWOz20vfjQ+fnQARDQGyxtES50opaQ/h6ri0MXiG7vGpwUi7heQ4fTGwT3L48JCL1f+vH8PVYOeDIub8/PWxsXyZdPlb9c7M/HMbi9646EPb0gdRazu/co9e3rdE/4Tq9WytR/aoQunR9277+NOD32iqWXytDn78667wb+6V26Dz8j3w3803toayooR9OGJbWnhz66SOYieOigl3fgi7X3kKKh9h47P/B9/UZR5F2+aRTrDJwU9KIHtJDA9b7S1QM9tK7QfEW+2tXJpynDk4PelCwRdJECgG6wIAAd0A0qYDBlOB3QDSpgMGU4HdANKmAwZTgd0A0qYDBlOB3QDSpgMGU4HdANKmAwZTgd0A0qYDBlOB3QDSpgMGU4HdANKmAwZTjdIPQ/X7ua/WzRXdYAAAAASUVORK5CYII=)

   ```less
   multiply(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![662900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA2lJREFUeF7tmE9IVUEUxo9gJvSHoEWEm6CNFbQJ0mjVH8ggH0EQFGhUVosQapUEhmhQrVpEOytSqBCCeLUoqGhTqRCtCimEWlgkBEIEZUIxA+dy3nlzL8PzQQPneyvf9czcOd9vvnNmXsPJDfSX8DGlQAOgm+LtkwV0e8wB3SBzQAd0iwoYzBk9HdANKmAwZTgd0A0qYDBlOB3QDSpgMGU4HdANKmAwZTgd0A0qYDBlOB3QDSpgMOVknX725lNqbd+VIZn7NkO3+rppauJ59qx7aJi2Hzieff/18wfdHTpN4+VR/0zPsfBnnp4MX6HytQvZmPZSFx3qv07Ny1b4Z3oODtRzfZ1+TwOdmyq2jF5PaM0p7LHkoLe27aSjl0do1ZqWCn20gAMP39Ha9RsrYiQwDYADJfhS7yDt6TlHjUuaKubRm0MD52AJPu99KYJPDjoLHHIliy0Ffnn/Bo3091QZyMWsbllHV4/t9v+TgBkWbxwG4+J4w3GMHDc1/szPp9c4+/ljVi14XMwa/5frk4IuS20eTFkJGEKMeHJuB+bexd4MsJyHYfFG2LrvsG8hsorodc7NfvEVw324fdS6zphcFhuTFHR21cL8b3rzeIzaSl2+9ErXs+CNTUtpojxKWzoOZv04b6M4kbTzJh/dyaDLEszuZ8jb9h/xZwsZo4F+n/lUtTHcO3muUP9fLLjFjE8Kel5fdAkyePd3qA+zCCHwcl4Jr+h9DL3jRJ8/OxRBd+92G0MfAgE9YmuG+qAspa4MT799lUHnsiydp10lD3yhQ5U8pDloHyZf0OYdnRnkvafOw+kR7GoOkeWdr166lL5+cNsfmlx5l9cv7Sp9Cygq/XLBeh7eFEU93Y13fV+2IfT0yG2gXe1OyvL07MDJXsyuDo2LuQW4uZuXr6SxS2f8CkOVpuj0zhvBjeW7vj69F91CImWpe1hSPd1ll3cnjunFDMHFhu76+n6dd08vahGSgDz1x9zl606vxgmTg64d576HTr8aWN5BK6RL6A7OcXnXQP1jUCgu5le7GjnVdViS0OuaISarUgDQDW4KQAd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTjcI/R/AjtANApDjYgAAAABJRU5ErkJggg==)

   ```less
   multiply(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![993d00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA39JREFUeF7tmD1oVEEQxyfKRTBEsBAkKUxjEEUwflR+4BcqBO3SCBeLpLNJqqCihaKYKiBYCElhghaKlQQMqDGiNn7kGkUMooI5CwURMYUBlX0wy9zc7ssrzDi488p3+3Zm/7/52mu4shN+gz1JKdBg0JPinR3WoKfH3KAnyNygG/QUFUjwzNbTDXqCCiR4ZMt0g56gAgke2TLdoCeoQIJHtkw36AkqkOCRLdMNeoIKJHhky3SDnqAC7MhrD5ZhR/9lKC1vhtfjI/BwsPe/E0VdplPRndrzc9/h0dBxmJkYqxG/a/QlrGxb799xQHwft/Dr+1dws3tDLsQY9F0Dw7Cus8d/++PzLEye74bqi/v+Hbf5a/4nVK4PwrORM6oCRxX0rT1nYdPRAVhSaqwRiYrXsnkv7Dk1Ck2rWuuEnH1+D8b792fvOSRcvBD4EPTYXhR8KMicTY3gVUHH7EUxnWgIGGEhACpm59BdaN2yr6YquHXuwfIcWhNKPw79U2XKl3vug/seKwz6jpXJ/YZtYqFAky4DaqDTDA5lLAZCR/lkBjiUZUtLy6LlFKFwALy6fHh8G1o6dvuePvelmlUf92Cp5r5Oj13wwUl9x0ALtQJp0NSeSuhUJJ5B7YeOZdBpptPyS3s77/tc/FjZRoHcXu5xvZzPFjSIKtcuBoc/3D82l/wr8Gqg5/VhOtDRshkSLQ86Lcc0WykUzE5c27y6ra51uN8o9HdTt/wsQu0b9IJhTUV3MKrTD2DN9sM15TxWkvPKO+6LFeLbxxmfnbQk855umV4Q3N9cFuvF1EaRjMob0IpAj90g3Ldv7lwNBpD19AKR4DK4sWkFPLnUV3ftwrLpyvLGrj6YOHEkW0Nh4pDm1mzrPQdPh0/7ezTPdFdB8GZAez0v73nTOw2EvOmdBlUBGRZ9iaqeHrun04k7dk+nfTnvLk/3ooDz5oPYOrpXzHdtQ5w7p3roPEtCQENXohCoUMbxGeLt5A1oP1DO/iCiQxnfL3T35uA1AlcHfdHrmhnIFFCV6cZERgGDLqOzKisGXRUOGWcMuozOqqwYdFU4ZJwx6DI6q7Ji0FXhkHHGoMvorMqKQVeFQ8YZgy6jsyorBl0VDhlnDLqMzqqsGHRVOGScMegyOquyYtBV4ZBxxqDL6KzKikFXhUPGGYMuo7MqKwZdFQ4ZZwy6jM6qrPwBsWb163NkPWAAAAAASUVORK5CYII=)

   ```less
   multiply(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![cc5200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu9JREFUeF7tmD1oVEEUhe/iD1qsoBEMahQbQUMae1ESK8FCKxViobFRiFjZSbATi6ghaaKNQrCzELExoqQ1VVgLLYJo/EGzogv+RENkBu7jMjuzCb4IQ87Z8jEzb+757rn37qs8PyYLwh+UAhVCh+LtgyV0POaEDsic0AkdUQHAmNnTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wkdUAHAkOl0QgdUADDkFen0neduyeaeM004f759IbULnf55tatbdvXfkTWbthXrftdnZPrmKWlMPSmetR3olY6zw7JqfdU/W/gzJx/uX5V39y4Xa2JnfR6/La9H+rJMKVjonTdqsm773iYoFnwIXBdb8DHgui5X8CsS+u6Bx1Lt6pHG1Li8HDgUdduea5PydfJh4Vjd4xYrLE2M+R8NeTN63p+jrteqofs0ERq1p0UFiVWOHKyfLfSYgyzErcevSPvRS1JZvbap7C4Feii+bQkOen1irIBn36tnK9AdfUO+YtjWoWdpssw+u5sD6+IOWUIPgeptVXzryljJjZVuCyVGoJWrbZm2QD89Gpa2g71+LoglpHtP2P9zoJ8ddOtw6xTXXzfsOyy/3r8qHG5Bdpy+LvPfv/lynerXKfB2vcKziZeC/mViTDbuP+mHPEIvkc52eIoNQv9SOkMXa7kNBzULLnUPOr0E3NTW/wFdXWvLrXVyrPfae6R6+scHg7LlyEVf3tnTSyRD2fLuyr9rA9ODJ4pbhE6fq88sacJu1efD+SI2vS82R5SQqdTW7Hq6iyb1cUWFjvVsFd1Btx9TrDraLlKDYvj/OrUunDVi74t9xClFahk3ZwndxRcT3Pb4cIK3IFr9nUudHUuO2NrFWoHbkzNwd79soS9jYvOoQAFCB0wJQid0QAUAQ6bTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wkdUAHAkOl0QgdUADBkOp3QARUADJlOJ3RABQBDptMBof8FfgWO+7mu/xcAAAAASUVORK5CYII=)

   ```less
   multiply(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=)

   ```less
   multiply(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==)

   ```less
   multiply(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuFJREFUeF7tmL1LXEEUxa8QJI0kVWCxDIJoHxLTqZBUqRPBlaCkEYtUWonYWaUQOyWwFkmdLqB2gfwDimBjowsBQbEJEkiYB3e4e533UDKwA+e87s2+2bn3/O6ZrwH5IH+FD5QCA4QOxbtKltDxmBM6IHNCJ3REBQBz5ppO6IAKAKZMpxM6oAKAKdPphA6oAGDKdDqhAyoAmDKdTuiACgCmTKcTOqACgCkX6fS9j3syNToVcRx1j2R8bbwHz3Z7W+Zfzse2s8szaX9uy8HxQc93s89nZevdlgw9HIrtOz92ZKGzEN8nRyel874jw4+HY9v+8b5Mf5qO7zlj6nedFQfdi6sCWfAeuH7jwdd9Z6Gvv1mX5VfLMvhgsIeFhZ4zpn4DD+MXBd0CUNFV8Js/N7LxfUNOfp1E52ohWLgK1Do8NVOE5K3D62aKnDGVALw46Arv+ve1LH5ZlN2fu2LhBaDnl+eVM8MTimD122oPPF8sdTBD/9R4HkzOmAg9oYC62oKybgxATy9Oq7XcFkb4q8O1QxlrjYm6Wt9Dn9ajVvVbeKzrdbzQ1r3qxn2EHT9nTISeUEBBNUEP3cImrwn60telWxszO5wvjBQMjWHz7WZVMP8bk9+I9rMAilrTc7nKQk+5Vgtm5fVKBdQWkE7nuoeYeDpRFVmO2aefoO3YRUJvWtND8GF6Vyh1a7qd3vXo5dfnuRdzFVA75evGTfcMCj1HTISeUKBpp6yih2567va7d1sIOmtYWH75mHk2c6uAfL+RJyPxSOc3ifeNidBrFFAw/uf7nptTlzL6n3qsS13K6Dd2vFwxEXqDAl5kfzsWut7lhsxDtTOBHd6P52/s7OkgVRTadpeYSgBf1JpegiAIMRA6AmWXI6ETOqACgCnT6YQOqABgynQ6oQMqAJgynU7ogAoApkynEzqgAoAp0+mEDqgAYMp0OqEDKgCYMp1O6IAKAKZMpxM6oAKAKdPpgND/AZxnARqhLNd2AAAAAElFTkSuQmCC)

   ```less
   multiply(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC)

2. `screen()`

   与multiply函数效果相反，输出结果更亮的颜色。（对应ps中“变亮/滤色”）

   Example:

   ```less
   screen(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=)

   ```less
   screen(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![ff8533](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAArpJREFUeF7tmMGLT1EUx7+TWKjZYWuyJmWibGatkSixsLFQwl9gI0lkZYnsWFjMTqJZT0oNG8VG0mzJThTS6Hid3p0z573ea/zq9vt+3/LOmffu+X7u95xzfzPrt+bXoYdKgRlBp+L9L1lB52Mu6ITMBV3QGRUgzFk9XdAJFSBMWU4XdEIFCFOW0wWdUAHClOV0QSdUgDBlOV3QCRUgTFlOF/QKFdi/CBy7CuzY2Wzu7VPg+U2ga73CFGrbUt1OnzsMnLgBzO5pdTPo75fzdTsM5+4Bc0fa+G9fgGfXgbXXzVr2Tlv/8xt49QhYedDExffEv1vMwiXg6Hlg2/b2e2urwJMrtXHesJ+6oZeiusOj2OV6BOWpluCHQB8Skx0M/17l4OuGfvwacPDkZhdm6yUoFz2L87ZggJbvAO9ebHalvWvhMrByv6kQZSspgZ65C3z+0FaHi0vArn1ArC6V+b5e6C5gKdivH8DP78Ds7o0y2vqbJeDAYtMKYt+38uulewj0CMkrjq2XLaCMyw5dZbB9O9MD3Vy7d76pDPEp3Zn14aH9umwlXfPB10/Aw7OV4m62VS90292Y8u4yxwphVaAs4xn0bJDL4oYMhdkBquwITA/0rO92HZoSQgm3awAr393nZD9w8aAJ+ggFxjjdY6MbHUTfRO0xfUD9ZtA3pA3p/SPSn1To9Di9b1K3H3Yc+qnbwMeX7dSeOd3eZY/d++3JnG5rh04Djy+0bOT0/3BOxzi9624d+/WQu7x/N6ZQ9uv4i2AZq3v6FuCPgT50kLO4OOzFsp4Bzfp0dq2ME/4W0p/Uv9Zd3ieVNfl7BZ3wAAi6oBMqQJiynC7ohAoQpiynCzqhAoQpy+mCTqgAYcpyuqATKkCYspwu6IQKEKYspws6oQKEKcvpgk6oAGHKcrqgEypAmLKcTgj9L+/GB90Lo0VpAAAAAElFTkSuQmCC)

   ```less
   screen(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![ffa366](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAslJREFUeF7tmDFrFUEUhU80AbUwdtFOgoiQwioSY2ET0EZ7GyFNev9B/oE/QdDC3lQBWyUgFikCQcQijaZLLIxgTORmuex1diZvHyFk8s551WN3dnbu+e65c2fHDt8sHUI/KgXGBJ2K91Gwgs7HXNAJmQu6oDMqQBiz9nRBJ1SAMGQ5XdAJFSAMWU4XdEIFCEOW0wWdUAHCkOV0QSdUgDBkOV3QK1XgyTIweaNZ3O53YGW5+V+6XmkYtSyrfqcvvACu32n1cuil64OUnZ4DZp8BE5e6c6bP5sZ+/QCsvW5H2trmF4Er19prPzaB9y8HreTM7tcNPQoaHV663kfGuefArQfdkXF+u1saF6HffQrMPAIujP8/n6D3IVEYE50WhSxd7/Mqg2k/d6tXjD+/gU9vgW9rQJw/TQZ/R0y8XzvAx1eArfEc/Op1eslpP7eBq1Ndac2Beztd5w0C4n1BhOuJcNyzvr6YLOcAuC1xtKBbRH1Kd2wA7ZkUrt83516ezDeRnhiWLHu7bd8xKMkqSIx6oZs4Jy3vvuf+3W9Ld9r1OwTfq3ONWQTlFSFNnDimcvCjBT3XbRuM40qwO/ZgH9hYBba/tN14hJfu/TOPmwoQ5/aS73Otv6vA190ljA50B35xvIFngpecHnWIieJuj+Xdj17pHj59vynpsRfw99n8voYKsY8O9JzgqTvNuTbOEsI77dTpdi/X0XsiuPtv3mv6h+jq3HOCPqQCw+zpudJupdec73u6AUs/pPiSomNL24SN7bP365w+JOhS6e1zTo/HPAO89Rm4/bCFbmfw9EuevS8HKW3oSvt02tClX+xOEP5pPVp3eT+tqMnnFXTCBBB0QSdUgDBkOV3QCRUgDFlOF3RCBQhDltMFnVABwpDldEEnVIAwZDld0AkVIAxZThd0QgUIQ5bTBZ1QAcKQ5XRBJ1SAMGQ5nRD6P71t9aAIzvJMAAAAAElFTkSuQmCC)

   ```less
   screen(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![ffc299](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxxJREFUeF7tmE1IVFEUx88sIqKIoKBNuCkKihCCauHQqo+FoDtbKAQNFBQthGiniEKLCFpEyQQjCAnZokwqSDc1TNAXgkif1CJpoWCRYUS0MO6F8zhz595xZmTwMuf/Vnq57753/r/zP+e8SS3ns8uES5UCKUBXxdsGC+j6mAO6QuaADugaFVAYM3o6oCtUQGHIcDqgK1RAYchwOqArVEBhyHA6oCtUQGHIcDqgK1RAYchwOqDHqcDH2Xk62XeLpj9/o+ZdO2i07wztadpOofU4o4jnraJ3+vdfv6mzP0dPXr21qjH0bVs2eddNMpS7ns98ofT5K0VbCjcuUcv+nUVrA8OPqDc3nqz59oxMvqSugaFkz+2e09R57HA8dANvEj10hiQdbmIJrZdTXFYGdx9D/fP3H3Vfv0vZ8XzJURKqmxS8uT/TRj2nWqMGHz10dtOJQ/topDdDWzdvtIKG1leC/vjFDHV3HLXbZBU523aErl3ooKlPs7YSyCRzn7XwcylpN5wstSThWmVGtNBDjsu0polSRLmHhSLNGNqG9euSKsAb3IThdfkMvv9efsqWbHkeJ8fcj0U7TywsLtnEkOfKs3ytYK0A+57bcNCv3pko6sUm6BB0We65dLOrpdPlHGCAmovnAgbsOysm0PJdooXOL1lNeZdwZP+dfPOeDuxuSlqDOVtCkknhDo6+3m/OCvV9sz/2ga6hoHOCyNLsc5ucun1VwB34xi6fo8GxZ8Tl3XwhuMmRvdhl5wEzAKK8r7LGVeP0SqDLqbtSR3IFCbUJWTnM3/w7wipDr9vtDeX0lcr7h69zJb3YVdYMZBOv31F7urlkwpefYw8K03T84F4yg6NvIDTrsV4NBT008bNDb95/WjLkMRhuCeZ/X792Xe77Tnd/SwD0GhWoprzzI1wgDNQ32VcC3TcjuM8oV/prDL1ut0Xv9LpFrvhgQFcIH9ABXaECCkOG0wFdoQIKQ4bTAV2hAgpDhtMBXaECCkOG0wFdoQIKQ4bTAV2hAgpDhtMBXaECCkOG0wFdoQIKQ4bTAV2hAgpDhtMVQv8P5tOos5LuEocAAAAASUVORK5CYII=)

   ```less
   screen(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![ffe0cc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAs9JREFUeF7tmk1IVFEUx4+rbGVigbgoaJEGgRBtwigIolopEgjZzqAgykoIghxoDIIo+5AyJRcxGYgR7vpalR8VzEYXRi2i9pa2KlfKvXSG6+V98OBJ7/X/v9XMve+dmfP/nf+9Zy5TtfKtvCK8oBSoInQo3jZZQsdjTuiAzAmd0BEVAMyZezqhAyoAmDKdTuiACgCmTKcTOqACgCnT6YQOqABgynQ6oQMqAJgynU7o2VXg89fv0nHmssx++iLNO3fI2P3r0rh9m4SNZzeTf//NcuH0H4u/pLP7irx6994qptA3124KHDfFEHf9/rMsF4q3ZOjp88qtU89GpGVPc9yjuZ/PBfTp8qzsO9a1xuFG+bDxOCpBwPUZBPC5gD468UJOnO+Vw/v3yujda1JXW2MZhY3HQdfndMXY2lBfcf2p4+1yu9AjG6s3xIXJ7XymoYc5squj1fzTS0bGJtYI7wJTsHrDkzt90tl21L7tu/dICv0PpXjxtPSeO2nHdNXwC0vHNY4/r7F03o2Z1ar4L6HfHC5ZqP5lwLcfOVhxtVsI2hCaZ7RJ9IGaOYVuXrt9BqGnXOJJlvegfV6B1m+pk4HiJTlbuGGbwijoCz+XbB9hLve+N5MfZfeuJnlQGreF5f6SMCvT68kP0nroQMoKpBsu007XVJNA95d1Vy4D6HH/VRksjduuPQp6eW7e9hFBe7y77bgx0kWzftGgoUft6S/fzhD6+tVdfOQkTg9ryNxPieretRDcBo7Lezyj1O9IAj3qN7jC8w979Av7+7N/eOM2cguLS5UTQjdhdu8p4U8CXT8yqPN2D17CTvn80zw/jrvHBxVPHvb4XOzpKdUOw/xVgNABS4HQCR1QAcCU6XRCB1QAMGU6ndABFQBMmU4ndEAFAFOm0wkdUAHAlOl0QgdUADBlOp3QARUATJlOJ3RABQBTptMJHVABwJTpdEDoq9wpgoVPavzRAAAAAElFTkSuQmCC)

   ```less
   screen(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==)

   ```less
   screen(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=)

   ```less
   screen(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![ffff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAlRJREFUeF7tmD8yREEQh79NZE4gF1AlJ6FwAzECMgeQCWQOICPhEBKUE8gI5E4gk6waNaO6xu5Wedvvla75bbQ79ab//L7unnk7Go8Zo09TCowEvSne38kKenvMBb1B5oIu6C0q0GDOOtMFvUEFGkxZnS7oDSrQYMrqdEFvUIEGU1anC3qDCjSYsjpd0BtUoMGU1emC/t8V2AcugcUc6DVwnL+/ACv5+yuw2nH9v2swf3yBOn0buAGWTNYF+j2wY9YL9L+uz/JhxZ5md34gQ1gIBP0cOAUWANvhFpTt8HnWa+mtvxp4edb6HgJddx+BoF8BR8AncAGc5aztyH8AdjuuF5jF/pOZLO/AAbBlCq/4qveVuLpD6XtnEOj2vC6SfADPwOYEjd6A5T+sp05ez3cC27Gl0JKvk+wrFV75fQvYorMToW903e0L+rd2d8Bavi/YaVGOlPRMmi4b+e5QOv8RsMeI3dsdSt87g0BPMvQ53qcdETX0vTwNBL3vwsz2+4Q+rVvV6QPBneZmKOizzvTDPN51pg9UDH1CTynMur2XQrCvjfXt3RbCQJJ0dKMzfeIrnlWzfkWc9CaRno9xiUuRCvoP9CRH/TdvDbwUQw0+DvBg0DvOMm37pUCgThc9LwUE3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHYEPRAsr1AF3UvJQHa+AEM0Rqxp6fAiAAAAAElFTkSuQmCC)

   ```less
   screen(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAArdJREFUeF7tmzFLXEEUha+QQhsJrIWFlQgmEBDERrQyhYWQ1mLthNgJ/gAt9AfYCRZ2bmGbai2ylWIjCzZJhCAWIimyEELAFIGVGbjLdZz3YHQWfDnHan3Ou+M535z7Zh7sQPdjtyv8gXJggNCheHuxhI7HnNABmRM6oSM6AKiZz3RCB3QAUDKTTuiADgBKZtIJHdABQMlMOqEDOgAomUkndEAHACUz6YT+sh3o/O1IvVWX45tj/4+efDiRudE5/3mnvSNb51v+8/bMtmxOb2YVc/nrUpY/L8tF50KmalNy9P5IJl9PStH1rJNnLlaZpN/9u5ONsw3Z/7rfs0ChN743ZKW10rtuoYcLJVws7vdY7cOFQ6lP1H3NsIZCHxkcebAI7WLIzClrucpAt4myCbfAwoSf/jiV+U/zjwyz99u6dqCFrnVCqEXXsxLqQ7HKQC8y2KawKJ1F7d4umLW3a7I7uytDr4Ye2aydZHFsURoLDakN1vyYout94JS1ZCWg2+e1qncA1t+ty1JzKZrk6z/XvuWXwdSFFMLUgrG27/62+mbVDzn4dvBg7rK5slJ7ZrH/FnrrtuU3di79V7+vops8TarrBOPD4719gcJz3ob7CEJ/5opLuT2lvRclVOfTdh/rIDrGppbtPYVUxrFPhV4Gb+/Lnu8Atr3H5iH0jCBTSqVAd3U1xbFduEJu3jR9S7cbPd3Nuxp6Fif0FFIZx6ZCj4HShaDpb/9s+yOdPYrF7iP0jCBTSqVCj72U0fn0nF727LcdgtBTSGUcmwrdTR1CLXpjFm7o7MubsvM4z+kZAbNUfx2oxDm9vxbgVSd0POb8Lhsgc0IndEQHADXzmU7ogA4ASmbSCR3QAUDJTDqhAzoAKJlJJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEDo9xNuuafVlZOIAAAAAElFTkSuQmCC)

3. `overlay()`

   结合multiply与screen两个函数的效果，令浅的颜色更浅，深的颜色更深（对应ps中的叠加），输出结果由第一个颜色参数决定。

   Example:

   ```less
   overlay(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==)

   ```less
   overlay(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAwJJREFUeF7tmD9sjlEUxp8OuoiVoaMJkwGNhZTYiKSbhCZ0azqYtEMNKsJEIgZJSZCYWPzZEN2UGNkMhkq6li4Mba6b4x73e++X903eIyfu06m5ue+55z6/8+9+I5t7sAn+VaXACKFXxfv3ZQm9PuaEXiFzQif0GhWo8M7s6YReoQIVXpmZTugVKlDhlZnphF6hAhVemZlO6BUqUOGVmemEXqECFV6ZmU7oDhU4dRZYuANs3xGde3oPWJgGSusOr+DNJd+ZfmgCuP4Q2DWWdAvQXzxuv762CsydA1beJBuzV4DpS8C20bjWtKd0dgg4/Xf/FTB+LK18+Qyc3OeN81/++Iau4UiGB/dL688/Abv3DgquoS4uAZMXBvdsfAcWZ4Bnj4Am4PKF9iMHLnucg/cNXQD9+gks3QBuX46yltaffASWX6Z9GkpeIQSMbhPvXgPnjwPynZz7/m2qLBJAB4+malH6Tvx1lvd+oTdlbcjGjXVgpyr3QVCdpVpgndUB+oflNB80ZawEgpytM1ZsyVkHjsSKoc/WAaTtE3pLBfqALjYETDhahkIBqkt5yOIHN4Gpi3GOkAzWLSX8H6rO/sOxl+vWoW3pb1te+V9t85vpw8p4qbxr1XTQaAClPiwDXejpZ2bia2EY9BOTcX4g9J5jtWtPD8fnT7mmjNMBEaB9+xozN2T/1dnUv5npPQNtY64rdD3Vl/p8fm5eku9eS9CH9fTTU7G8s6e3IdlhTxfoeW/O3+Zy7Pwt4Mf64ISvXwjDpncJBB1g+fTeNuA6SNHn1v+np+c/uOQqyTRdeqfrUp63CLGVPx1Lvws4HuLCVQg9qND0vMrB58AlEHLwzoH7h95nTaOtPwr4znSCMlGA0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W10C4hyCe/9gaTvAAAAAElFTkSuQmCC)

   ```less
   overlay(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![ff5200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuZJREFUeF7tmL9qFVEQxr+ACBY2FjZapBQveQBtoialCGpjEy2MjYKxs5NgI5YK2qiNgtj5AiYoeYQQnyBJmUKLgAgJx2XY2ZO9m13uHfjwfLdKzj1ndub7nfmzd+bgFg6gT1EKzAh6Ubz/BSvo5TEX9AKZC7qgl6hAgTGrpwt6gQoUGLIyXdALVKDAkJXpgl6gAgWGrEwX9AIVKDBkZbqgF6hAgSEr0wWdUIH5JeDBG+DU6cq5tQ/A22Vg3DphCGwucWf63DXg8UfgzLlatwR943P7+t4ucPMpcOJkU+e9HeD1XWBzvVpf/QbMLdR7/v4Bvr4Evjyr18Y9O104/8ltbf8EVkZsnBv+cEO/87yGaBme3O+z7sP00B++BxbuH4XiwbcBtxPejxy47SEHzw3dAOWZeNx6l+jp7NlZYHWxQuQvkJ0zmPbcre91ZbELNLpSX8jNtcpefs5XDqLc54X+ags4f7Ep1f5vYP9Xs9ynHWn93SNgNF9l8ZBM87OBnbNnezt20fJn2f8/PjXnDF8RiIAnV/4v6FfvNXt1ijDv5zkAX+7zecEy2FeE9Hfq/xcuV8/y9n1b8GcFfYACx5XxvOyP67HjwHvgticNjfa20AX90u2qEgn6AKB9tg6Fntvs6rG+ffQBZ71fmd6H3AR7JoXu+7X12Hwyz3uv/76rp1srUU+fAHDb0SHQE6zrT4AXN2pLbZneZ8Lumt7tIvipP5/e/UWYsiTTMMc7yKXohkLPf8gxhQxK1/t32mtA81/7zE4+Q7S9YaS9xEMc9/Q+FHra3wbLl+++0Ntstf1ql/bl4MmB80OfRi2TjSMKcJd3AQtRQNBDZOU2KujcfEK8E/QQWbmNCjo3nxDvBD1EVm6jgs7NJ8Q7QQ+RlduooHPzCfFO0ENk5TYq6Nx8QrwT9BBZuY0KOjefEO8EPURWbqOCzs0nxDtBD5GV26igc/MJ8U7QQ2TlNiro3HxCvBP0EFm5jR4CRqv/4mo/+kwAAAAASUVORK5CYII=)

   ```less
   overlay(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![ff7a00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmD9IVlEYxh8ptKVcosWtoKEgCtqCBnV1aGgJbKg2B2kKHBranELEzRwSXBwcXC0iaBCCIKihyK3FbCkaEsK4Hl7uy/Wc+71XPj4P73m+Sc73es59nt95/9xv6GAOB+CnKAeGCL0o3odiCb085oReIHNCJ/QSHShQM3s6oRfoQIGSmemEXqADBUpmphN6gQ4UKJmZTugFOlCgZGY6oRfoQIGSmemEnqkDs5+AC1fCw+1+Bhauhr9T65nKyOWx8s/0B1vApYnaL4HeXP/zAzgzCpwajnv79zewOQN8WLV5f2MamFoCRs6G+H/7wNt5YOtp/f8Xx4G7L4FzY/Xa+xfAxiPbGScUlTd0barO8Nj65DPg9pP+QG8CFzgafAy4xGUOPm/o2vxvr4CVyWBrar2ZORpMFxDSNqQ6VPtK1jcrjVyEnTd11v/6DqzfB3Zen1Autx+bL/Q7y8DNh0effu8LcP7y0fUYVGkBukrEsliXfn1R9EWTvQTo1GKYM/Te8sxdW8mAr4Zf6FLuK0N1L061AYE5OlZntb5IGuj2EnB9OvRyfTFSZw4Yaq/j8oXeVsYt5V1KtM7EmBuy1+mRcDmqj8wGKegf14Br98KQR+i97ljH74/b09syLjWASW/++ZWZ3hFTf8OPC12yvDlQxQa7ZqZr6Kme/u45cOtxKO/s6f1lnpzS28q79Tsp3dKr9etY2/QuF0EGu9j03qul9Nmmrtv56+kxiOJKrLRXk3bVz/XAlxr29FRueZfvSmNA8f6gSwamXps00CpGSrUMcvKLWxN8bD/Lr3YDAtnlmLyhd1HCWLMDhG62yk8gofthaVZC6Gar/AQSuh+WZiWEbrbKTyCh+2FpVkLoZqv8BBK6H5ZmJYRutspPIKH7YWlWQuhmq/wEEroflmYlhG62yk8gofthaVZC6Gar/AQSuh+WZiWEbrbKTyCh+2FpVkLoZqv8BBK6H5ZmJf8Bi3HjshDH11AAAAAASUVORK5CYII=)

   ```less
   overlay(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![ffa300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtlJREFUeF7tmL1rFVEQxU8UFQRjG7BRkSCmSBUQS03rPyDERhuxsrLSQqtUFpJOGwNWdrZRtBHBKkVEoogISrTTgKKikXEZ7nDfbnYXwnp557zqvftmP+b87pmZ3YmtZWxBHyoFJgSdive/ZAWdj7mgEzIXdEFnVIAwZ/V0QSdUgDBlOV3QCRUgTFlOF3RCBQhTltMFnVABwpTldEEnVIAwZTld0AtV4OwacPBEdXNfXgIPZ6rvTeuFplHKbZXv9PkVYOpM0suhN623KXt0AZhbAvYcGD1nPDaP+/MTWFsEVq+nqKnTwKl7wP5Dae3NXeD5xba7+K//lw09ihod3rTeRcqTd4BjF0Yj4/nrNoYdEcHXAfezFg6+bOhR/I1HwMp8JWvTelfoFudu9IrxaxN4cRl4u5zahq9ZvFeHvNL4Rvj0JLn+2wfg2Xlg43GXOxo8plzoTY78ug5MTo8KZe76/hGYuQrs2pv+bwPgc4HDjA6OG803h59v7nY1Z8QK4fccN9DgSNsvOF7QLd+20p0PgPY7boxYRWKZjkDXl4AjC1Uvjxtj9ka16eyT9/92FoNFlAt9uzLetbw7hN8/Uumug25rDtiPsWrRBP3dfeDwuWoYFPQd3qx9e3rTALZdufWy7b1583Xq33L6DgPtcro+0D12975UWpucHq+dl/PPTxP0pp7+6hZw/EpV3tXTu5DsEdMHel0/zSdz692zN4HVa2myzp1uz+E+3NVN774R8uPi9B43Qo90hwodn55eV9oNmjnfe7pBz1+muNIRVOzrkURsE12e5Yei2PM64wPdEo+PeQb4/QNg+lKCbs/g+Zs8Oy6WcRcwB183F3R5a9cTyBDhZUMfQgHCawi6oBMqQJiynC7ohAoQpiynCzqhAoQpy+mCTqgAYcpyuqATKkCYspwu6IQKEKYspws6oQKEKcvpgk6oAGHKcrqgEypAmLKcLuiEChCm/BdKqvm0AAXVKQAAAABJRU5ErkJggg==)

   ```less
   overlay(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![ffcc00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAspJREFUeF7tmC9vFUEUxc8LKcE8BIWUQAjBIOi3ABQfoUkx4DA4HKKCFIfBgWirsShAorBFYAohQJvQkPAQtIS8Zpjc7H3TnSZl7rZ335yVszN37pzf/TO7g/E7jMGnKgUGhF4V73+HJfT6mBN6hcwJndBrVKDCM7OnE3qFClR4ZGY6oVeoQIVHZqYTeoUKVHhkZjqhV6hAhUdmphN6hQpUeGRmOqE7VuDqK2B4Izr45wuwcRsYvQFy446Pctyu9SPTLz8Dzt5ptBLoZxbax0MwlDy5/bTd2UXg0lPgxDDuNN4FNh8DXx+W7Hwka/sBfX4dOHVtMsODPLnxEulS4GJLV5cUuMzpCXj/0IfXgSurwMxF4Pd7YH0+SpwbLwGuYcpeOgi+Pwc+3W2C7e8I+Hwv7ihZr30s8aXDtb6hX1gCzj8ABicnJdjdiEGQjo9eAx9uxrlSBWRlLmDkfVj7623cLzxSqnVwhTnfHjVBqPeTu4WuCB2CKzE9fdC3VyZ7bQo9F0gB4M7HeEeQDN5emwygEDiby419yfwwSypCuraETkdrfUM/qIznyrtkuO6vYe7cfWDrSZOlGk4o66dvATPn4hfCQdB/vGiqD6F3FJaH6elpKZZSL67pnq2ByftctkogMdM7gpyaPQ7oaZWQi2RoAbp9sKd3FASHga4vcP9T3n++3H8Ll+zX9iTz227vOhA6kqTU7PT19NxFre0TTKsnsPQfPv1e3/5ze/TgEheONH3Qw6nafp7oDGyDpnt8Cr7t2zu10RPg/YBeWsu4fp8C/jOd0MwVIHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0b3APTcu+2MXGRSwAAAABJRU5ErkJggg==)

   ```less
   overlay(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==)

   ```less
   overlay(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![ffcc00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAspJREFUeF7tmC9vFUEUxc8LKcE8BIWUQAjBIOi3ABQfoUkx4DA4HKKCFIfBgWirsShAorBFYAohQJvQkPAQtIS8Zpjc7H3TnSZl7rZ335yVszN37pzf/TO7g/E7jMGnKgUGhF4V73+HJfT6mBN6hcwJndBrVKDCM7OnE3qFClR4ZGY6oVeoQIVHZqYTeoUKVHhkZjqhV6hAhUdmphN6hQpUeGRmOqE7VuDqK2B4Izr45wuwcRsYvQFy446Pctyu9SPTLz8Dzt5ptBLoZxbax0MwlDy5/bTd2UXg0lPgxDDuNN4FNh8DXx+W7Hwka/sBfX4dOHVtMsODPLnxEulS4GJLV5cUuMzpCXj/0IfXgSurwMxF4Pd7YH0+SpwbLwGuYcpeOgi+Pwc+3W2C7e8I+Hwv7ihZr30s8aXDtb6hX1gCzj8ABicnJdjdiEGQjo9eAx9uxrlSBWRlLmDkfVj7623cLzxSqnVwhTnfHjVBqPeTu4WuCB2CKzE9fdC3VyZ7bQo9F0gB4M7HeEeQDN5emwygEDiby419yfwwSypCuraETkdrfUM/qIznyrtkuO6vYe7cfWDrSZOlGk4o66dvATPn4hfCQdB/vGiqD6F3FJaH6elpKZZSL67pnq2ByftctkogMdM7gpyaPQ7oaZWQi2RoAbp9sKd3FASHga4vcP9T3n++3H8Ll+zX9iTz227vOhA6kqTU7PT19NxFre0TTKsnsPQfPv1e3/5ze/TgEheONH3Qw6nafp7oDGyDpnt8Cr7t2zu10RPg/YBeWsu4fp8C/jOd0MwVIHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0bJHT/jMw9JHRzSf0b3APTcu+2MXGRSwAAAABJRU5ErkJggg==)

   ```less
   overlay(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==)

4. `softlight()`

   与overlay函数效果相似，只是当纯黑色或纯白色作为参数时输出结果不会是纯黑色或纯白色（对应ps中的“柔光”）

   Example:

   ```less
   softlight(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAwJJREFUeF7tmD9sjlEUxp8OuoiVoaMJkwGNhZTYiKSbhCZ0azqYtEMNKsJEIgZJSZCYWPzZEN2UGNkMhkq6li4Mba6b4x73e++X903eIyfu06m5ue+55z6/8+9+I5t7sAn+VaXACKFXxfv3ZQm9PuaEXiFzQif0GhWo8M7s6YReoQIVXpmZTugVKlDhlZnphF6hAhVemZlO6BUqUOGVmemEXqECFV6ZmU7oDhU4dRZYuANs3xGde3oPWJgGSusOr+DNJd+ZfmgCuP4Q2DWWdAvQXzxuv762CsydA1beJBuzV4DpS8C20bjWtKd0dgg4/Xf/FTB+LK18+Qyc3OeN81/++Iau4UiGB/dL688/Abv3DgquoS4uAZMXBvdsfAcWZ4Bnj4Am4PKF9iMHLnucg/cNXQD9+gks3QBuX46yltaffASWX6Z9GkpeIQSMbhPvXgPnjwPynZz7/m2qLBJAB4+malH6Tvx1lvd+oTdlbcjGjXVgpyr3QVCdpVpgndUB+oflNB80ZawEgpytM1ZsyVkHjsSKoc/WAaTtE3pLBfqALjYETDhahkIBqkt5yOIHN4Gpi3GOkAzWLSX8H6rO/sOxl+vWoW3pb1te+V9t85vpw8p4qbxr1XTQaAClPiwDXejpZ2bia2EY9BOTcX4g9J5jtWtPD8fnT7mmjNMBEaB9+xozN2T/1dnUv5npPQNtY64rdD3Vl/p8fm5eku9eS9CH9fTTU7G8s6e3IdlhTxfoeW/O3+Zy7Pwt4Mf64ISvXwjDpncJBB1g+fTeNuA6SNHn1v+np+c/uOQqyTRdeqfrUp63CLGVPx1Lvws4HuLCVQg9qND0vMrB58AlEHLwzoH7h95nTaOtPwr4znSCMlGA0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W10C4hyCe/9gaTvAAAAAElFTkSuQmCC)

   ```less
   softlight(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![ff4100](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAs1JREFUeF7tmL1rVUEQxU9ALAJa2VnY+wo/Oi38FsHWykIlmM4iTUBsLGxESGNhpwQU9D+w0Yh/gdpob2FnpZAiCJHNZbjzNrsv7z3uxIN7XrnZnZk9v/nYm4Xti9iGfk0psCDoTfHeuaygt8dc0BtkLuiC3qICDd5ZM13QG1SgwSur0gW9QQUavLIqXdAbVKDBK6vSBb1BBRq8sipd0BtUoMErq9IFnVCBq7eAlWfA4qEuuLcvgLVloLZOeAW2kLgr/dQl4MFL4MjRXrcEfeN1eT0lg/18UnzaAFav7NZ+9Tlw/S7w/RuwNBr/e82395FOrL0HTl/uz5ZskVHnhr70CLh5HzhwsK/wJGBt3YvrYeTQ/fl0JgdVAm62rdOUgNsecvDc0K0S/2wBb54A6w87WWvrJnoO1UO3sz5BckiWMOb3y8e+s/z8ATy+DZy80Cek2c/PWbyq9CkVWP8KHDs+vnnzN7D5a7zdpx1p/ek94N0rwKp08XC/twQ9VezoTOcjh26+/boli/k6cb4bDd63Hym+I0x55f3axlvp80K3cx5qbaaX4PrW7s9Z90hkUtcZne1muVX+5w99wqU3SM3nfpGd4IcX+qQ2Xmvv1l5NcIM6C/TaAzCHfu5G1yUEfeA0nmWm214PYR7oqvSBIc5qblro/qFV85E/BtO+vdr7pJl+7U7X3jXTZ6W6x/5/Ad1/ipVe75YI/gshf737RBhYkiHM/V8zPVdknvaebOT/7TO7ebcoPTbTXuJHXApP0EufbCXwpfHgR4QlBjlwfuhD9DLZ2KUAd6ULWIgCgh4iK7dRQefmExKdoIfIym1U0Ln5hEQn6CGychsVdG4+IdEJeois3EYFnZtPSHSCHiIrt1FB5+YTEp2gh8jKbVTQufmERCfoIbJyGxV0bj4h0Ql6iKzcRgWdm09IdIIeIiu3UUHn5hMSnaCHyMpt9C+Hc/yQFeARLQAAAABJRU5ErkJggg==)

   ```less
   softlight(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![ff5a00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAthJREFUeF7tmDFrFUEUhU8gqI1FSklv4fsJGhC1UbCzCkQRtRC1SKNgYZFC0CaFBAsViYI2dkIEiZKI9mmeAa2jdilsJAiRcbjsdXY3b3c1z+Gd86pkmZ3Zc745c+/u2PY0tqEflQNjgk7F+7dYQedjLuiEzAVd0BkdINSsmi7ohA4QSlbSBZ3QAULJSrqgEzpAKFlJF3RCBwglK+mCTugAoWQlXdAzdeBuH5g8FB9u4yNwvRf/rrueqYxcHiv/pN9cBnrHC78MetX1xWvA5SfAxOSf/v7cAl7eAV7cau77kRng/AKwb3+8p2qO3rHyeiuPgAcXm6/zH0bmDd2b6hPe5Lo3sy30FLjN5eepAm7jMgefN3Rvfv8NcPtEtHXQ9TDm8RXg/dNuObKy8eN7nCf8LPXpSWMbYX2lSP3mBnD/LNB/2239Xb4rX+iXHgJHL5Tlf/0EHDhYvh7Stb4a4ewEvSrFBjdsEp9gv9GsnBjQc/din+FPIHtmP98uA+wy/WhB3/wCnL4BjO8pvEiP9jNz5TFhtMEM/YCl2h/THujrBWBqJvYOfmPY3GG+tj1EF3od78kXepNjPDRZVaZ76HVNmBlmyR/fG0GFn22cOugfngGHp2OTJ+gdt17dbYNqd2p6Oo9PtcGpa8DsRPj2WUn/xxjbTfe30MNq1pSF2utf6SzFadI99Lqa/moeODkbj3fV9HZMB45uC/3qc2Btqeja06S/Wyyn2Gq1r/07de+2Eayxq+re/UYYKHL4A0arpqcfbMxPa9LC/+nHm9Bph3rum6+6Zs935U3e5YfPs9GKowXdH+cmP02dBxogLs0Dp2YjeN9xp+CrXsOafLVrhGG4g/KGPlwvaFYTdBrUhVBBF3RCBwglK+mCTugAoWQlXdAJHSCUrKQLOqEDhJKVdEEndIBQspIu6IQOEEpW0gWd0AFCyUq6oBM6QChZSRd0QgcIJSvphNB/AXmR4XLrZAHTAAAAAElFTkSuQmCC)

   ```less
   softlight(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![ff7200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuNJREFUeF7tmDFoVEEQhv8gaBUFm4A2AZNGwSYEwUaJKewDFoJaaCHYWWhnYae9YKGNQgrBPkUMthbaxca0ESyNjRElsr4Mb7LZ9zwuN+Tn9r/q2Nudnf2/mdnZm9i5jx3oU5UCE4JeFe9/hxX0+pgLeoXMBV3Qa1SgwjPrThf0ChWo8MjKdEGvUIEKj6xMF/QKFajwyMp0Qa9QgQqPrEwX9AoVqPDIynRBJ1Rg7gaw9Aw4Ntk49+El8OYO0DVOeAQ2l7gzfWYBuP4KOHG61S1B/7S8f3zrK3D8VLe+3z4DT881v99dBWavtHP//ALWngArj9qxrr1TwPlPbsvvw0Z71x9u6FcfAwsPgSNH2wxPjpfGc/FzwQ3GtRfAhdv7cXjwJeC2wipNKXhsDjl4bugGKM/ErvEcpQXH723g7T3g42sgrT05DTxfbGb7ADJYFkC278b7trJ83wSWbwIzl9uA/PKusZev85WDKOt5oT9YB6bO7pVq+wfwc2tvuU8z0rhBtRU+W3125uL73sCg294+Yy3QbK8zl5qK4ff2tvr2POQAGF/olnWWmRtrZal9uc/7BctgXxHS93T/T19s+gJv3weaX3vIkPPteaEnT4ct74NmnAdu8FLTaK+FPujnl5pKJOgjDulhoQ+S5f76GASc3f3K9BFDzs0NA91neanE5p15fvf63/vu9PlbTXnXnT7iIBgG+v86+0E67L7u3QLBd/15915qLEcszUHMjd+dbmW71MD1vb+TigY0/7fPFM6fjqUXRppL3MQl98YLeldpLj3jSqniy3kOvvSvXbKRgycHzg/9IDVMazsV4M50gQtRQNBDZOU2KujcfEK8E/QQWbmNCjo3nxDvBD1EVm6jgs7NJ8Q7QQ+RlduooHPzCfFO0ENk5TYq6Nx8QrwT9BBZuY0KOjefEO8EPURWbqOCzs0nxDtBD5GV26igc/MJ8U7QQ2TlNiro3HxCvBP0EFm5jf4FxdMEMbDb7BcAAAAASUVORK5CYII=)

   ```less
   softlight(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![ff8a00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAsdJREFUeF7tmDFoFkEQhV8IxMYIYhlQKyEGC0GsrPQvBHsLQQu1S2VlZ2EhaGMh6dRCwcJesIiiYCESsJBE0MqIpaTQKiCRdRlu3P/2chvCZfnf+6tw2du9ed+8mbmb2rqHLehHpcCUoFPx/hesoPMxF3RC5oIu6IwKEMasni7ohAoQhiynCzqhAoQhy+mCTqgAYchyuqATKkAYspwu6IQKEIYspwt6pQpcXQUOHY8P93MNeLwQ/85drzSMWh6rfqdfXAaOnGv0Mui562duA6dvAtMz8Z4/m8CHu8C7W2WaL1wGRkvAzGx+n8NngQtPgP1zzd6fHgEvr5edNfDquqF7Ub3Dc9dT4CZmKfgUeNs+bcBtXeXg64buxf/2Cng+irLmrpv7f/8AXlwBZucat/r7t3OWtY3NX8DyYlxtrk8rjSXU+pvG9Xb++uvtTtqT/9cL/fxD4MS1cVE2vgAHj41fD+46cDS2gra+b9DbXGxwV58C3sE+UdKEGj2Ic4Y/y57Z77cnWLsPnSzo39/+34ctdu+8XAtoqw6+THugH5ditQm93CeG7R3O3ckcMVCC1Au9q4znynu4p61CdPVY22t6XwQVfjYI5qB/fgbMX4pDnqDvcqqW9nTrxV09PTeAWW/e+NpUCzl9l4H22a4EeupYe0Uz54dEWLkPnLoRy7IBTe/z0HM93e+jnt6HZMGanUBPS653fxjUTi7GsmzQLSn8a13X9G6JYINd2/TuE6Eg3KGWTlZPTz/YeBUDrPd3xj+mhEk79HM/fOWGPT+V93mXH4pi4TmTBb3PIOeBBohWqm2Qs7aQgm97Devz1a4QyBDL64Y+hAKEZwi6oBMqQBiynC7ohAoQhiynCzqhAoQhy+mCTqgAYchyuqATKkAYspwu6IQKEIYspws6oQKEIcvpgk6oAGHIcrqgEypAGLKcLuiEChCG/Bf/z/bSEUqH5AAAAABJRU5ErkJggg==)

   ```less
   softlight(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![ffa100](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAArlJREFUeF7tmD1oVEEUhU+UKATUVhELRSJGC7uAWKmp7QVttAlWqews7KxShHRaqGBlY+0PFiLYWcSIIoYUaloVAioaGSeXd/Mys763eVmGPWerZXZm3tzz3XPvvB1Zu4s16EOlwIigU/H+F6yg8zEXdELmgi7ojAoQxqyeLuiEChCGLKcLOqEChCHL6YJOqABhyHK6oBMqQBiynC7ohAoQhiynC3qhClx4A+ybiIf7ugg8OhG/58YLDaOUY5Xv9KknwIFzlV4GPTfeRllLmg93gJdXN648cgmYnAdG98TxPz+BhVvA6xvVvP1ngTP3gLGD1VhqrzZnGsDcsqF7Ub3Dc+NNBasnTB1UHbjt68GngNu8wsGXDd2L/+Up8Ph8lDU33gS6bwk5SDbn13fg1bU4y1xfrzSWCCvPK9evfgJeXAZWnjU50cDnlAv99G3g6JXNgnx7D+wd3zwe3LX6GTh5Hdixq/q9DiAAHTsEvJ0Fjs/E8u2d6R3sE82qg+03ORfvGb4C2ZktWT7eHzjQJg8cLugh4lSieDCmiq8WHnpu3AN9Nx+rTejlPjFO3YxJFz71/t+ExoDmlAu9VxlvWt4Nwu8fsUx75+Xg2ppQLXwyeOhLD4DDF2OVEPSOU7VtT89dwFLlVk7vGFZX27WBbnN37q5Kaz9Ozz3T9/TFWWBiJpZ39fSuaK/v0wZ6qp8aqDZOD4/udXu3cm57p27vqTtEx9JsZbvh6emp0h5gB+e36elBTd/Xvbo+eZq8y2+FzDauHR7oQST/mhderZYfAsem20NPgf9ftQhrUv/abSO8frcuG3q/UWldTwUEnTBBBF3QCRUgDFlOF3RCBQhDltMFnVABwpDldEEnVIAwZDld0AkVIAxZThd0QgUIQ5bTBZ1QAcKQ5XRBJ1SAMGQ5XdAJFSAMWU4nhP4X3v7tUN9I/0kAAAAASUVORK5CYII=)

   ```less
   softlight(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAwJJREFUeF7tmD9sjlEUxp8OuoiVoaMJkwGNhZTYiKSbhCZ0azqYtEMNKsJEIgZJSZCYWPzZEN2UGNkMhkq6li4Mba6b4x73e++X903eIyfu06m5ue+55z6/8+9+I5t7sAn+VaXACKFXxfv3ZQm9PuaEXiFzQif0GhWo8M7s6YReoQIVXpmZTugVKlDhlZnphF6hAhVemZlO6BUqUOGVmemEXqECFV6ZmU7oDhU4dRZYuANs3xGde3oPWJgGSusOr+DNJd+ZfmgCuP4Q2DWWdAvQXzxuv762CsydA1beJBuzV4DpS8C20bjWtKd0dgg4/Xf/FTB+LK18+Qyc3OeN81/++Iau4UiGB/dL688/Abv3DgquoS4uAZMXBvdsfAcWZ4Bnj4Am4PKF9iMHLnucg/cNXQD9+gks3QBuX46yltaffASWX6Z9GkpeIQSMbhPvXgPnjwPynZz7/m2qLBJAB4+malH6Tvx1lvd+oTdlbcjGjXVgpyr3QVCdpVpgndUB+oflNB80ZawEgpytM1ZsyVkHjsSKoc/WAaTtE3pLBfqALjYETDhahkIBqkt5yOIHN4Gpi3GOkAzWLSX8H6rO/sOxl+vWoW3pb1te+V9t85vpw8p4qbxr1XTQaAClPiwDXejpZ2bia2EY9BOTcX4g9J5jtWtPD8fnT7mmjNMBEaB9+xozN2T/1dnUv5npPQNtY64rdD3Vl/p8fm5eku9eS9CH9fTTU7G8s6e3IdlhTxfoeW/O3+Zy7Pwt4Mf64ISvXwjDpncJBB1g+fTeNuA6SNHn1v+np+c/uOQqyTRdeqfrUp63CLGVPx1Lvws4HuLCVQg9qND0vMrB58AlEHLwzoH7h95nTaOtPwr4znSCMlGA0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W10C4hyCe/9gaTvAAAAAElFTkSuQmCC)

   ```less
   softlight(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![ffa100](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAArlJREFUeF7tmD1oVEEUhU+UKATUVhELRSJGC7uAWKmp7QVttAlWqews7KxShHRaqGBlY+0PFiLYWcSIIoYUaloVAioaGSeXd/Mys763eVmGPWerZXZm3tzz3XPvvB1Zu4s16EOlwIigU/H+F6yg8zEXdELmgi7ojAoQxqyeLuiEChCGLKcLOqEChCHL6YJOqABhyHK6oBMqQBiynC7ohAoQhiynC3qhClx4A+ybiIf7ugg8OhG/58YLDaOUY5Xv9KknwIFzlV4GPTfeRllLmg93gJdXN648cgmYnAdG98TxPz+BhVvA6xvVvP1ngTP3gLGD1VhqrzZnGsDcsqF7Ub3Dc+NNBasnTB1UHbjt68GngNu8wsGXDd2L/+Up8Ph8lDU33gS6bwk5SDbn13fg1bU4y1xfrzSWCCvPK9evfgJeXAZWnjU50cDnlAv99G3g6JXNgnx7D+wd3zwe3LX6GTh5Hdixq/q9DiAAHTsEvJ0Fjs/E8u2d6R3sE82qg+03ORfvGb4C2ZktWT7eHzjQJg8cLugh4lSieDCmiq8WHnpu3AN9Nx+rTejlPjFO3YxJFz71/t+ExoDmlAu9VxlvWt4Nwu8fsUx75+Xg2ppQLXwyeOhLD4DDF2OVEPSOU7VtT89dwFLlVk7vGFZX27WBbnN37q5Kaz9Ozz3T9/TFWWBiJpZ39fSuaK/v0wZ6qp8aqDZOD4/udXu3cm57p27vqTtEx9JsZbvh6emp0h5gB+e36elBTd/Xvbo+eZq8y2+FzDauHR7oQST/mhderZYfAsem20NPgf9ftQhrUv/abSO8frcuG3q/UWldTwUEnTBBBF3QCRUgDFlOF3RCBQhDltMFnVABwpDldEEnVIAwZDld0AkVIAxZThd0QgUIQ5bTBZ1QAcKQ5XRBJ1SAMGQ5XdAJFSAMWU4nhP4X3v7tUN9I/0kAAAAASUVORK5CYII=)

   ```less
   softlight(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAwJJREFUeF7tmD9sjlEUxp8OuoiVoaMJkwGNhZTYiKSbhCZ0azqYtEMNKsJEIgZJSZCYWPzZEN2UGNkMhkq6li4Mba6b4x73e++X903eIyfu06m5ue+55z6/8+9+I5t7sAn+VaXACKFXxfv3ZQm9PuaEXiFzQif0GhWo8M7s6YReoQIVXpmZTugVKlDhlZnphF6hAhVemZlO6BUqUOGVmemEXqECFV6ZmU7oDhU4dRZYuANs3xGde3oPWJgGSusOr+DNJd+ZfmgCuP4Q2DWWdAvQXzxuv762CsydA1beJBuzV4DpS8C20bjWtKd0dgg4/Xf/FTB+LK18+Qyc3OeN81/++Iau4UiGB/dL688/Abv3DgquoS4uAZMXBvdsfAcWZ4Bnj4Am4PKF9iMHLnucg/cNXQD9+gks3QBuX46yltaffASWX6Z9GkpeIQSMbhPvXgPnjwPynZz7/m2qLBJAB4+malH6Tvx1lvd+oTdlbcjGjXVgpyr3QVCdpVpgndUB+oflNB80ZawEgpytM1ZsyVkHjsSKoc/WAaTtE3pLBfqALjYETDhahkIBqkt5yOIHN4Gpi3GOkAzWLSX8H6rO/sOxl+vWoW3pb1te+V9t85vpw8p4qbxr1XTQaAClPiwDXejpZ2bia2EY9BOTcX4g9J5jtWtPD8fnT7mmjNMBEaB9+xozN2T/1dnUv5npPQNtY64rdD3Vl/p8fm5eku9eS9CH9fTTU7G8s6e3IdlhTxfoeW/O3+Zy7Pwt4Mf64ISvXwjDpncJBB1g+fTeNuA6SNHn1v+np+c/uOQqyTRdeqfrUp63CLGVPx1Lvws4HuLCVQg9qND0vMrB58AlEHLwzoH7h95nTaOtPwr4znSCMlGA0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W2U0H3zMfGO0E1k9W10C4hyCe/9gaTvAAAAAElFTkSuQmCC)

5. `hardlight()`

   与overlay函数效果相似，不过由第二个颜色参数决定输出颜色的亮度或黑度，而不是第一个颜色参数决定（对应ps中“强光/亮光/线性光/点光”）

   Example:

   ```less
   hardlight(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC)

   ```less
   hardlight(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![662900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA2lJREFUeF7tmE9IVUEUxo9gJvSHoEWEm6CNFbQJ0mjVH8ggH0EQFGhUVosQapUEhmhQrVpEOytSqBCCeLUoqGhTqRCtCimEWlgkBEIEZUIxA+dy3nlzL8PzQQPneyvf9czcOd9vvnNmXsPJDfSX8DGlQAOgm+LtkwV0e8wB3SBzQAd0iwoYzBk9HdANKmAwZTgd0A0qYDBlOB3QDSpgMGU4HdANKmAwZTgd0A0qYDBlOB3QDSpgMOVknX725lNqbd+VIZn7NkO3+rppauJ59qx7aJi2Hzieff/18wfdHTpN4+VR/0zPsfBnnp4MX6HytQvZmPZSFx3qv07Ny1b4Z3oODtRzfZ1+TwOdmyq2jF5PaM0p7LHkoLe27aSjl0do1ZqWCn20gAMP39Ha9RsrYiQwDYADJfhS7yDt6TlHjUuaKubRm0MD52AJPu99KYJPDjoLHHIliy0Ffnn/Bo3091QZyMWsbllHV4/t9v+TgBkWbxwG4+J4w3GMHDc1/szPp9c4+/ljVi14XMwa/5frk4IuS20eTFkJGEKMeHJuB+bexd4MsJyHYfFG2LrvsG8hsorodc7NfvEVw324fdS6zphcFhuTFHR21cL8b3rzeIzaSl2+9ErXs+CNTUtpojxKWzoOZv04b6M4kbTzJh/dyaDLEszuZ8jb9h/xZwsZo4F+n/lUtTHcO3muUP9fLLjFjE8Kel5fdAkyePd3qA+zCCHwcl4Jr+h9DL3jRJ8/OxRBd+92G0MfAgE9YmuG+qAspa4MT799lUHnsiydp10lD3yhQ5U8pDloHyZf0OYdnRnkvafOw+kR7GoOkeWdr166lL5+cNsfmlx5l9cv7Sp9Cygq/XLBeh7eFEU93Y13fV+2IfT0yG2gXe1OyvL07MDJXsyuDo2LuQW4uZuXr6SxS2f8CkOVpuj0zhvBjeW7vj69F91CImWpe1hSPd1ll3cnjunFDMHFhu76+n6dd08vahGSgDz1x9zl606vxgmTg64d576HTr8aWN5BK6RL6A7OcXnXQP1jUCgu5le7GjnVdViS0OuaISarUgDQDW4KQAd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTgd0gwoYTBlOB3SDChhMGU4HdIMKGEwZTjcI/R/AjtANApDjYgAAAABJRU5ErkJggg==)

   ```less
   hardlight(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![cc5200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu9JREFUeF7tmD1oVEEUhe/iD1qsoBEMahQbQUMae1ESK8FCKxViobFRiFjZSbATi6ghaaKNQrCzELExoqQ1VVgLLYJo/EGzogv+RENkBu7jMjuzCb4IQ87Z8jEzb+757rn37qs8PyYLwh+UAhVCh+LtgyV0POaEDsic0AkdUQHAmNnTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wkdUAHAkOl0QgdUADDkFen0neduyeaeM004f759IbULnf55tatbdvXfkTWbthXrftdnZPrmKWlMPSmetR3olY6zw7JqfdU/W/gzJx/uX5V39y4Xa2JnfR6/La9H+rJMKVjonTdqsm773iYoFnwIXBdb8DHgui5X8CsS+u6Bx1Lt6pHG1Li8HDgUdduea5PydfJh4Vjd4xYrLE2M+R8NeTN63p+jrteqofs0ERq1p0UFiVWOHKyfLfSYgyzErcevSPvRS1JZvbap7C4Feii+bQkOen1irIBn36tnK9AdfUO+YtjWoWdpssw+u5sD6+IOWUIPgeptVXzryljJjZVuCyVGoJWrbZm2QD89Gpa2g71+LoglpHtP2P9zoJ8ddOtw6xTXXzfsOyy/3r8qHG5Bdpy+LvPfv/lynerXKfB2vcKziZeC/mViTDbuP+mHPEIvkc52eIoNQv9SOkMXa7kNBzULLnUPOr0E3NTW/wFdXWvLrXVyrPfae6R6+scHg7LlyEVf3tnTSyRD2fLuyr9rA9ODJ4pbhE6fq88sacJu1efD+SI2vS82R5SQqdTW7Hq6iyb1cUWFjvVsFd1Btx9TrDraLlKDYvj/OrUunDVi74t9xClFahk3ZwndxRcT3Pb4cIK3IFr9nUudHUuO2NrFWoHbkzNwd79soS9jYvOoQAFCB0wJQid0QAUAQ6bTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wkdUAHAkOl0QgdUADBkOp3QARUADJlOJ3RABQBDptMBof8FfgWO+7mu/xcAAAAASUVORK5CYII=)

   ```less
   hardlight(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![ff8533](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAArpJREFUeF7tmMGLT1EUx7+TWKjZYWuyJmWibGatkSixsLFQwl9gI0lkZYnsWFjMTqJZT0oNG8VG0mzJThTS6Hid3p0z573ea/zq9vt+3/LOmffu+X7u95xzfzPrt+bXoYdKgRlBp+L9L1lB52Mu6ITMBV3QGRUgzFk9XdAJFSBMWU4XdEIFCFOW0wWdUAHClOV0QSdUgDBlOV3QCRUgTFlOF/QKFdi/CBy7CuzY2Wzu7VPg+U2ga73CFGrbUt1OnzsMnLgBzO5pdTPo75fzdTsM5+4Bc0fa+G9fgGfXgbXXzVr2Tlv/8xt49QhYedDExffEv1vMwiXg6Hlg2/b2e2urwJMrtXHesJ+6oZeiusOj2OV6BOWpluCHQB8Skx0M/17l4OuGfvwacPDkZhdm6yUoFz2L87ZggJbvAO9ebHalvWvhMrByv6kQZSspgZ65C3z+0FaHi0vArn1ArC6V+b5e6C5gKdivH8DP78Ds7o0y2vqbJeDAYtMKYt+38uulewj0CMkrjq2XLaCMyw5dZbB9O9MD3Vy7d76pDPEp3Zn14aH9umwlXfPB10/Aw7OV4m62VS90292Y8u4yxwphVaAs4xn0bJDL4oYMhdkBquwITA/0rO92HZoSQgm3awAr393nZD9w8aAJ+ggFxjjdY6MbHUTfRO0xfUD9ZtA3pA3p/SPSn1To9Di9b1K3H3Yc+qnbwMeX7dSeOd3eZY/d++3JnG5rh04Djy+0bOT0/3BOxzi9624d+/WQu7x/N6ZQ9uv4i2AZq3v6FuCPgT50kLO4OOzFsp4Bzfp0dq2ME/4W0p/Uv9Zd3ieVNfl7BZ3wAAi6oBMqQJiynC7ohAoQpiynCzqhAoQpy+mCTqgAYcpyuqATKkCYspwu6IQKEKYspws6oQKEKcvpgk6oAGHKcrqgEypAmLKcTgj9L+/GB90Lo0VpAAAAAElFTkSuQmCC)

   ```less
   hardlight(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![ffc299](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtpJREFUeF7tms1rFEEQxd8mJMQobBSUmBA9mGBAAga9GQTx4CFn/zj/BsGbB09C8KgoBEFJLjFEokKMgh/4taEdiqnt7d5kNyxp9r05LU1P99T71auuGbbRWnvQgi4qBRqCTsX7f7CCzsdc0AmZC7qgMypAGLPOdEEnVIAwZDld0AkVIAxZThd0QgUIQ5bTBZ1QAcKQ5XRBJ1SAMGQ5XdALVeDyTWDuOtAYAVr/gO1XwNZzIDdeaBilPFb5Tr+wAMyvAKNjlWYG/ceX9HhIhm7Xwm1gerF9xu4bYGOtfezGfWDybD2WmrO0CkzN1nP2d4D1x6WwzT5H+dANknd4CCc33k1yXxnieQZ1aga4egcYP925kocaJ4XN/v4ZePGwaPDlQzc3/f0NbD4DPm5UgubGD4N+7hLw8lE1y1eRX9+At0+B8/NVJfBJFu91qlkfN5Ys/SThCaVGudBzjvvzEwh/5RybaJfMoO2/r6uAzYgTxsb9Hnb/3HJVsv16lhwjo1U/MT5ZJYZf16+VOgpOCHBq2+GDfuVW+1kcos5B9+XeSre52jvd9wEBaLisLzDAqbUKAu0fpVzo9pS9lHcPx5+/i3eBvXf10RDW9pB8UsSNY+rs/7SZP/fD/MIbuuGCbgniS3PKbb7rTlWBuOHb2wKaM4CV9/CGECfH1w/AxJmqAVR5P2aN68XpR4Huu+6jOtIqSO6Y8JUj/LbvCMcMfVC3D5fTDyvvzYudZ3GsbGjIZpeA1086O3z/OnbtHrCzDoTGMdUQhvFCr+GCnuv4zaHhq57/4OKh2JEQxlLv6bHLU+/p8bcEQe9TgV7Ku20RAzGgqc7e7ukGPdUjxHt0K/19hj6o28p3+qAiJ15X0AnhC7qgEypAGLKcLuiEChCGLKcLOqEChCHL6YJOqABhyHK6oBMqQBiynC7ohAoQhiynCzqhAoQhy+mCTqgAYchyuqATKkAYspxOCP0ATMobsxGVGtIAAAAASUVORK5CYII=)

   ```less
   hardlight(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==)

   ```less
   hardlight(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==)

   ```less
   hardlight(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC)

   ```less
   hardlight(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==)

6. `exclusion()`

   效果与difference函数效果相似，只是输出结果差别更小（对应ps中“差值/排除”）

   Example:

   ```less
   exclusion(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=)

   ```less
   exclusion(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![cc7033](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAthJREFUeF7tmD9oVEEQxueMpyIkKUwkBIIomkJL/yBiZ5U0EhRTpLDQwlgJFtqIiI02FhaJrUUKxcbGVOlDTGuRExQVQ9CkkGBICEfCHsxj3jJ7xuOK5b7vurvb3bfz/eab2X2VxXtnd4QfKAUqhA7FuxEsoeMxJ3RA5oRO6IgKAMbMnk7ogAoAhkynEzqgAoAh0+mEDqgAYMh0OqEDKgAYMp1O6IAKAIZMpxM6oAKAIXec0888eCuHBk4kUa7XFqQ2fbfx/7HxR9J38WoxdvvPL/k681jWP38sfovXq29tyI93z2Rt8UMxZnhySrqHLxTfd+rbsjL3WpZnX2WZUrDQY+BKx4I/cm5Uhq4/lK6Dh0vwLPjuU+fl+MQTqfYeLY3JGXzHQfespYAV6IHe/gLm5soX+fT8Rsn1q/Pv5dubpxKg9126JksvbzWWHRy5IwNXbkqlqyo6JkAfHJmU5dnpRoWwiWKrSk6Wzxa65yArogUQBE05SyHs218tSq7ODfO0DNvnpWBp8nglXqF6a+cEPOwlS+gxUBVNYcQ9tBl07cnqaNvLY3jNxuoevOTy9quVIDfgWUK3jrNQgmN7Tl+Wrd/fixJrQQ6N3Zf65t/S4SnlOk2aVqAH0exz47LvnQ1yA5+d021P9NyylxKrInvO/V+nW2DW0akWYPcfJ0cu8DsWunfoUtE1cWyp3ktPD/NTiWSBaiXxroA5gM8OervKe6qEB9E9N3qJcPL2C9n4WStahuf0MC98wmk/tXYOoO0esoNuy28slpZU7wVMyrWpEusdBuN+nRpjzwKp+z7v6S2k+r9OxDGQ+NCnL1Wa3ZXjNVo5oHkvcJpd6VqQou1TsnR626PkgiUFCB0wIQid0AEVAAyZTid0QAUAQ6bTCR1QAcCQ6XRCB1QAMGQ6ndABFQAMmU4ndEAFAEOm0wkdUAHAkOl0QgdUADBkOp3QARUADJlOB4S+C3o2g80fYvP5AAAAAElFTkSuQmCC)

   ```less
   exclusion(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![997a66](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA01JREFUeF7tmL1rlEEQxid+oPjFiWBQIUWicp0GggaVgCCkCAoWESwUYmFjIbZW+Q/E0kbBxiKFoKQQFCEYtIhESHOcXBDBBAUxfoVELyr7wryMk933VvMeLMyT8mWzu/P85pmduY5b1y//JvyZUqAD0E3xzoIFdHvMAd0gc0AHdIsKGIwZbzqgG1TAYMhwOqAbVMBgyHA6oBtUwGDIcDqgG1TAYMhwOqAbVMBgyHA6oBtUwGDISTp96NI12tdTzXG8a9Ro/PaNv/D0nTpDhwcGad36Ddn3718W6OnYHZqbrdGB3n46cfo8bdy02Yv010qTXk08oqnHD6KR7+2u0snhEdq6o1J4L313ea/ow9q8MDnow1dHaefuPavC/vRhnsZujmbfB85epGrf8VVrfi4v0bOH97LvZULXCcYHy2T0JYVOxjazjN4+KehS3NrUJE3cv5sDZnfOzdZzx3EiSGf7qgKrwQlVtEYrJ2EWuZYd/j9VJJpWSQuTgs4OZse+nn5BUnSXCPNv6rmLOTGcFiy6rAhSI95bg/NVFrmv705ae5l08n9LYlT6NklCd1GyeNL9zqH16ec5dAbcyo0hKKGSLN0qk2nx6+e815DJw3dcaTapMfOSDvYezXqNVF2fFPQQBP2G6mZJWsFXgnl9TFOln4BQjyHf6/2Hjnh7DLcmRfBJQXci6c77/dsGbavsyrpmWTolDAfz28JH6uzqIV3eW5XeUALxu8/nyCeHSz4D3bK9kkPnO8b2GaXX7ogNk4Ou78yl030PjVmyQugmjaFKaHyGrw/QTvet0Xdy+7nx0ZV3Nz3oXuRfGscIZmtekhz0wQtXaGbySTZvh97qY0Pn6MfyUj5nhzpn+f++Bo8BF00B2tVutteJJEdEBuybRNZMq6QNkoPue0P1uxia07WjWgmvS7s7x7nV/ajDexX1GfK80DMR00eUxDJ6m+Sh+8qyD7pvVPK5VCqjgbo9Oru6sx+HdALpZCw6j88IjY/RdNq0MDnobYoT2woFAN1gOgA6oBtUwGDIcDqgG1TAYMhwOqAbVMBgyHA6oBtUwGDIcDqgG1TAYMhwOqAbVMBgyHA6oBtUwGDIcDqgG1TAYMhwOqAbVMBgyHC6Qeh/APNhxMFyPLNHAAAAAElFTkSuQmCC)

   ```less
   exclusion(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![668599](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA4xJREFUeF7tmM9LVUEUx489i9InPiuCbNGDUF5gFlRUFEK0MJDauahF0MZNq/4DN61btbBN0KIW7gpBFxFIZT/BzOihCQb9IAgKIjDzVczAuZyOc29v9x2Y4+5d586Z+X7O95yZ2zJyZewP2V9SCrQY9KR4+80a9PSYG/QEmRt0g56iAgnu2Xq6QU9QgQS3bE436AkqkOCWzekGPUEFEtyyOd2gJ6hAgls2pxv0BBVIcMvmdIOeoAIJbjlap18+P0S16q4MybfvP+jG3ftUX/6YPbswNEDH99ey3yurv+j25AN6PL/on50dOESDxw5Qa2mD/73W+E1TM7N0Z/p59s7oyDDt3N61Dv3Dl3W6OTGdO07/3w3Ua64vf6CrtyaiS6vooNeq3XTxzEmqdLT/I5aGHoIloWvgPJkG/z/oeetx80moefN8+vKVRq+PRwU+OujslpArWTnp8JDjpOs4WSrldjp3+gRt3rQxg8VAy21b1lUAHUuuh9fISbZja2dWUXg9vMaifaAyISroR/t6MjB5MKXzisong5FOYzfye81A53lkpeF1tpZKPlkqHW2+zchKI9eZtxeDLnrwWqNBL94s0ZG+Xt+PpVuk4E/mF+jg3j3eve5PiisTSIobgsfv8zg5T6jy6Erj3uOzBb8r20tsvT0qp+uDmYTF4N0zeTjTbpHAQvM1kxgygfKSRybI09dvg+cQHmPQC2paqFdL0Z14S+8/Z9B1mXaHPy7nXMqLerpeiowl24I+FM4tvqPe3d3E5d3dBnRyuHVu6yz7A6mV9wLoLK4r73z10j18Zm7B930puJuSITtYk49mg2M4qULXP15WqIfrJfM8+ooox/Fe3DN9TUT1co4bVXnXrnZ3XOky5xhZStmN+j1ODHlSl4nB0Pt7qrTyczW7t4ec7pLu1OF9dG18ymuWVw0uDQ/SvWev/HcEmahFCYaCHxV0edXSgkjx8nq/dJ7+UCLn47bQzDx593Tt8tA9PcbrmtMhOuhuURpG6AOH7rPNfLHTJ/xQkul5QtBDsTT0otKPcniU5R0tRirxo3R6KuKj9mnQUcoD4xp0oPio0AYdpTwwrkEHio8KbdBRygPjGnSg+KjQBh2lPDCuQQeKjwpt0FHKA+MadKD4qNAGHaU8MK5BB4qPCm3QUcoD4xp0oPio0AYdpTwwrkEHio8KbdBRygPjGnSg+KjQBh2lPDDuX6W+2+dChtIcAAAAAElFTkSuQmCC)

   ```less
   exclusion(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![338fcc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAv5JREFUeF7tmD9oU1EUxk8MqSINiFYtxSgVEaQ4SK2gm4OIDjoUBBcVXNTRyUVEBHHSTbvWseCggyAOLqKgdRGKWMR/kSBWRWwo2lIq98G5nN7ePJtHQ1/6fVmSPO49L+f7ne/c81LovzU6J3xBKVAgdCjeSbKEjsec0AGZEzqhIyoAmDPPdEIHVAAwZTqd0AEVAEyZTid0QAUAU6bTCR1QAcCU6XRCB1QAMGU6ndABFQhSvj24U/ZVysnVb/UZufLog7ysTq4oYXLt9HP7e+T03m4pFQte9BfVSblwb9x/P7prg1w6WJG1HUV/7f3PP3Li7tg8UGGsmdk5GR79KkPPa37d5UPb5Hhfl/9O6MtQ69Z19vYWfAhK11nwseJx60LwI6f6ZPv6NSvW4apNrp1+89gOGZ+Y8m6MQXHQ3eva40/JuxbK1PSs3HhSlYdvfvhr6tyNnR2+O2gBDVTKcvVwr2zqLEmsUyxDzbfslrmGbrO2UMIWb9dpYVhwWgj2mq5zsV7X6guOERczraPYonJrNV6s07SMXsbAuYZuQaeJGQoensWxc98Oav1byqnQw/hur0J3n8OZwl3Lc7doO+ixASwG5f7Yd9/yHYTY2W/XNGrvdp91/vUjvfL04285M9CdzAH2d7lYJ/dslosP3mX0Ymu35Rp6mLrCDVurXaet3EIIZ4FmznSNF5vkF3vktBZh89HbCrpO4S7N8HFLU7et3Dn51Zd60n5LxVXz9qiDFabbHxvkCL35osq8w8Eb3N0lZ0fe+hih0yfq03L+QI/ceVbzf6CETv/86++CSd0OXv+DzvaeGWHzGxsNX3aqjg16sYGv0fN+o1h2CGt0Dz1itq5bHR0COcg1zzzZsZgBLQY09kiXdZDTn572hBAr0LTHyoxyLNm2tjrTlyxr8ECEDlgAhE7ogAoApkynEzqgAoAp0+mEDqgAYMp0OqEDKgCYMp1O6IAKAKZMpxM6oAKAKdPphA6oAGDKdDqhAyoAmDKdTuiACgCmTKcDQv8HL8hi23uWkYwAAAAASUVORK5CYII=)

   ```less
   exclusion(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![0099ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu9JREFUeF7tmDtoVkEQhU8UYyEKVmJSBAtFlICCjSA+op1oI5ZqoZ1NrAQLCwvRyiqFoIUKacRKLARfiFgJBiQiKohF0gqKTQQj4zLcuctuIOG/4cI5f5dldu/O+ebM7mYItxcXoR+VAkOCTsX7f7KCzsdc0AmZC7qgMypAmLPOdEEnVIAwZTld0AkVIExZThd0QgUIU5bTBZ1QAcKU5XRBJ1SAMGU5XdAJFVhByme2A1MHgI3r0uS7n4ALr4Ha+Ao+0emU3jv9zkHg/M5Gg7nfwNmXwIv5ZiwXe+EvcHMGuPquHvPrD3DxDfDgS1vf2dPArs3NmAP1kYkR4P4RYHRDO2b6a3nciqFvv15Dz4G7eBF8DtxjIvhr+4DLe4DhNW35Y0wJpkc/nwOOPUl/xbViQdTG+wbc9tNb6BHmxx/A7odALAIX3J3pzrWkvPX6PI/xYrEYd2u+diyEZ8eBo6NA7Aq+h7yb1MYFfRkKuHNsirfq6EZz3/X3DbzoRodlkG99AC6Np3YcYxySF8KVvQlwqYusX5v2cGpbu/Xb3qwgfi60272Pl46PZUjQWWhvne5Q8rPXXWsOvTHTuDq22jh3ajZdsAx6BJp3iHM7EvTo4Lyz7N8i6J1Voi1caq02HqE/+tac1TXo5rZDW9uXwbjx0rFQSszXV3vvEPugnO4t1ovIW++reeDEWNv9+YXv8Xfg8Ajg7d1eA4K+CtBrN2w7n+99btp77UzPn3e+5dgx7JJY+pUKT9A7hL7U7T0WwlK3dy8Ec/CmYWDybdpw6RVgl8TJceDk0xRT+n6cq9t7R/BjS46f8GeWjdXe4PECWIuJ69Te6flFUk7vCHZcNgcfQXlcDjUHVYIejwNbpwS99N8/QV8F6PrE4BXo7Tt98KlqRVdA0AlrQdAFnVABwpTldEEnVIAwZTld0AkVIExZThd0QgUIU5bTBZ1QAcKU5XRBJ1SAMGU5XdAJFSBMWU4XdEIFCFOW0wWdUAHClP8BudM3z2qKlQkAAAAASUVORK5CYII=)

   ```less
   exclusion(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuFJREFUeF7tmL1LXEEUxa8QJI0kVWCxDIJoHxLTqZBUqRPBlaCkEYtUWonYWaUQOyWwFkmdLqB2gfwDimBjowsBQbEJEkiYB3e4e533UDKwA+e87s2+2bn3/O6ZrwH5IH+FD5QCA4QOxbtKltDxmBM6IHNCJ3REBQBz5ppO6IAKAKZMpxM6oAKAKdPphA6oAGDKdDqhAyoAmDKdTuiACgCmTKcTOqACgCkX6fS9j3syNToVcRx1j2R8bbwHz3Z7W+Zfzse2s8szaX9uy8HxQc93s89nZevdlgw9HIrtOz92ZKGzEN8nRyel874jw4+HY9v+8b5Mf5qO7zlj6nedFQfdi6sCWfAeuH7jwdd9Z6Gvv1mX5VfLMvhgsIeFhZ4zpn4DD+MXBd0CUNFV8Js/N7LxfUNOfp1E52ohWLgK1Do8NVOE5K3D62aKnDGVALw46Arv+ve1LH5ZlN2fu2LhBaDnl+eVM8MTimD122oPPF8sdTBD/9R4HkzOmAg9oYC62oKybgxATy9Oq7XcFkb4q8O1QxlrjYm6Wt9Dn9ajVvVbeKzrdbzQ1r3qxn2EHT9nTISeUEBBNUEP3cImrwn60telWxszO5wvjBQMjWHz7WZVMP8bk9+I9rMAilrTc7nKQk+5Vgtm5fVKBdQWkE7nuoeYeDpRFVmO2aefoO3YRUJvWtND8GF6Vyh1a7qd3vXo5dfnuRdzFVA75evGTfcMCj1HTISeUKBpp6yih2567va7d1sIOmtYWH75mHk2c6uAfL+RJyPxSOc3ifeNidBrFFAw/uf7nptTlzL6n3qsS13K6Dd2vFwxEXqDAl5kfzsWut7lhsxDtTOBHd6P52/s7OkgVRTadpeYSgBf1JpegiAIMRA6AmWXI6ETOqACgCnT6YQOqABgynQ6oQMqAJgynU7ogAoApkynEzqgAoAp0+mEDqgAYMp0OqEDKgCYMp1O6IAKAKZMpxM6oAKAKdPpgND/AZxnARqhLNd2AAAAAElFTkSuQmCC)

   ```less
   exclusion(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![ff9900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAyFJREFUeF7tmDtoVUEQhv8I2qiFlZgUYhERg2BhFcQ3pLAUS7XQLlhY2VlYCFamsRC0UMHGTrAzvlArUSEoojYWiQiC4KOJYGRchjN3757LPRKHIfvf6rLs7sz8387s7BlZuoIl8FeVAiOEXhXvv8ESen3MCb1C5oRO6DUqUGHMvNMJvUIFKgyZmU7oFSpQYcjMdEKvUIEKQ2amE3qFClQYMjOd0CtUoMKQmemEHlCB8WPA7svA6vXJubfXgMengLbxgCFEcyl2po8eAPbfANaONboJ9A+3yuNyGA7fA8YONvPnZ4G7h3p133Ue2HkWWLUmjf+cBx4cBxbuN/PabIsN+8vtfX0D3J6IxrnHn9jQLRzNcHG/bfzoa2DD9n7BLYg9V4FtJ/vn/PoOPJkG3t8ESsB1hfUjB65zgoOPDV0B/V4EXl0Enp9LspbGSwchn7fwsKkQCsZeE1oVFKbateu0Kozua6pF2zr1N1jex4VeylrJxsVvveVeBJXxLy+ATXvT/1LGSoZ+etT0B6WM1YOgtksVQvcXW1IxrD17gOz+hD6kAv8K3TZ7NvslG99db6ArUFvKJYvnLgE7zqSDZfsB3Uv2l6qzcTL1DrYfsHuVeokhQ//f0+JmelsZbxsfdA/LmrwEl5QVgHKnT0yn18Ig6FuOpP6B0Jf5jHa508V0/oz7/AxYtzllrS23tooItB8fU+ZK9j893dz7zPRlBjrMdl2h53vmJbnUWOUl+eWF/mbPVhe9w7eeSOWdd/owJDvM6Qp96g4wN5Pe2/ldre/wyZnUDOoByDt1GR/UvWsvkPcL8i1A19mD0CFcr6kr504XxUrNX9tzL1fYlvL8mtC5+V5t3wUCN3ESysqGXsq40seZ0vMqB58D14OQgw8OPD50r3pXmZ3YmV4ZDK9wCd1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2/gDMLCvPFhHnsAAAAABJRU5ErkJggg==)

   ```less
   exclusion(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAArdJREFUeF7tmzFLXEEUha+QQhsJrIWFlQgmEBDERrQyhYWQ1mLthNgJ/gAt9AfYCRZ2bmGbai2ylWIjCzZJhCAWIimyEELAFIGVGbjLdZz3YHQWfDnHan3Ou+M535z7Zh7sQPdjtyv8gXJggNCheHuxhI7HnNABmRM6oSM6AKiZz3RCB3QAUDKTTuiADgBKZtIJHdABQMlMOqEDOgAomUkndEAHACUz6YT+sh3o/O1IvVWX45tj/4+efDiRudE5/3mnvSNb51v+8/bMtmxOb2YVc/nrUpY/L8tF50KmalNy9P5IJl9PStH1rJNnLlaZpN/9u5ONsw3Z/7rfs0ChN743ZKW10rtuoYcLJVws7vdY7cOFQ6lP1H3NsIZCHxkcebAI7WLIzClrucpAt4myCbfAwoSf/jiV+U/zjwyz99u6dqCFrnVCqEXXsxLqQ7HKQC8y2KawKJ1F7d4umLW3a7I7uytDr4Ye2aydZHFsURoLDakN1vyYout94JS1ZCWg2+e1qncA1t+ty1JzKZrk6z/XvuWXwdSFFMLUgrG27/62+mbVDzn4dvBg7rK5slJ7ZrH/FnrrtuU3di79V7+vops8TarrBOPD4719gcJz3ob7CEJ/5opLuT2lvRclVOfTdh/rIDrGppbtPYVUxrFPhV4Gb+/Lnu8Atr3H5iH0jCBTSqVAd3U1xbFduEJu3jR9S7cbPd3Nuxp6Fif0FFIZx6ZCj4HShaDpb/9s+yOdPYrF7iP0jCBTSqVCj72U0fn0nF727LcdgtBTSGUcmwrdTR1CLXpjFm7o7MubsvM4z+kZAbNUfx2oxDm9vxbgVSd0POb8Lhsgc0IndEQHADXzmU7ogA4ASmbSCR3QAUDJTDqhAzoAKJlJJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEDo9xNuuafVlZOIAAAAAElFTkSuQmCC)

7. `average()`

   分别对RGB三个颜色值取平均值，然后输出结果。

   Example:

   ```less
   average(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![803300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA01JREFUeF7tmM+LjlEUx880zSg1K7FRMsqGUhpskAYriqSUhZFGYfwBpMav2bCxMyxQxs5GplhhsrAhkWJBmUn5kUmpt5SZJrpPnafzXPfO+7zHTHNP93t379s99577/Zwf9z5t53voD2FkpUAboGfFuzgsoOfHHNAzZA7ogJ6jAhmeGT0d0DNUIMMjI9MBPUMFMjwyMh3QM1QgwyMj0wE9QwUyPDIyHdAzVCDDIyPTAT1DBTI8cpKZPnD3LS1dtabEMfnxHQ0fWFvBs2fwBq3f21/+1/j+me6d66PxF0/K//x1pn416MGlk/Tm4Z1yTt/wI+retKP8PTM9Rc9uX6ax62cr+/nztD6lEGPJQfdBsUhSZB84z5Hg1+06RLtPX6XOxV0VnSX47o3bad+FEepatrwyxwfvA9f6lAJw50NS0CWoV/dv0ujQUWLBGZZzmmFyIMggYDu31ob9x+hW/5ZC697jF2nz4VPU3tFJPMdB7z0xRGPXBosKIfcff/6YRgZ2Vuz4P/aJg+PHpw+1fAL0gAIsenvHorLEMqyZ6d9FaV6yYnUBzw0uwzJjGYy/PAdGqMTzXN5Lrh2y84OzMflF5dNCBUFSme5EiJVSzs4YPG4Ls7WBUL+WFYAh8F7SH9k6/CD7+XWiuF/4ARXyaaFAy32Tgx7qs1Jwv9zzpawOdHdw/wIWgi7343Vng+7WdZdBQFeEdKjv+pBX9mxTZZVcO9YCZNnm4OD9kekKoHVMWGCZjTLzXdl1w5VSWarr9HRnV6fc+pC3HjnzTxb7Pf1/fKqjy1zPSaq8h0q3n/0TL59Gb8oyEA5eGaVv71+X7+1Qprv7gRvuleBGKNNDdq28KGLv/rkG2cp6SUGPva3dgWS/rPNujs2R68Te+z6o2LcD2Sbq+NQKmPmcmxT04kIU+GAS+trW7AtZswuan9kscuxJ54MP3Qua+TSfIFtZOznorTiPuToFAF2nm2krQDeNT+c8oOt0M20F6Kbx6ZwHdJ1upq0A3TQ+nfOArtPNtBWgm8ancx7QdbqZtgJ00/h0zgO6TjfTVoBuGp/OeUDX6WbaCtBN49M5D+g63UxbAbppfDrnAV2nm2krQDeNT+c8oOt0M20F6Kbx6Zz/C8QP7xUjAQ7eAAAAAElFTkSuQmCC)

   ```less
   average(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![994d1a](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA1hJREFUeF7tl0toU1EQhqdqkNaCWh+RqAvxQTb1gd2IRclCXEgF0SLixo0gFAuCrtSVulIQlILixo2IWBEEF9JFUCqoVPBRIVRdiGgtWERQisQXc2DC5PTc3Bux5uL82SW595wz/zf/zJymi125X4SPKQWaAN0UbxcsoNtjDugGmQM6oFtUwGDM6OmAblABgyHD6YBuUAGDIcPpgG5QAYMhw+mAblABgyHD6YBuUAGDIcPpgG5QAS/k3OqNVDh0jmbNW0Tvng7S7eO7/ztRUun07r4izV26qiJ2aeAq3Tt/uEr8bSeu0eI1nZXf4gDJml/HP1DxbC+9f3Y/CDMOesfeI7R2Zw9NfB6vuU6aMyVV0LXgvmgaqp8U8uyntyN0vacwSW8BNW1Ghv4U+srCLuo8cIoyza1u/bh1AD2hApsOnqH8lj3083uZntzoo6Erp0kcXZ74QoMXjtLs3DLnNAYoFSD0nmzpJ1IcrJDTddLIunHrJAy5IY+lyukCWAsqDpuemekSoaUt6xJDkuBlsZ80KL8VyJpcBbhl+LB8B4+Vhqh1wZKqni7QR188dJC4rYSghypQqDU1hLTaNJXQtdPFxXxmFpA/DF2+c6/XTtRtQN5l4GOlx+49DSvkYA0kNCeEEpPfiWpNOpZGw5b9UwXdd50vEkN/dfdmZboOiSig/IFrxeYdk6CLM6OSrB7oobPI+nFD5r9OhlRB5+B99715NEC59g0k5Z37fK2SzIkxOvzADV36HXG9OJ33kquZHgDjpvcop/N6/o1CYAJ6nWktsHQP95eQROHfue9n8x1V17nQlh9fD1PznPmud/8N6Hp2kBsEnJ4ANrusfft+unNyn3taO1qD2XrsMj2/dcndtbUzxcXruntjobP7WtoWuuFOJ5SeIeop7wJYzqnPDqfXgB81DPkuD03JcQOTX945YTTgWvOB/i+qvPulnc/zo/zN3esBvU7oSa5GtUq/bBeCzv9p8AxqpNhPyzu7ImElnd55tsjm17tKAugJyjwemVoFUje9T224WJ0VAHSDeQDogG5QAYMhw+mAblABgyHD6YBuUAGDIcPpgG5QAYMhw+mAblABgyHD6YBuUAGDIcPpgG5QAYMhw+mAblABgyHD6YBuUAGDIf8GZMm2H+RAvoUAAAAASUVORK5CYII=)

   ```less
   average(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![b36633](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAz9JREFUeF7tmD9oVEEQxicSMQhCiqgoIUUskkJCQBIDQmwEJUUsBEEERQTRWNnZiBEbsdAu2omCCHZGEEUbCyVEBRGLJGAKlQhygn9AIgaVeTCPucm+u0047vac73W57Nvd+X77zcy+lvsndvwlPK4UaAF0V7yzYAHdH3NAd8gc0AHdowIOY0ZNB3SHCjgMGU4HdIcKOAwZTgd0hwo4DBlOB3SHCjgMGU4HdIcKOAwZTgf0xiowdGaCOnoHaWnxJ729c4k+Tj1o7Ib+09WTcvpKoO8ev0sbtnTnWIoOSkfvAPUfu0Bt7ZvysaWZaZq6OlaGVNaWHxe/fqbXN85TaeYFdQ6N0PZDZ6m1bX3+zo9P8/R0/GDZHLF7avRZakroIQgspAXfM3qStu09Smta15bprKGHDgUP1tD7jpyjrl37l7HS4GP31GjgvH7TQu8aPkDPLx/PNNRw3z+7R29uXSQNUwO0oovD/yz9pnePbtLs5PVlXBg6PzwvP6GMxNCr7SkF4ElDn39ym7r3HM5Taigli4jiRO300G9WdO1OOSwxYCSNh1J8pT3FzF2PMUk6vShwDcamXOtUcSOD+fWtlDWINm1LhuB3F149ps6dI1kpCLne1utQ9qi2p3oAjVkjWegCWLtROytUZ/X/LSQthgDbOrgvWKt5rAUfms9mh2p7igFSjzFJQrcuEtcW1WZd06UMCKRQyheg69o35tBDh6yopMT0AaE91QNozBpNAV0AVmrIbJ3V6V2uVgKCheGmjR/u7vkQyHcB3QAWQY/tBWJqfwykWo9JEjoHKc7TjpH0PXD6Cn3/MJd32iFXSarVadp23byO3L8FsL0JLEw/pJ7RUzQ7eS27s+vuXc8ds6daw1vtfMlCtwGF4NkxOpUX3b/5He1g+1FG5pSswn/bjzsyRvcQRfOk+HUxSegs1Je5l7S5bzjT1zZVoY8uRanfNmChq5ltwOxVLATUpv6V7Gm1Dq3Ve0lBr1VQmKeyAoDu8IQAOqA7VMBhyHA6oDtUwGHIcDqgO1TAYchwOqA7VMBhyHA6oDtUwGHIcDqgO1TAYchwOqA7VMBhyHA6oDtUwGHIcDqgO1TAYchwukPo/wAW6t/3OzN3rQAAAABJRU5ErkJggg==)

   ```less
   average(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![cc804d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAyxJREFUeF7tmLFr01EQxy9WrZbGwShKMZgsImhbnZ0iCIIoSEfBTRDFwcFREPwDHERxFhxFEDo5dKlLqYiVLFWIUhC1RlsDalPTyntw4fp8vyaXkuXeN1ub+737fb+fu/fuJTd75/w64ZOUAzlAT4q3Fwvo6TEH9ASZAzqgp+hAgppxpgN6gg4kKBmdDugJOpCgZHQ6oCfoQIKS0emAnqADCUpGpwN6gg4kKBmdDugJOtAHyYWxChXPXaWBnbvp2+sX9PH5/T5k6X1Jk50uTWdrYuYfuXyX8uXxtnt/Fheo+vB61E25ZqP2huYf3850HdB7L8ienowBj4EPgXNMFngZD+g9oenfQ4cv3KB9J89Qq/mbFiYfUbNRp/LFm7QjXyAGOlK5RAdPTVBuYDsxQIa63vpLn18+pU9TT9ovKePdPwG9T/xCo0MYDJfTM+Th0nEPfbVRp9qze9SozREDZehhYdTnpihrS86Xx3zRbBscorWVX754Qujhuy7Pz9BwaRRnuqY2YluvhH7s2gPatb+4YclYZ8sA/t4B5vVlYTDcECrncjPB8KGjPq+EHhZfqBODXBfkZdfI87V49gq1fKft9Z0cbrPliVv0890sOahh54WxDLITdC4OhszP8d+yUGJF5fICehfQY1uvfCzWpbHvNzvTu+n0laWv/x0TIfSsiR7TexegZchWoMvOkx3Gnb/WWvXDXeHEaX9Vk90pQS2/f0VDB0r+/M76uOPGxbmc7j4ut3xAV0LfyvbeXPpChfHKhkndpefOZsiDhZHM6d3FLM5MttfZDPr36jTtKY/6fLHBEdu7An5sUONBrvHhbfsKFhvUeHqPpZMzQixHp+tYuL3LgsqShzNdAT6c4OVW7JYJoclO6zTI8WuEa3S6f8egh+Dde/6oTvsroPsdANAV0BHaPwdM/vbeP7tsrAzoNjiqVAC6yi4bwYBug6NKBaCr7LIRDOg2OKpUALrKLhvBgG6Do0oFoKvsshEM6DY4qlQAusouG8GAboOjSgWgq+yyEQzoNjiqVAC6yi4bwYBug6NKBaCr7LIRDOg2OKpUALrKLhvBgG6Do0rFPyslvAFKR4rjAAAAAElFTkSuQmCC)

   ```less
   average(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![e69966](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA1tJREFUeF7tmD2Lk0EQxyeXeJq70+QUEbX0hZQKouKJYHWIYKdoI9jYWPkN7htYWwk2FnaChYUIomihINiEyJXmEFETNMldTC6yy80y7s0mjxY7T9hJFZ7ss/Pf+c3bptC8f3sE+knKAwWFnhRve1iFnh5zhZ4gc4Wu0FP0QIJn1p6u0BP0QIJH1kxX6Al6IMEja6Yr9AQ9kOCRNdMVeoIeSPDImukKPUEPJHhkzXSFnqAHEjzyVGf63st3YefhmsM27LSg9eIB9Jt1+8z/feNzHb4/vfcX5t2nrsD8iWUozJTsc38PXDx7qAbVi7egOF9173P7TdKUhxibSugcAB/Y/qsrUFo8uM3Hgx9r8PXxin1euXAT5mpL29aMfq9D+9Uj6H16a3/zAwNfoNCzaMoDcKNhKqFjNo02B9D58Ax+vnsSzN5u/TW0Xz50gPGdjWbDZS4GQvnYWaicvwGFHbsAgVKYoSpAq0pIU16A5xa6n4E0OykYBOo7FN+nGUvhmff6aw0HmO6DAYU2ub18e1k0KfQxHvB7Ii5FCFhqYTiA3up7KB8/Y/sxzTAaNAiUlmiTxb3GGwcd9+ayeuHkJTs3mDXDbtvNEDTrs2hS6AEPUDAIC7MIiiVbymfmqmwfNltypZszhaU7FGB0PqgsXWdnA7qmfPT0RE1+C5IMglz19NBghQ4ygWA+OHz5gUF7MS255p3+l1UoLuyz0zct53TgM9k7/PUNZg8csZltBj78nbYKV/K3ZgoaiOM0SYKmtqcO+ma3Za9YprzjhE3LMneNohO4+c4Nf+Y5t4/f47m9zLP/0SQVBLmC7nrjGDDchM21hcXlO9D5+Nze2UMT+J5z12DUX3fTP3cr8LPalGm3butqZ+D5Uz+nSQqybzdX0I24LPfrUC+mwxW3j3+dCrWTLPdvo5Wuy6JJof/jBO+X7XHXOi54/D9cbHYyf86EroF+EHHrJmlS6HnxQII6clfeE2QQ/cgKPbrL5Q0qdHkG0RUo9Ogulzeo0OUZRFeg0KO7XN6gQpdnEF2BQo/ucnmDCl2eQXQFCj26y+UNKnR5BtEVKPToLpc3qNDlGURXoNCju1zeoEKXZxBdgUKP7nJ5gwpdnkF0BQo9usvlDSp0eQbRFSj06C6XN/gHKELV2ZYCNl4AAAAASUVORK5CYII=)

   ```less
   average(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![ffb380](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAw9JREFUeF7tmD9oVEEQxieFggG18ISgEghE1EbBXKegFnIBuyAYOEEUglhKRJsoopX/S5FAqhwohCMiiGk0ggpCCESLRFACQTHFSSBKBC0iuzCPubl95yXPkyXzvXJ5u2/n+803s/talp9cXSY8phRoAXRTvH2wgG6POaAbZA7ogG5RAYMxo6cDukEFDIYMpwO6QQUMhgynA7pBBQyGDKcDukEFDIYMpwO6QQUMhgynA3p8CpTG39HJO2W/sbPdebrXV6AN69eRHj93LE+n7o7S1Ow8vbp5hg7saY8vmEh2FLXTX0/P0cGLQ4lUDH3y09ea8ZVA//ClQidujPgE4We4v4eKh/dWYZGJpZOOX/z2fYmKt8s0NvnRD+3raKNHl47Tru25SBDXbiNq6NcfvqQrpRdVDnchhMYlyL85XcMMgU97R1YbDZzXiR18tNB//vpN5wfH6MGzCbpWPEKXew95TdPGVwL98dsZ2r0jl7hRJ5H7Dn+bKwBXHQmU5xX2d1LpQo/fH7te7jk2y0cJPVR+nXC3Th+l4fH3VWXZjTsw+Z3bkpLtnP58atZXibSyzCBkEjFgOcZVg109v/DDl+/2rZtrEsOtyRVCVgRAb0CBrNBDn0irFvyudqY+T+gWIEu7bCc8j92/ZWNrAxH/31eidHq9Mt5IeZcAdQl2EOQa9fowz+V3JMi0dgLoGRJ4tT3dfVKenhmOHpdbq9eb03p6blNr0r/h9Ayg5dR/BT10ANNb1IlRWVzyV0Ldlzk5XCJ0d3Um0OV1Dz09QwKsFrq7e3N5l2vIE3b5zTT1FbqS3Wmnz3yueOjypC57OENupEJkkKBpU9dkTw+pxaDS7tZ8C3A/aEI9P9T70w6cMR/iXBxrEvroQC/dfzqR/CXTf9v0Ac0JEfqhU+8gx0mgwccOPGroTattWDhep4NN8xSItrw3L2SsDOgGcwDQAd2gAgZDhtMB3aACBkOG0wHdoAIGQ4bTAd2gAgZDhtMB3aACBkOG0wHdoAIGQ4bTAd2gAgZDhtMB3aACBkOG0w1C/wP2fCHyUNSa+wAAAABJRU5ErkJggg==)

   ```less
   average(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![ff3300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtlJREFUeF7tmD+MTUEUxr8tKCRKGp2SRPNCIRKyVCJRbKJji5VYRE2zhE2ESHT+FESQKHQUomCz25JNNBQKnUZJKChWxs3JnD3mXne9t5vJft+r3pudmXvP9zvfOTM7tjTAEvShUmBM0Kl4/wlW0PmYCzohc0EXdEYFCGNWTxd0QgUIQ5bTBZ1QAcKQ5XRBJ1SAMGQ5XdAJFSAMWU4XdEIFCEOW0wW9QgUOHwcu3AI2bW5e7tl9YPYk0DZeYQi1vVLdTt89Dlx+BGzdlnVL0F8+KY8vLixPkLTq0wfg2M7luj99D2zfkcd+fAOunQVePM5jbc9OCec/t18Bew7mkdLzKqNeN/TpK8DkeWDDxuzwJGDb+Mw94OjU3xJ7ELFC2GwPvgTc5lmlSb8jcJtTOfi6oRvEXz+Bh9eBuxcbWbvG09/NjQbFA03QJ04BU/uavUoJZOvsuYvzubJ8+QxcOgEMDuSEfPMaOHMoJ0F8Xzm9pwKxBKdlCd73r8AWV+5tPJbnNG57dDnPEsgnRmldnDfY31SVmFB2/vAVoWfIazWtXqf/L/S4zpz5di5rGtuAd6Yv7eZgXxHS91R1du1ternfv23tWtHs+Zx6of+rjCeXlcpoKVmi60q936qB7/ld0McnmsOgoPdMtb7TVtrT476xN9uZwM/zPT1BfnA19285vS+pEc4bFrp3bVeP9T38xrkM3Z8FYk8/MtmUd/X0EQJfaXlP/fT0LHBnBrD+XXL6zefAx3f5JhCdXjqF+9O7JULXutK9f8TSDLPd+unpXXdr79i2u3WbY7268QxROj+k+b4tDENnldauH+hJoBLQCMA71EQtnfDjP3Ha7t4RfOXAU8h1Q1+lTGffVtAJM0DQBZ1QAcKQ5XRBJ1SAMGQ5XdAJFSAMWU4XdEIFCEOW0wWdUAHCkOV0QSdUgDBkOV3QCRUgDFlOF3RCBQhDltMFnVABwpDldELovwH+nQXjcCyG4AAAAABJRU5ErkJggg==)

   ```less
   average(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![80b300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA2tJREFUeF7tmM2LzWEUx8+gQbolxoZpmCSFplGUoiYvO6UsLYwFpfgD7CQL8QewQhlrqZGdlyZMdm6Tl8Q0YtSUQbkpJjP0/Or8OvN4fr9zf/fexdN5vnc3d87zcr6f8/bcrgv36C/hk5QCXYCeFO/MWUBPjzmgJ8gc0AE9RQUS9Bk9HdATVCBBl5HpgJ6gAgm6jEwH9AQVSNBlZDqgJ6hAgi4j0wE9QQUSdBmZDug2FBjoPU6Hd1yl7mU1mpp9SCPPD9lwrENeRJfpEhj7+OLjDRqdOLXI5eE9D6i/52D+3ZfGa7o2tj37uwr0/Vsv0t7N52jpku58r1CgHBm4Tjv7TuY2jV+f6W59mKZmH+Xf+XefX5ijZ5NX6PHb8x3C1ZltooIeAh4C7wNnGwZfBXrRXhK8D5zPk+CL7h4j+Kigs7hzfxp0/+VZcqIeHRyh2ooNxEBlZjIYBscCf/35runyfmz3KM38qOfZeGboFa2rbcvOdpnszuZWwXeQQcBViNfx3V1g+Os6k6ft7xIldJlBDNQXnMWdmL69qJw7CB++jS2C7mTiViDX+fL19xzIg4wDioPM2XKp9u2evL/03zpnz3cPtYL20bW+Q1TQpZjSJQkqJKQPoT59K4cekkZCCJ0p5wO/+rggcx/ObGf7dPJyfp6cP4rWto6rMyujgu5c0gYrv/y6QaoMeqhqyD4bgi7/z0HmVwgJ/c3MnXwYBPSKgekLHOrpVTNdQpDDVuhFIDOYIW9aM5RN7WXQkekVQbO5zDgJhDN/fuF3NtwN9p7I+nOzPT20l3ueFUH3e3ht+foMelF1cL1fthM59aOnK8Egocue6mf/2lVb8lLqT++hyVmWdy7Jcsrf1Xeabo7vy2/XzBTOvVoGQtm62H4giqqnF72HHREZCCywH0csbtl7X+5VZhfKWP88eafQLOLsy14LLRbFtpdFBb2ZQY499sFLSBLmp+/jtHrlxuy97QeP7OFSyaq/APJaH3yMwN1do4PedhhjA1UBQFclsmcA6PaYqh4BuiqRPQNAt8dU9QjQVYnsGQC6PaaqR4CuSmTPANDtMVU9AnRVInsGgG6PqeoRoKsS2TMAdHtMVY8AXZXIngGg22OqegToqkT2DADdHlPVI0BXJbJnAOj2mKoeAboqkT0DQLfHVPXoH/xRIySBzm9fAAAAAElFTkSuQmCC)

   ```less
   average(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![803380](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAvVJREFUeF7tl7FLXEEQxkckFgErMWXw8gcIokkjFtdGMEggYGMINub+gjQhhjSpUl5SCbFMIxFMe5VNQhAsLXKWEkshhUES9sE85i27Ucw7voX5trvH3n6z329mZ3dsa37rj3C4cmCM0F3xrjZL6P6YE7pD5oRO6B4dcLhn9nRCd+iAwy2z0gndoQMOt8xKJ3SHDjjcMiud0B064HDLrHRCd+iAwy2z0gndoQMOt1xkpfc+9WT63nSN4+zHmfSf9Bt4Vl6uyNyjufrb+c9z2X21K8Nvw/pbvM7FrwvZf7svR1+O6jnr/XXpPOjUvy9/X8rBxwMZfBg09NqKqYQcKw56bK6aZMHHwHWOBT/7cFaWXyzLxO2Jhs8WfOd+R1Zfr8rkncnGnBh8WzGVADzEUBR0C+rw86HsvdkTrUSFFYJWmJoINgn0f2GthccLsr2xXXnd3ezK4tNFGb81LjonQO8+78rg/aA6Iaz+8OtQdno7jW//GxOhJxxQ0wMYPWIVVqi+cDRP3Z2q4IWhc2zFKqx4eU2M1BGvc1XLrj3KmFBJUFSlBxPiHqvGaJXl4OkR/K82kOrX9gSItfR3mzGhQFvd4qCn+qzt1fFxr5ey60APG48vhSno8aWwzZgIPXIg1XdjyDPzM9WtPT6mU9Dt8nbtXAuwPV2TY5QxoRKgqEpXwLYabZWFIz6MAN0e1dfp6eF/VyWGbS9a7UvPlqon3ahiQoAvErqt4rjSTr6fZG/vNhHW3q3J6fFp/d5OVXq4H4QRXglhpCo91U5uGhMCcEqzKOi5t3UI3CZC7mJlqzE3x66Te+/b5GkzJkLPOHDVpSl3o77JBS0FNPWkaysmQi/FAYdxFHW8O/QfsmVCh9iOFSV0rP8QdUKH2I4VJXSs/xB1QofYjhUldKz/EHVCh9iOFSV0rP8QdUKH2I4VJXSs/xB1QofYjhUldKz/EHVCh9iOFSV0rP8QdUKH2I4VJXSs/xB1QofYjhUldKz/EHVCh9iOFf0LRPH5Fac8ndIAAAAASUVORK5CYII=)

8. `nagation()`

   与difference函数效果相反，输出结果是更亮的颜色。（效果相反不代表做加法运算）

   Example:

   ```less
   negation(#ff6600, #000000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtJJREFUeF7tmLGuOVEQxuc2Ok/gDXgDDcEb6CQo6FQqnUIhVArRUSDReQPES9BoNLyATnNvziZzMvfc3b3yJ/4nd74tN8fuzPebb2bsBxF9Ei5VCnwAuireQbKAro85oCtkDuiArlEBhTljpgO6QgUUpgynA7pCBRSmDKcDukIFFKYMpwO6QgUUpgynA7pCBRSmDKcDukIFFKbspdM3mw0Vi0WL43g8UiaT+YZnOp1So9Gw9y6XC9VqNdrtdvZetVqlyWRCyWQyuHe/32k4HFK327VnCoUCLRYLSqVS9t5sNqNms/ntfa+KyYca8w66Ky6LJMG7wPmMBO8C5zMSfBhwPifBvyomH4CbGLyC3uv1qNPpUCKRoO12S6VSiVhwhnU6nax7uRBkETCsw+FA6XSabrcbtVqtQG92Pf/OffZ+v7eu5wLK5/MviwnQQxRgeAxquVySdKwBer1eAwjm4lYtHWuKpd/vW3hcPOY8Q2ag4/E4KIywLsIx5HK5YIw8G5MpYF8ur5zuQjHz2QV6Pp9/QDBisrMNwMFgYF0t27QsKuN6U1BmlsvC4G7DRZXNZoP9Qo6Of4nJ3Un+ZwF4BZ3BxQlsxDIQpPNc6Ov12rbkKOir1YoqlUqw5MVBL5fLQTd4NiZAjyhzOP09/vfK6Qw9bn4aWcyMjdrCjWvn87lt71EzfTQaUbvdDtp73Eyv1+s/Oou7ZzwSE2Z6REHHbe9xWzjPalkIcdt71D8Dub1zIbwypvf4+Pe3eOV0OZvd0MMc656RjpWw5LmoLiLPuB9xuICejel3HO854R30MPASOMvyyBcyF7y7/JlnPfLV7pUxvQdr/Fu8hO6DMH85BkD/y3QjcgN0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0QFeogMKU4XRAV6iAwpThdEBXqIDClOF0hdC/AF80Cy4arLeSAAAAAElFTkSuQmCC) ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=)

   ```less
   negation(#ff6600, #333333);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuBJREFUeF7tmDGOKjEMhr0FDQfgAFwBwSFoKJBoKWiAE9AghGioKIGWlo6GQ8ANKDgA9DQU7ykjZWTyMqt4NEh5zr/NakaerP//i+1kf1qt1h/CT1IO/AB6UrwzsYCeHnNAT5A5oAN6ig4kqBkzHdATdCBByah0QE/QgQQlo9IBPUEHEpSMSgf0BB1IUDIqHdATdCBByah0QE/QgQQlR1fp4/GYhsMh1Wq1HMflcqHpdJo/d7tdms1mVK/X83f3+50Gg8EHwuPxSM1mM3/3er1ovV7T+XzO3223W+p0Ovnz+/2mw+FA+/0+f1dlTjHsseiguxCsSRz8fD6nXq/3j38cvG9jmA84+Ha7TcvlkhqNxsdaLviqcooBuMkhOuibzYZut1teabZaH48HLRYLul6vZKCbn9Vqlf22UDhQA73f79NoNMpieLWeTqfsWwN9MpnQbrfL1uUbhW+yqnIC9AAHeCW6LZ5/bjeGr8XbONsdfC3extiNYZ7dFm9jqswpwIKvhERX6b6WGzKveSdwQdvn0HltO4EPtH1XNqevUBQu+l9A98FyD2lGtwvLN/tdWL5DmruBfBuxbE5CPl8Jjw66q9LC/a0t25nuA+G2bnMrKBoVfKb/NiqqyukrRAMWjR56yJzlsNxql85+u4F840Iy+0NzCmBUeUhU0N0Tt1HrVtXz+fw4cfPTO69098TN27itdPcW4Kv0KnOqnF7JBaOD7v7TxeqyoIru1iaOt+SiuzUfE0X3fb55iu775u9JcyrJqPLPooLOK5srdVu2D6g7p0MOaD6gvrNDyKExJKfK6ZVcMDroJXXgM4EDgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHYAuMEtLKKBrISnQAegCs7SEAroWkgIdgC4wS0sooGshKdAB6AKztIQCuhaSAh2ALjBLSyigayEp0AHoArO0hAK6FpICHX8BRVxmASBhBlsAAAAASUVORK5CYII=) ![cc9933](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAx9JREFUeF7tmDFoVEEQhucSjki8KGI04SQRRUWRiGBiIUGwErSwEAUrCxu11MZGRNJYWWosrQStFBSthGBlIogiaAxKFA+jRgnnBc0RI7Mwj3l7+y7mTLHc/K97d/t2d/5v/5l5Lzd2Y88C4TKlQA7QTfF2wQK6PeaAbpA5oAO6RQUMxoyaDugGFTAYMpwO6AYVMBgynA7oBhUwGDKcDugGFTAYMpwO6AYVMBgynA7oBhUwGHLTOn3nsdu0Ys3mBOm313dpcmQohXjb4WvUsWFv8lv501Mav382NcafZ746Sx+fXKHptw+Scf48C3+q9Pn5TSqNDUd5pJoOekdxgDYduEz5letrBNdQfZgy+NePd/TqznF3u3brIeoZvECt+fbUXBp81noxg2866Bv3X6TO7UdIiy5OFFhtq3upe/dJyrXkSTJA6DmG3rnjKL25d8pBL/afrnmOoRcHzlBp9DqVS6OpgxLKHDFYP1roIQdpETUAFlIgF7p2uZRdrXyh948vpUC0tOZd2s23r3MHI8uxoVLAa8jBCKV4gSn74vtYU3yU0H2gIqhA92toCLp2usDicQyUL4Yu91zr9Zr6cOln9Tq6Xof2m3Vw4PSAAtrh2lGcalf1DtLvmQ9JitX1t2ffeZqfq7j/Q3VYlmIY3yceZtZ9HlcPOv+v1/XTvqyjM00MoPUeonO6bp5CbllKiuWazdfM5AgViv0k6Z1d6jdpP6deUFuh2zWAWS7NygZaUD2vfzhigd+U0H1xl3JQFqvF0vXXAyrlJ1a3Rwf9f9N7ufSMuvpO0MSjc459lvO2HLxKUy9vuUZPr6lB8ZjZ6fHkfTvkdD5QfMk3ADi9wXzmN09+Ixd6x5bGjaGH3tP9jrveHNKkhRpG3oueK2uveE9vAP5iHbEPREDMVb7WQA+l2X/50hbagz9X6ANOvVe6BqRY9keiS+/LHiEmrFEA0A0eCkAHdIMKGAwZTgd0gwoYDBlOB3SDChgMGU4HdIMKGAwZTgd0gwoYDBlOB3SDChgMGU4HdIMKGAwZTgd0gwoYDBlOB3SDChgMGU43CP0v+/SRz6Xga+EAAAAASUVORK5CYII=)

   ```less
   negation(#ff6600, #666666);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD2KAkEQhWvBwExTb2Au+IMH0MjcRDAx9wbewCMIJhqbeQDxB8xNjE01MxB2KaGasrZnYXcHbKhnpM04U/W+flVd8zEajT4JH1cKfAC6K97PZAHdH3NAd8gc0AHdowIOc0ZPB3SHCjhMGU4HdIcKOEwZTgd0hwo4TBlOB3SHCjhMGU4HdIcKOEwZTgd0hwo4TDlZp4/HY6pWqwHJ9Xql2WxGp9MprA0GA2q32+H3/X6nxWJBu90urDWbTer3+1QsFsPaZrOh+XwefvNzhsMhlcvlsMbPmU6nL1sir5jevc+Sgx4DwCJZ6JPJhCqVyot+FrrdFHKxht7r9ajT6VChUHi5l4aeZ0zvBs7PTw66uOnxeNB6vabVavVNJw3TulYu1g6/XC7Em8R+NMxYJZHr84opBeDJQdegsmBqULESbEH9BFM2T6wtxDbPf2MC9IgCUmrZ5cfjkRqNxrPsatfLxuD1/X5PtVot9GsNRco/b4xSqRRagXa9OJjXbrdbOEPojZJnTIAeUSCrB/OlAp6/x3qw7teHw+HbwUw/TsDHzgVynYCv1+svh0V9n9/EpA+O74afVE+P9Wpd8tm15/M5QJfyrks+A10ulwG6dq04W8p5t9t9VgBd3iUGAconepkQpJL8JabYmeJd8JOCrkupjF62h2+32+cIxuVdH/TEtdbFuu/bHt5qtZ4lXZd8iYGB8P2lsvAm+G9M74Jsn5sUdOsgnpP1SMVO06VbYMX+Z13Ns7tsDFu69ZnB/o8FkzlfNtBfYwL0DAXsCxDbY1n4rN6vy3TspYzu+9xjs+Zvvk5XiLxiAvQfFLBQY3O2fakSG80s1KzZ3x7oYqNZXjGlAD6p8p6CIB5iAHQPlE2OgA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynA7oDhVwmDKcDugOFXCYMpwO6A4VcJgynO4Q+heo/tTjOytJAgAAAABJRU5ErkJggg==) ![99cc66](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0ZJREFUeF7tmE1rU0EUhk+s0aAtUSumqZRS1BCQSgURvxCkgorgTsGNIIh7/0H/QVcuXAluFN0JLgqKIFZcdCFUIVRBbDFasPjRKtFYKzNwhuN0RpP2Xrh43rsKYe7MnPc575wzN3dt4vIS4VGlQA7QVfG2wQK6PuaArpA5oAO6RgUUxoyaDugKFVAYMpwO6AoVUBgynA7oChVQGDKcDugKFVAYMpwO6AoVUBgynA7oChVQGHJmnb6v9wwN9ZygNbm1FsvX5id6+Po61edrDtOu7gN0pO885TsK9r/mYoMez9ykl3NPE0Xpr2Mmr30Yp0dvbrh1eruqdGzgIm3Mb3L/vZ2v0b2p0UT3ksRkmYR+tP8CVbceXhafhOonBQ/+tfSTnr0fo4n63ST0odheJPTYXgC9RQTSMR8b7+jOixGSTmMhz+4eoc2FsjsBzPTsNH6vxSWjw+S6sTnlfkOn0Wr3kMb7mXO6FFq66XTlCm3vqpIRf3z6lgMs3cSu9MX33eqXAU4gFpgB85p/g8lzp1Va1EFn8X03Tc7ep8HScVs/JRCGJwH4QGXtN79lT+BD53dNYm3IF+3JYh7pepmM35qfbWLGepA0AK5kzsw53QTBQoYCYsg7t+wP1n0JtdxZcWPkiTA8cImmvzynoZ6TFqTsA0yCDZaGaXL2wbLGTO6HwYeSisdl9bjPJHQjmhTTiLfwfY5KnTuCLmPQ9YUp6i/uce7fWz5lnRcSX54eoYYrVqs5Ifk04cSRpwsf+Uk3lStxdeidzEKXm/0XIB7LidJKTW5lTnm889XLr+GV7oOu1zBNp3m4mze/k7xJ/NfQD/Wdox+LDXftYndJ5xhh13UU6MnMbauFbNa4AZT/tXu8j7266spMqEfwy4zcm38aJP3dYLXwM+n02N1YgovdjWWTFfpgImt+cf22Pz4A+Y1c6KMMj+HEiq1hxuGe3kZ6hqD7X8BC0GMi+82WrPEhsHIeH2qsTvtr+PttI/zUh2bS6alHrXwBQFeYAIAO6AoVUBgynA7oChVQGDKcDugKFVAYMpwO6AoVUBgynA7oChVQGDKcDugKFVAYMpwO6AoVUBgynA7oChVQGDKcDugKFVAYMpyuEPpvQFCzxSYBHpkAAAAASUVORK5CYII=)

   ```less
   negation(#ff6600, #999999);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAA0FJREFUeF7tmDGPaVEQx2cJhZWISkKhsaKRkKhEJCqF2ofzGdQK1SabpVJINIJGgag0NCTey5y8uRlnr2bdlznJmVtZOXtnzv83/zlzvA0Ggz+gj1cKvCl0r3ibzSp0/5grdA+ZK3SF7qMCHu5Zz3SF7qECHm5Zna7QPVTAwy2r0xW6hwp4uGV1ukL3UAEPt6xOV+geKuDhltXpCt1DBTzcspNO7/f7kM1mAxzL5RK+vr4e8PR6PSgUCsF3u90ORqPRw5pGowG1Wg1isZj5/nK5wOfnJ+z3+2Ddx8cHtFotSCQS5rvb7Qbf39+wXq8f3hVVTi7UmFPQ8/k8dDodeH9//6ENh2oDoMWn0wmGw6H5s91uQ6VS+fEeDtUuClp8v99hPp/DbDaDKHNyATjm4BR0AsVFJ0cTrEwmE7iXOoD9f+hkKh4qBO5oKiAqHuoAKIj9f1HlhAXkyuMUdALM2zDBisfjxn2pVMo4mDuWuxEL4XA4BC2bHw30fiyEyWQSAOZdhCBTDvV63Rwjr+ZkH0+SBeAkdO503qYRID7Utgkob9MIcLVaBdDJ6bwwEOBisYBqtWqOEg6U3E9FVS6XDfRXc7LnDYX+TwF7qLKFQcibzebpuY/rybX2oMffRZBLpVLouc8HOvzMB71XcpIEzWM75XRMzB6uttutGaaovePZaBfH8XiEdDptXMvbOR/4EPT5fIZcLgd84OPFge7GeaBYLD64P8qcXADvHHRbFGrvz65SvFDwM03d9nt4ew+73tF6KhReGP8rJ6kCcAo6gsFzdjweGz24ozmEbrdrzmR0pX1W0z282WzC9Xo11y58yNH8bEYHJ5NJmE6nZo09P+DwFWVOUpDtuM5BD7un2y4Pu6dzmDZAvmnu8mf3dF5gz+7pv8lJoYcoECZw2K9oNvSw1h/244z9y14YdLv1R5mTQndFAQ/zcKq9e6i/yJYVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskEVuqz+ItEVuojsskH/AtIUAdSlv+QBAAAAAElFTkSuQmCC) ![66ff99](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAvxJREFUeF7tmT1rVEEUhp9olKjgR2ksLNSQRlEUURRBLEQEOwsbwcbef5B/YGVhJdgo2gkWqQQxomIhViHaKBibgIIIgmJkLpzL2XFmyccWo+e91e7cuzNz3ue8c2bujl1fvr2MrlAKjAl6KN5dsIIej7mgB2Qu6IIeUYGAMaumC3pABQKGLKcLekAFAoYspwt6QAUChiynC3pABQKGLKcLekAFAoYspwv6/63ADS4yzZ4uyK985w5PmGeRWvv/qsY/4fSrnOEU0z2DH/zkHs94wbu+7QQHuMJpJtjUt80xz12edt/zPgz6cfYP9G3tO9k20F9pzNTvDJfZza7imK0mTfPQc1GTkDmAHKiJ7aFbP97hHppvv8QxznOYcTYMcPvFb2Z5wyNeM80k1zhLSo78mucTN3ncKvO2/0/3MD1Ar6Z3+Ge+MMPDv8T2gPwztfY8QVKHBth+b3PziWBlorYqtJIFzTrdAxnmHBM6d7AJXHPtEt86l+Zu/sgS29nS3fPjGmQb5wJHuv2BH9cScJyN/YrQCmg/j2ahewFfssBR9vX1urRsJ0A72NrXV3PkeqB7oOZ+c/FJpjro3ukrWZlaSIJmoddg+Xr9ivfVupqeM/CrXd5rewS/n0if842jB1orR4I+RAEP3ZbZHN595nropSOYuTLdy2tyGrqWDOmeP8alfhZY5BB7B5bzPDHf8oEpJtHyvsbUrtVHW2bNxfa9VH/XAz2fdj5uKSxbIbSRWyP0kgv9Tt0gl3bMK9l9D3N6cvAEm3nA8272pVqd5neOg9xitntmJaeINUox8p81W9NzsX3k3kmllzK+7qeXM6ut6bX9RO24V5vbyGmNqMOmoacYcwClo1n+osTvqIc5upYMJej5sbH0cqZ2bBwRq5F10zz0kUWqjnoFBD1gMgi6oAdUIGDIcrqgB1QgYMhyuqAHVCBgyHK6oAdUIGDIcrqgB1QgYMhyuqAHVCBgyHK6oAdUIGDIcrqgB1QgYMhyuqAHVCBgyHJ6QOh/AKacDbsLC6M0AAAAAElFTkSuQmCC)

   ```less
   negation(#ff6600, #cccccc);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAiNJREFUeF7tl72qwkAQhcfK0tbOTuxs9RFsfQUt7K0ErQyksrfQJxDS+ghGsLETO9/AlKm87MKEcREu9wZh4ZxUrvnZnPPt2Zk0LpfLS3hAOdAgdCjeXiyh4zEndEDmhE7oiA4AamZNJ3RABwAlM+mEDugAoGQmndABHQCUzKQTOqADgJKZdEIHdABQMpNO6IAOAEpm0gkd0AFAyUw6ocfpQFmWstlsJMuy6gXX67WMRiM/fjweslgs5H6/+3G325U0TaXT6fjx9XqVyWRS3TscDiVJEmm1Wv6/3W4n2+22Oj+bzWQ6nfpx3bljdDT6pIdA1USFfjweZbVavXlroYdA3YUK3f1eLpdyOp3e7lfodeeOEbh7p6ih25TZdBZFIbfbTdrtdpVwm06X+GazKc/ns0q43RnO57P0ej05HA4+4XaRuDnzPJfBYFDtLv+ZW3eZGMFHDd3B1STu93vp9/tvHuq2HW7XepHuAuPxWObzuV8IetgFZReEnq87d4yw9Z0IPcuE0CNaotzevwMj6qR/6rzDRu5To6Y12tX8sOu3jZyr+bbr12drfxB2/X+ZmzW95oL91EXbGh928L99ktkab2t3CNWN685dU/pXbo8+6V9RDf5QQgdcAIRO6IAOAEpm0gkd0AFAyUw6oQM6ACiZSSd0QAcAJTPphA7oAKBkJp3QAR0AlMykEzqgA4CSmXRCB3QAUDKTTuiADgBKZtIBof8A82M1p9hgjn4AAAAASUVORK5CYII=) ![33cccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAtRJREFUeF7tmD9LW2EUxk+Qi1KIi2kNgSgp4uJY2qmfoEuHgtBJwUUddelSSimUTo5t167duvQTdGp1dDCIYoUg/lm8GtQQUt5bzuXw5o1Gq/CW58mS5HLvuXnO7zznnJvCo9XVjvAFlYECoUPxzsQSOh5zQgdkTuiEjpgBQM2c6YQOmAFAyXQ6oQNmAFAynU7ogBkAlEynEzpgBgAl0+mEDpgBQMl0OqEDZgBQctROn69UZKZclqRQyNH8TFNZrNfz789GRuRVtSr3BgbyY1tnZzK9vg6Isz/JUUP/ODkpT4rFLiUW/OvxcXleKnWdQ/C9CyBq6CsTE1JvNuVzo5Ep+Do1JQ+HhmS/1ZI329vyK03FQXevdzs72bsWSrPdlg+7u/L96Ki/8gc6K2rolsPjYlHe1mryIEnEb/H2PC0M3+n2ej3fxvFHSavTkS97e3nB+R3FLyq9r8aOudNEDT0EKpRMP+G2EzgIod3AHVfooTFiofvx3bUK3X32dwp3jNBv2DpD0H0H2rZvb/Pt8DBr+TaGdadbAJ8OD8vv8/N8WbSglqtVOW235X6S5DuD7QzvazX5cXwss+VyNnLs73L3fDk6KkubmzdUfreXRe10X7o67rJ5ra5VCA6qOlELwcbVtt0rpsbzu4eL0e/IuVuE14/+X0HXNu1k2nlrZdtHOAd57eSE0L26iBa6g/eiVJK5jY38J/tOP7i4kIVKRT41Gtkmb7d3dfpamuYLINv731RGDT20INkFLDTzQ9tzr2d5ndGhRS1UNNYwWkBjg4NdfyBxkbv+yOlydmhB02OhzTv0SBfa4O2M9+Nc9UhmZ3zoX8HLHiv/ISW3cmm0Tr8VdQwSzAChAxYGoRM6YAYAJdPphA6YAUDJdDqhA2YAUDKdTuiAGQCUTKcTOmAGACXT6YQOmAFAyXQ6oQNmAFAynU7ogBkAlEynEzpgBgAl0+mA0P8A1mg7xaoa0fIAAAAASUVORK5CYII=)

   ```less
   negation(#ff6600, #ffffff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAeNJREFUeF7tlyGuwkAURR8SzSKQ3QCeJSCKJ0gWAAIUQSAJBgVNWAILQGCaIFkEFmR/mKSkn5+S9heaTO4Z1bxMB+49776BRpIkibGkHGgAXYq3Ewt0PeZAF2QOdKArOiComTsd6IIOCEom6UAXdEBQMkkHuqADgpJJOtAFHRCUTNKBLuiAoGSSDnS/HJjNZjaZTNyXnk6nNh6P3XNefbfbWb/fd3sGg4Etl0trNptWtn65XKzX69n5fLYgCGy/31u73fbGPG+TngWVhZ5XPx6P1ul0nmBS6HEcl6rfbjcLw9AOh4M7C+g19fr9frfRaGTr9fpXwvPq2fRnE/6feto8PsJO8XiZ9Ov1+kzbdrt1z4+VVy/bJO+aJ50k3W7XXQutVqumVv/cx3gH/XVMp1asVisbDod/nImiyObzubt/s2uxWNijYYrWN5uNnU4nN12y63VyfA7N904CesFmAPr3mrDQyYz3QjblbvIu6e/ubu70Ys0A9JL/AvghV6yxPr6L8V7NUpJO0qt1UF1vk/RqTnuZ9GqSeRvogj0AdKALOiAomaQDXdABQckkHeiCDghKJulAF3RAUDJJB7qgA4KSSTrQBR0QlEzSgS7ogKBkkg50QQcEJZN0oAs6ICiZpAtC/wFxi+aJxG2t+gAAAABJRU5ErkJggg==) ![0099ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu9JREFUeF7tmDtoVkEQhU8UYyEKVmJSBAtFlICCjSA+op1oI5ZqoZ1NrAQLCwvRyiqFoIUKacRKLARfiFgJBiQiKohF0gqKTQQj4zLcuctuIOG/4cI5f5dldu/O+ebM7mYItxcXoR+VAkOCTsX7f7KCzsdc0AmZC7qgMypAmLPOdEEnVIAwZTld0AkVIExZThd0QgUIU5bTBZ1QAcKU5XRBJ1SAMGU5XdAJFVhByme2A1MHgI3r0uS7n4ALr4Ha+Ao+0emU3jv9zkHg/M5Gg7nfwNmXwIv5ZiwXe+EvcHMGuPquHvPrD3DxDfDgS1vf2dPArs3NmAP1kYkR4P4RYHRDO2b6a3nciqFvv15Dz4G7eBF8DtxjIvhr+4DLe4DhNW35Y0wJpkc/nwOOPUl/xbViQdTG+wbc9tNb6BHmxx/A7odALAIX3J3pzrWkvPX6PI/xYrEYd2u+diyEZ8eBo6NA7Aq+h7yb1MYFfRkKuHNsirfq6EZz3/X3DbzoRodlkG99AC6Np3YcYxySF8KVvQlwqYusX5v2cGpbu/Xb3qwgfi60272Pl46PZUjQWWhvne5Q8rPXXWsOvTHTuDq22jh3ajZdsAx6BJp3iHM7EvTo4Lyz7N8i6J1Voi1caq02HqE/+tac1TXo5rZDW9uXwbjx0rFQSszXV3vvEPugnO4t1ovIW++reeDEWNv9+YXv8Xfg8Ajg7d1eA4K+CtBrN2w7n+99btp77UzPn3e+5dgx7JJY+pUKT9A7hL7U7T0WwlK3dy8Ec/CmYWDybdpw6RVgl8TJceDk0xRT+n6cq9t7R/BjS46f8GeWjdXe4PECWIuJ69Te6flFUk7vCHZcNgcfQXlcDjUHVYIejwNbpwS99N8/QV8F6PrE4BXo7Tt98KlqRVdA0AlrQdAFnVABwpTldEEnVIAwZTld0AkVIExZThd0QgUIU5bTBZ1QAcKU5XRBJ1SAMGU5XdAJFSBMWU4XdEIFCFOW0wWdUAHClP8BudM3z2qKlQkAAAAASUVORK5CYII=)

   ```less
   negation(#ff6600, #ff0000);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuVJREFUeF7tmL8uREEUxj+Nbp/AG/AG2xA0ap0EBZ1KpVMohEoh21Eg0XkDxBPoeAMvQKchx+Rkzk7m3hBOnOx8t9vZ2XPPfL/zb3bqA/gAn6YUmCL0pnh/HZbQ22NO6A0yJ3RCb1GBBs/Mnk7oDSrQ4JGZ6YTeoAINHpmZTugNKtDgkZnphN6gAg0emZlO6A0q0OCRmemEHlCB9XVgNAIGg+Tc+TmwvQ10rQc8QjSXYmf64iJweQnMzGTdBPr1dX1dguHsDNjayvtfXoCNDeD+Pq+VAfP+DhwfA/v7eU/Xu+Ud9rm9BZaW8srzMzA3F43zmD+xoR8cAHt7wPR0znBxv2u9BK5HteBL4LrHgq8B131aaeRzCVz3BAcfG7pCLDOxtm5hqug2CBTW0xMwOwu8vQE7OwmTtg/9ncLU9z485MqiAbSwkAPy7g5YXs5BUKscgXI/LnSFY8USUK+v4+Vevpd1Kd8rK2m3lmqbsQLm8DDDU1A2YxXo6WkKDJuxGkAaLPPzqY3o56ur8TnDVoRAwMWVyYH++AgICAtBTqjBIwCPjnJWWygWqGS9VA2ZI2xgaEvRoBoOUy+3raMMMsn+gE9c6CLWT8q7luQ+6Dc39RnBQpchcW0t3Rb6oK+upmpA6H8c1j+BXpZfKbfM9CqQycn0WoCU5fbiIpf3rp5+cgLs7qby3tfTNzdTeWdP/8dM75ve7TTdN713TeF2etdAsNfG8ndli/ljWX5rbnIy/bv3ZgurvBnIFa6cwu2e8ipWu2HIfltFfkvI4feTBb0GvvZHSQm+lpnf+dfOzgwKJzjw2Fc2hwinyaRA7EwnJRcFCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGCT02HxfvCN1F1thGPwG8UiXtkL3IWQAAAABJRU5ErkJggg==) ![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuFJREFUeF7tmL1LXEEUxa8QJI0kVWCxDIJoHxLTqZBUqRPBlaCkEYtUWonYWaUQOyWwFkmdLqB2gfwDimBjowsBQbEJEkiYB3e4e533UDKwA+e87s2+2bn3/O6ZrwH5IH+FD5QCA4QOxbtKltDxmBM6IHNCJ3REBQBz5ppO6IAKAKZMpxM6oAKAKdPphA6oAGDKdDqhAyoAmDKdTuiACgCmTKcTOqACgCkX6fS9j3syNToVcRx1j2R8bbwHz3Z7W+Zfzse2s8szaX9uy8HxQc93s89nZevdlgw9HIrtOz92ZKGzEN8nRyel874jw4+HY9v+8b5Mf5qO7zlj6nedFQfdi6sCWfAeuH7jwdd9Z6Gvv1mX5VfLMvhgsIeFhZ4zpn4DD+MXBd0CUNFV8Js/N7LxfUNOfp1E52ohWLgK1Do8NVOE5K3D62aKnDGVALw46Arv+ve1LH5ZlN2fu2LhBaDnl+eVM8MTimD122oPPF8sdTBD/9R4HkzOmAg9oYC62oKybgxATy9Oq7XcFkb4q8O1QxlrjYm6Wt9Dn9ajVvVbeKzrdbzQ1r3qxn2EHT9nTISeUEBBNUEP3cImrwn60telWxszO5wvjBQMjWHz7WZVMP8bk9+I9rMAilrTc7nKQk+5Vgtm5fVKBdQWkE7nuoeYeDpRFVmO2aefoO3YRUJvWtND8GF6Vyh1a7qd3vXo5dfnuRdzFVA75evGTfcMCj1HTISeUKBpp6yih2567va7d1sIOmtYWH75mHk2c6uAfL+RJyPxSOc3ifeNidBrFFAw/uf7nptTlzL6n3qsS13K6Dd2vFwxEXqDAl5kfzsWut7lhsxDtTOBHd6P52/s7OkgVRTadpeYSgBf1JpegiAIMRA6AmWXI6ETOqACgCnT6YQOqABgynQ6oQMqAJgynU7ogAoApkynEzqgAoAp0+mEDqgAYMp0OqEDKgCYMp1O6IAKAKZMpxM6oAKAKdPpgND/AZxnARqhLNd2AAAAAElFTkSuQmCC)

   ```less
   negation(#ff6600, #00ff00);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAu1JREFUeF7tmLEuRUEQhn+NTi3iDXgDDUGjvoVEgoLOA+gUCuEBdBSX2hsgnkBHq/IEOg2ZTWYz1jnXnBDZzP6ns2b33vm/f2bn3Cl84AN8mlJgitCb4p2SJfT2mBN6g8wJndBbVKDBnHmnE3qDCjSYMiud0BtUoMGUWemE3qACDabMSif0BhVoMGVWOqE3qECDKbPSCT22Are4xRrWUpKveMUOdnCPe/StR1Wj+kq/wAX2sJf1t7B0cRvbOMc5ZjCTlt7xjjOc4QhHeV/fOVvY6jxfNl7hCvOYz2dc4hL72P/iBWsY+ccznrGIxar9UjX0EpQqacGXwDWmBP+EJyxg4UuFS2zX+ipWvwHXcy34ErjG1A6+WugWpopoTaDiK7Q3vOEAB0l3rXrdZyFaIH3rClON84CHbAI13ApWcIhDTGMad7jDOtbzNdHVaWoq/WqhH+M4iSqPtmoLSYQ+wUmGocJLvEITQHJnb2IzwbHPC15S6y7X5Zw5zKWuYA2ihlNzLWM5XQv69zWuYY3adRXUAr5a6KXIIqptxwLkFKe5qq3Idq9A38CGG/ojHjGL2WQIa6TShEtYSkOhvWpKU0r11/hUC12r1VZSCf0GN7nF9kGXli9gdCj7qb3bap0EfYTRtxmB0H9p8b+q9KHQ+8Cx0n8J1LNdoduhqAQyxji39747Xd7F7evXT5XeN9yVJtzFbmrvvNM9NJ0xk6Z3a4RJ07sa4S+ndzWNVn7X9F5eSc6U/y2s2jvdTuGlGrZarfg2zgo/FPrQd//y+9mu828kB3xQ1dC7wHf98FGCLyttKHT5XM+vfHawVM1rBy7fs3roAwzMUKcChO4UKlIYoUei6cyF0J1CRQoj9Eg0nbkQulOoSGGEHommMxdCdwoVKYzQI9F05kLoTqEihRF6JJrOXAjdKVSkMEKPRNOZC6E7hYoURuiRaDpzIXSnUJHCCD0STWcuhO4UKlIYoUei6cyF0J1CRQoj9Eg0nbl8AonbDe1bWpYlAAAAAElFTkSuQmCC) ![ff9900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAyFJREFUeF7tmDtoVUEQhv8I2qiFlZgUYhERg2BhFcQ3pLAUS7XQLlhY2VlYCFamsRC0UMHGTrAzvlArUSEoojYWiQiC4KOJYGRchjN3757LPRKHIfvf6rLs7sz8387s7BlZuoIl8FeVAiOEXhXvv8ESen3MCb1C5oRO6DUqUGHMvNMJvUIFKgyZmU7oFSpQYcjMdEKvUIEKQ2amE3qFClQYMjOd0CtUoMKQmemEHlCB8WPA7svA6vXJubfXgMengLbxgCFEcyl2po8eAPbfANaONboJ9A+3yuNyGA7fA8YONvPnZ4G7h3p133Ue2HkWWLUmjf+cBx4cBxbuN/PabIsN+8vtfX0D3J6IxrnHn9jQLRzNcHG/bfzoa2DD9n7BLYg9V4FtJ/vn/PoOPJkG3t8ESsB1hfUjB65zgoOPDV0B/V4EXl0Enp9LspbGSwchn7fwsKkQCsZeE1oVFKbateu0Kozua6pF2zr1N1jex4VeylrJxsVvveVeBJXxLy+ATXvT/1LGSoZ+etT0B6WM1YOgtksVQvcXW1IxrD17gOz+hD6kAv8K3TZ7NvslG99db6ArUFvKJYvnLgE7zqSDZfsB3Uv2l6qzcTL1DrYfsHuVeokhQ//f0+JmelsZbxsfdA/LmrwEl5QVgHKnT0yn18Ig6FuOpP6B0Jf5jHa508V0/oz7/AxYtzllrS23tooItB8fU+ZK9j893dz7zPRlBjrMdl2h53vmJbnUWOUl+eWF/mbPVhe9w7eeSOWdd/owJDvM6Qp96g4wN5Pe2/ldre/wyZnUDOoByDt1GR/UvWsvkPcL8i1A19mD0CFcr6kr504XxUrNX9tzL1fYlvL8mtC5+V5t3wUCN3ESysqGXsq40seZ0vMqB58D14OQgw8OPD50r3pXmZ3YmV4ZDK9wCd1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2CD0QDC9XCN1L6UB2/gDMLCvPFhHnsAAAAABJRU5ErkJggg==)

   ```less
   negation(#ff6600, #0000ff);
   ```

   ![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAxBJREFUeF7tmD9oVEEQxr9AUBvRNlgKIWinIP4pFBXExkYQFBIRxUYsrAQLCVZaWaVThATU2kpBJY1g0keCvdiKNkGEyPgYdm5v9/DIGxhuv6vu3u2bN/v95t++qe3b2AY/TSkwRehN8f63WUJvjzmhN8ic0Am9RQUa3DN7OqE3qECDW2amE3qDCjS4ZWY6oTeoQINbZqYTeoMKNLhlZjqhN6hAg1tmphN6QAWOzwNXl4A9ezvnPj0Hlm8BtesBtxDNpdiZPncWuLEM7D+QdBPo6y/L1yUY5LPwDDh1M92z9Qt4dQf4vJKu5UFjA0q+156tz1BL994Dc+eS3e9fgMXD0TgP+BMb+qVHwIX7wPSulOHifu26/Le4AcwcGhQ9h54Hha7WKlICnq+R3zlwXRMcfGzoCufPb+DdE+DNw8FMrl3Ps9aGgM3wGhyFqfa/rqbK8uMb8GIBmD2TAnLzA/D0fAqC3K9geR8Xei1jt34OlnsRVDJ5danr89IKFEJJbAWq8DY/Dq/SZ9ug0ADUqjF7umshtorYgNKqEQy4uDM50KXPH7sGTO8G1laAo1eGhz9b/iUw9s2kVqCAbWm3waMtRWxI1Tl4suvlNnhq9wYDHxe6Hcj+p7zbPl8SuTYA2rUC/u3jdFoYBf3I5S5gCL3nkB6np1voCstmngB9fXe4N0t515I/qk0w03uGWzM3DnTtp1Le7dCX92f9bbPY9ms7G4zq6Seud+WdPb3nYBgHep7Vcla2g1U+YVtYGghaqi8+6ICWpncNhFJlsRUjfy/QszQ7MTc5Pd3OALkitWzM19Xe9um6fLYonTBk7ajTw05o9XTvZEEXUfKBrnQ0y1++lM7V+Ru72tk7Bx8ceOwjW09RTTPDCsTOdBJzUYDQXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbZTQY/Nx8Y7QXWSNbfQvHQ4X2X8kDHsAAAAASUVORK5CYII=) ![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAuJJREFUeF7tmD8uRVEQxj+NzgLEDlgBDcEOdBIUdCqVTqEQKoXoKJDo7ACxCXZgA3QaMm4mZ0zevYbkvtzM+W7nmHPum+83/86dAD4/wacqBSYIvSre384Sen3MCb1C5oRO6DUqUKHP7OmEXqECFbrMTCf0ChWo0GVmOqFXqECFLjPTCb1CBSp0mZlO6BUqUKHLzHRCr1CBf7i8sQGcnwNTU83my0tgZwdoW//HK3rdMvhMv7gAtreLBq+vwOYm8PhY1rzYHx/AyQlwcFBslpeB62tgZqasKSyr8P09sLJSVl5egLm538+5vY2d3yvN4OGDhu6Bq08WvAeuNhb8KOBqZ8F74GpjwR8eAvv7wORkyXCxa1sPchir2WChW5gqug0ChfX8DMzOAu/vwO5uo52WXt2nMDUQnp5KVmoALS0VmA8PwOoq4PdJ5dDf4KtJ2/pYaQZfNljomjnih5Zqm7EC5uiowFNQYq+wFOjZWRMYNmMVkgbL4mLTRvTvm5ufPVqCbH6+Occ+Yv/29rNtyP/tOUEWYzMbLHQPRSDIo5ktAI+PS1bbMm33StZL1ZBebgPDB9XCQtPLbevwQTY9Tei9RqZmq88YC/3ubnR/tdBlwFpfbybtLuhraw3QLuhS8lnee8Q+xEwn9B6By9GjMsqX26urUt7bevrpKbC315T3rp6+tdWU966eLndxZnqP4Lumdzs5d03vbVO4nd41EOyVy++zgUDoPUK3U7h/Tdu92U/VcoXzU7i18dcuDSD/PltFCL1n6KPA+y9k/sNI23Up8tXO3g7UNQu8re10rY9Boj+/YrBXtj97wg1hBQg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9IfSwVHkMCT0Py7AnhB6WKo8hoedhGfaE0MNS5TEk9Dwsw54QeliqPIaEnodl2BNCD0uVx5DQ87AMe0LoYanyGBJ6HpZhTwg9LFUeQ0LPwzLsCaGHpcpjSOh5WIY9+QK9YCXtTUTAjwAAAABJRU5ErkJggg==) ![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAH0AAAAyCAYAAABxjtScAAAAAXNSR0IArs4c6QAAAlpJREFUeF7tmDGKFUEURc+AgcGAgpGpweAKBsUFaGRuIphMPjtwBy5hwMR8MhcguARjE8FIwcBA+FJSDx5F1Ydyapjpf29H/burX/13T93Xr/pod7bb4UNKgSNDl+L9L1lD12Nu6ILMDd3QFRUQzNnvdEMXVEAwZTvd0AUVEEzZTjd0QQUEU7bTDV1QAcGU7XRDF1RAMGU73dBvuQJPgFfA3fo/PwHv6/lb4GE9/waU3yuPl8Bz4A7wB/gIXAKj6yvnXhxrO05/DLwB7icFAvo5UO7HkaG3C6WMyYul/O7F/gK8qwHbGAH9e7MI82JYDGpluO1Az47K0DKw1uGvgWcdufLzOW4emqFHnBbq6PpKQtcQazvQRwJnF47cOSr3ecH8AC6AEqM9opL8Bj4An+uA0fVrALUy5Dag5/d1ZF8AfAVOBk5+UMv2PpixkFqYEbJX9su9X3XAcTP3vrlWUrtirMOF/qg2dsW59wZNXji1VIKfqS8IeEXcto8w9CsuuZnHZ8r7yKFto9erIDEmu9blfYbUwrH/C30fvBe1AuTy3pvH0BeCnAk1A73EDRf3uvCA/LSW9NzoRTdfYsRe3NBnSC0cOwu9ByoWQrj/tG7p8las95yhLwQ5E2oWeu+jTMwX+/R97/5cIQx9htTCsbPQy9Qt1NEXs7aha7/YGfpCkA51IwpsY59+I9Ic7qSGfrhsh5kZuqELKiCYsp1u6IIKCKZspxu6oAKCKdvphi6ogGDKdrqhCyogmLKdbuiCCgimbKcbuqACginb6YYuqIBgyna6oQsqIJjyX6qtaJjEcizLAAAAAElFTkSuQmCC)







​	
