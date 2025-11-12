
# 第一章：`JavaScript` 语法基础

## 1. 前期准备
### 1. 技术准备
1. `Python`基础语法
2. 爬虫基础功底
3. `JavaScript`基础语法知识

### 2. 工具准备
`nvm`全名：`node.js version management`，一个`node.js`的版本管理工具，通过它可以安装和切换不同版本的`nodejs`。类似于`Python`中的`conda/miniconda`。
下载地址：`https://github.com/coreybutler/nvm-windows/releases`

`nvm`基础指令：
- `nvm list`：列出所有已安装的`node`
- `nvm install 16`：安装指定版本的`node`，`16`指的是`node`版本
- `nvm use 16`：切换到指定的`node`版本，`16`指的是`node`版本，若需要切换到其他版本则修改 ==版本号==

> 在`js`逆向中建议使用`node.js 16`。

安装并配置成功之后可直接使用`Pycharm`执行`JavaScript`脚本，`Pycharm`会自动识别已安装的`node`环境。

##  2. 逆向简介

### 1. `JavaScript` 逆向
- `JavaScript`加密：通常指的是在`JavaScript`中使用各种加密技术和算法来保护数据的安全性或者对敏感信息进行加密处理的过程。
- `JavaScript`逆向：工程是指对`JavaScript`代码进行反向工程的过程。在软件开发中，逆向工程通常是指对已有的软件、程序或代码进行分析和理解，以获取它们的设计、功能和工作原理的过程。
- `JavaScript`逆向并不是把网站的加密信息还原成文明数据,而是根据网站上的加密(解密)的代码,我们通过`python`代码模拟出来。逆向并不是通过密文能反推出明文（算法是没有办法能反推出来的）

### 2. 存在机密的原因
1. **数据传输安全：** 在`Web`应用程序中，客户端和服务器之间的通信可能涉及敏感数据，如用户身份验证信息、支付信息等。通过使用加密技术，可以确保这些数据在传输过程中不被窃听或篡改。
2. **客户端数据保护：** 在客户端，有时需要对一些敏感信息进行本地存储，例如用户凭据、令牌等。通过使用前端加密技术，可以增加这些本地存储的安全性。
3. **密码安全：** 当涉及用户密码时，最佳实践是在服务器上存储密码的哈希值而不是明文密码。然而，有时需要在客户端进行一些密码处理，比如在注册或登录时进行密码哈希。使用前端加密库可以确保这些操作在客户端的安全性。
4. **数字签名和身份验证：** 在一些场景中，需要确保数据的完整性和来源。
5. **加密算法研究和实验：** 有时，开发人员可能对加密算法进行研究或实验，以了解它们的工作原理、性能和适用性。

### 3. 加密方式

> 请求头加密

请求头常见的是：`UA`、`host`、`Origin`、`Referer`，这些请求头我们很简单就能分辨出来。但是除了这些数据以外，网站会自己带上一些其他的参数，跟平时的数据可以明显看得出来不一样。这些就是请求头加密

> 请求参数加密

网址：`https://www.qcc.com/web/search?key=%E5%B0%8F%E7%B1%B3&p=2`

> `cookie`验证

网址：`https://q.10jqka.com.cn/`

> 响应数据验证

网址：`https://www.swguancha.com/home/ranking-list?code=td&tabName=city`

> 全加密

网址：`http://www.birdreport.cn/home/search/report.html?search=eyJ0YXhvbmlkIjoiIiwic3RhcnRUaW1lIjoiIiwiZW5kVGltZSI6IiIsInByb3ZpbmNlIjoi6Z2S5rW355yBIiwiY2l0eSI6IiIsImRpc3RyaWN0IjoiIiwicG9pbnRuYW1lIjoiIiwidXNlcm5hbWUiOiIiLCJzZXJpYWxfaWQiOiIiLCJjdGltZSI6IiIsInRheG9ubmFtZSI6IiIsInN0YXRlIjoiIiwibW9kZSI6IjAiLCJvdXRzaWRlX3R5cGUiOjB9`

## 3. 变量与数据类型

`JavaScript`是一种弱类型语言，也就是说不需要指定变量的类型。`JavaScript`的变量由它的值来决定。定义变量需要使用关键字`var`，一段`JavaScript`语句应该以`;`（分号）结尾。

### 1. 定义变量的语法格式

```JavaScript
/* 变量 */
var number = 123;
var str_data = "hello";

/* 连续声明多个变量, 使用逗号隔开并共用一个var关键字 */
var a = 1, b = 2, c = 3;
```

```JavaScript
// 单行注释

/*
  多行注释
*/
```

### 2. 数据类型

`JavaScript`中有六种数据类型，包括五种基本数据类型和一种复杂数据类型（`object`）。

五种基本数据类型：
1. `number`数字类型
2. `string`字符串类型
3. `boolean`布尔类型（`true`或`false`）
4. `undefined`类型：变量声明未初始化，它的值就是`undefined`
5. `null`类型：表示空对象，如果定义的变量将来准备保存对象，可以将变量初始化为`null`，在页面上获取不到对象，返回的值就是`null`

`object`复合类型：是一种特殊的复杂数据类型，也被称之为引用类型。数组、函数和`JavaScript`对象都属于复合类型

```JavaScript
/* 数据类型(数据类型一共有六种: 数字, 字符串, 布尔值, null, undefined, 对象) */

// 数字
var int_data = 123;
var float_data = 123.456;

// 字符串
var text = "world";

// 布尔值
var bool_data_1 = true;
var bool_data_2 = false;

// null(表示一个空值或不存在的对象，它是一个明确的赋值，表示"没有值")
var null_data = null;

// undefined(表示变量已声明但未赋值或不存在的属性)
var undefined_data = undefined;

// 对象(复合类型, 可以包含多个值, 也称之为引用类型)
var obj_data = {
    "name": "hello",
    "age": 123
};

/* 类型输出 typeof类似python中的type函数 */
console.log(
    typeof int_data,
    typeof float_data,
    typeof text,
    typeof bool_data_1,
    typeof bool_data_2,
    typeof null_data,
    typeof undefined_data,
    typeof obj_data
);
```

### 3. 变量命名规范

1. 区分大小写
2. 第一个字符必须是字母、下划线（`_`）或者美元符号（`$`）
3. 其他字符可以是字母、下划线、美元符或数字

  
## 4. 函数的定义与使用

与`Python`一致，函数就是一段可以被重复利用的代码段，在`JavaScript`中使用`function`关键字声明函数。

### 1. 函数定义

```JavaScript
// 函数定义
function run_func() {
    console.log("hello world!");
}
```

### 2. 函数调用

```JavaScript
// 函数定义
function run_func() {
    console.log("hello world!");
}

// 函数调用
run_func();
```

### 3. 定义存在形式参数并有返回值的函数

> 1. 定义函数时，函数如果有参数，则参数可以放在小括号中。如果函数需要返回结果，则可以使用`return`返回
> 2. `JavaScript`函数可以不定义形式参数也能接收到调用时传递过来的值，可使用`arguments`完成接收，功能类似`python`中的`*args`

```JavaScript
/* 函数返回值 */
function add_number(a, b) {
    return a + b;
}

result = add_number(1, 2);
console.log(result);

/* 特殊的返回情况 */
function return_numbers() {
    return 1, 2, 3;  // 函数返回多个值, 实际上返回最后一个值
}

result = return_numbers();
console.log(result);

/* 向不接收参数的函数传递参数 */
function not_attr_func(number) {
    console.log(number);
    console.log(arguments);  // arguments是一个数组, 包含所有传递给函数的参数
}

not_attr_func(1, 2, 3);
```

### 4. 函数嵌套

```JavaScript
/* 函数嵌套 */
var inner_func;
!function () {
    function _inner_func() {
        console.log("这是一个内部函数");
    }

    inner_func = _inner_func;  // 将内部函数赋值给外部变量
}();
inner_func();
```

### 5. 自执行函数

```JavaScript
/* 自执行函数 */
!function () {
    console.log("这是一个自执行函数");
}();  // 注意后面的括号(代表函数调用)
```

  
## 5. 变量作用域

### 1. 作用域简介

变量作用域就是变量的使用范围，变量分为：

- 局部变量：在函数内使用的变量，只能在函数内部使用

```JavaScript
function my_func() {
  // 定义局部变量
  var num = 123;
  console.log(num);
}

// 调用函数在控制台输出
my_func()

// 在函数外直接使用函数内部变量会抛出异常
// console.log(num);
```

- 全局变量：在函数外定义的变量，可以在不同的函数中使用

```JavaScript
var num = 123;

function my_func() {
  // 修改全局变量
  num++;
  console.log(num);
}

my_func()
```

### 2. 变量的生命周期

- `JavaScript`变量生命周期在它声明时初始化
- 局部变量在函数执行完毕后销毁
- 全局变量在页面关闭后销毁
### 3. 声明变量类型

在`JavaScript`中，声明变量使用的关键字是`var`、`let`和`const`
- `var`：是旧版本的`JavaScript`中用来声明变量的关键字，它可以在任何作用域内声明变量，包括函数内部和块级作用域外
- `let`：是`ES6`引入的新关键字，用来声明块级作用域变量，即变量的作用域被限定在声明它的代码块中
- `const`：也是`ES6`引入的新关键字，用来声明块级作用域的常量，即一旦声明，其值就不能改变

## 6. 条件语句

> 条件语句就是通过条件返回的结果来控制程序的走向。

### 1. 语法

1. `if`语句：只有当指定条件为`true`时，使用该语句来执行代码
2. `if...else`语句：当条件为`true`时执行代码，当条件为`false`时执行其他代码
3. `if...else if....else`语句：使用该语句来判断多条件，执行条件成立的语句

### 2. 比较运算符

| 运算符   | 描述        | 案例                                                             |
| ----- | --------- | -------------------------------------------------------------- |
| `==`  | 等于        | `x == 8`                                                       |
| `===` | 全等于（值与类型） | `var x = 5`<br><br>`x === 5`为`True`<br><br>`x === '5'`为`False` |
| `!=`  | 不等于       | `x != y`                                                       |
| `>`   | 大于        | `x > y`                                                        |
| `<`   | 小于        | `x < y`                                                        |
| `>=`  | 大于等于      | `x >= y`                                                       |
| `<=`  | 小于等于      | `x <= y`                                                       |

> 示例代码

```JavaScript
var num_1 = 1;
var num_2 = '1';

// == 默认将数据类型转换为相同类型再比较
if (num_1 == num_2){
    console.log("相等");
} else {
    console.log("不相等");
}


// === 不进行数据类型转换, 直接比较
if (num_1 === num_2){
    console.log("相等");
} else {
    console.log("不相等");
}


// 多条件判断
var age = 25;
var hasLicense = true;
var hasCar = false;

if (age >= 18 && hasLicense) {
    if (hasCar) {
        console.log("你可以合法驾驶自己的车。");
    } else {
        console.log("你可以合法驾驶，但你需要一辆车。");
    }
} else if (age >= 18 && !hasLicense) {
    console.log("你需要先考取驾照才能驾驶。");
} else {
    console.log("你还未成年，不能驾驶。");
}
```

### 2. 三目运算

```JavaScript
/* 三目运算 */
var num_3 = 1;
var num_4 = 2;
var max_num = num_3 > num_4 ? num_3 : num_4;  // 如果num_3大于num_4, 则max_num为num_3, 否则为num_4
console.log(max_num);
```

> 比较恶心的三目运算

```JavaScript
var num_3 = 1;
var num_4 = 2;

var result = num_3 < num_4 ? 1 < num_4 ? 3 < num_3 ? 'a' : 'b' : 'c' : 'd';  // 嵌套三目运算
console.log(result);

/*
    1.先判断 num_3 < num_4 （1 < 2），结果为真
    2.继续判断 1 < num_4 （1 < 2），结果为真
    3.再判断 3 < num_3 （3 < 1），结果为假
    4.因此返回值 'b'
*/
```

注意：三目运算与`if...else`的区别是三目运算有返回值。

### 3. 逻辑运算符

假如`x=6; y=3`查看比较后的结果：

| **比较运算符** | **描述** | **案例**                      |
| --------- | ------ | --------------------------- |
| `&&`      | `and`  | `(x < 10 && y > 1)`为 `true` |
| `\|`      | `or`   | `(x==5 \| y==5)`为`false`    |
| `!`       | `not`  | `!(x==y)`为`true`            |

> 示例代码

```JavaScript
/* 逻辑运算 */
// &&(逻辑与) ||(逻辑或) !(逻辑非)
var x = 1;
var y = 2;

result = x > y && x < y;  // 逻辑与, 只有两个条件都为真时, 结果为真
console.log(result);
result = x > y || x < y;  // 逻辑或, 只有两个条件都为假时, 结果为假
console.log(result);
result = !(x > y);  // 逻辑非, 取反
console.log(result);
```

### 4. 控制流（`switch` 语句）

在`JavaScript`中，`switch-case`语句是一种用于替代多个嵌套`if-else`结构的简洁语法。

```JavaScript
/* 控制流(switch) */
var day = new Date().getDay();  // 获取当前日期(返回值为0-6)

switch (day) {
    case 0:
        console.log("今天是星期日");
        break;
    case 1:
        console.log("今天是星期一");
        break;
    case 2:
        console.log("今天是星期二");
        break;
    case 3:
        console.log("今天是星期三");
        break;
    case 4:
        console.log("今天是星期四");
        break;
    case 5:
        console.log("今天是星期五");
        break;
    case 6:
        console.log("今天是星期六");
        break;
    default:
        console.log("今天是未知日期");
}
```

## 7. 数组

数组就是一组数据的集合，`JavaScript`中，数组里面的数据可以是不同类型的数据，好比`Python`中的列表。

### 1. 数组的定义

```JavaScript
/* 使用对象创建数组 */
var my_array = new Array(1, 2, 3, 4, '5');
console.log('实例化对象创建的数组: ', my_array);

/* 使用字面量创建数组（推荐使用） */
var my_array = [1, 2, 3, 4, '5'];
console.log('字面量创建的数组: ', my_array);

/*
 - 字面量是指在代码中直接给出的值, 比如数字、字符串、布尔值等
 - 字面量是一种特殊的表达式, 它的值就是字面量本身
 - 字面量可以直接使用, 不需要定义变量
 - 字面量的类型是确定的, 不能改变
*/
```

### 2. 多维数组

```JavaScript
var my_array = [[1, 2, 3], ['a', 'b', 'c']];
```

### 3. 数组操作

> 获取数组长度

```JavaScript
var my_array = [1, 2, 3, 4, '5'];
console.log(my_array.length)
```

> 根据下标取值

```JavaScript
var my_array = [1, 2, 3, 4, '5'];
console.log(my_array[1])
```

> 修改指定元素

```JavaScript
var my_array = [1, 2, 3, 4, '5'];
my_array[1] = 100;  // 修改数组中的第二个元素
console.log(my_array[1]);  
```

> 在数组末尾添加元素与删除元素

```JavaScript
var my_array = [1, 2, 3, 4, '5'];

my_array.push(6);  // 在数组末尾添加一个元素
console.log(my_array);

console.log('pop取出的数据为:', my_array.pop());  // 取出数组末尾的一个元素
console.log(my_array);
```

> 在数组开头添加元素与删除元素

```JavaScript
var my_array = [1, 2, 3, 4, '5'];

my_array.unshift('0');  // 在数组开头添加一个元素
console.log(my_array);
console.log('shift取出的数据为:', my_array.shift());  // 取出数组开头的一个元素
console.log(my_array);
```

> 指定下标添加或删除元素

```JavaScript
var my_array = [1, 2, 3, 4, '5'];

// splice(开始删除的索引, 删除的数据个数, 在删除的位置添加新的元素)
my_array.splice(1, 2, 'a', 'b', 'c');  // 从数组的第二个元素开始删除两个元素, 之后在第二个元素位置添加三个元素
console.log(my_array);
```

## 8. 循环语句

循环语句就是让一部分代码重复执行，`JavaScript`中常用的循环语句有：`for`、`while`。

### 1. `for` 循环

```JavaScript
// for
var my_array = [1, 2, 3, 4, 5];
for (var i = 0; i < my_array.length; i++){
    console.log(my_array[i]);
}
```

### 2. `while` 循环

```JavaScript
// while
var i = 0;
while (i < my_array.length){
    console.log(my_array[i]);
    i++;
}
```

## 9. 对象

现实生活中，万物皆对象。对象是一个具体的事物，看得见摸得着的实物。例如，一本书、一辆汽车、一个人可以是`对象`，一个数据库、一张网页、一个与远程服务器的连按也可以是`对象`。

在`JavaScript`中，对象是一组无序的相关属性和方法的集合，所有的事物都是对象，例如字符串、数值、数组、函数等。

对象是由属性和方法组成的：
- 属性：事物的特征，在对象中用属性来表示（常用名词）
- 方法：事物的行为，在对象中用方法来表示（常用动词）

### 1. 创建对象的三种方式

- 利用字面量创建对象
- 利用`new Object`创建对象
- 利用构造函数创建对象

> 利用字面量创建对象
> 对象字面量：就是花括号里面包含了表达这个具体事物（对象）的属性和方法

```JavaScript
/* 字面量创建对象 */
var obj_1 = {
    "name": "admin-1", "age": 12,
    "print_info": function () {
        console.log(this.name, this.age);  // this: 类似python中的self, 指向当前对象
    }
};

console.log(obj_1);
console.log(obj_1.name)
obj_1.print_info();
```

> 利用`new Object`创建对象

```JavaScript
/* new 创建对象 */
var obj_2 = Object();
obj_2.name = "admin-2";
obj_2.run_function = function () {
    console.log("obj_2的函数被执行...");
}
console.log(obj_2.name);
obj_2.run_function();
```

> 利用构造函数创建对象

```JavaScript
function person(name, age) {
    this.name = name;
    this.age = age;
    this.print_info = function () {
        // 反引号支持变量插值, 类似python中的F表达式
        console.log(`my name is ${this.name}, age is ${this.age}`)
    }
}

var obj_3 = new person("admin-3", 13);
console.log(obj_3.name);
obj_3.print_info();
```

前面两种创建对象的方式一次只能创建一个对象，里面很多的属性和方法是相同的。因此我们可以利用函数的方法重复使用这些相同的代码。我们就把这个函数称之为构造函数。

构造函数：是一种特殊的函数，主要用来初始化对象，即为对象成员变量赋初始值，它总与`new`运算符一起使用。我们可以把对象中一些公共的属性和方法抽取出来，然后封装到这个函数里面。

### 2. 类的使用

- 在生活中，类一些具有相似属性和行为的事物抽象出来的概念，比如：人类、球类、汽车类；
- 在`JavaScript`中，类是模板，是用于创建实例对象的模板；相当于实例的原型（`prototype`）

> 创建方式

```JavaScript
class 类名 {
  // 构造方法，用于声明实例属性
  constructor() {
    ...
    };
}
```

1. `class`：`ES6`中提供了`class`关键字，用来创建类
2. 类名：一般为名词，采用首字母大写表示，如`Person`、`Car`等等
3. `{...}`：类体放在一对大括号中， 我们可以在大括号内定义类的成员，比如构造函数、静态方法等等
4. `constructor(){...}`：每个类都会包含的一个类的构造函数，用来实例化一个由`class`创建的对象

> 完整的类的创建示例

```JavaScript
class Person {
    // 构造函数
    constructor(name, age) {
        this.name = name;
        this.age = age;
    };

    // 私有方法: 使用 # 定义私有方法或者私有属性
    #private_func() {
        console.log("这是一个私有方法");
    };

    // 静态方法
    static static_func() {
        console.log("这是一个静态方法");
    }

    // 实例方法: 无需使用 function 关键字
    print_info() {
        console.log(`my name is ${this.name}, age is ${this.age}`)
    };

    // 在内部调用私有方法
    run_private_function() {
        this.#private_func();
    }
}

// 实例化
var obj = new Person("admin-4", 14);
console.log(obj.name);
// obj.private_func();  私有方法与python一致, 无法在外部调用


Person.static_func();  // 调用静态方法必须使用类名(静态方法是属于类的而不是对象的)
obj.print_info();
obj.run_private_function();
```

## 10. 定时器

定时器就是在一段特定的时间后执行某段程序代码。

### 1. 定时器的使用

`JavaScript`定时器有两种创建方式：

1. `setTimeout(func[, delay, params_1, params_2, ...])` ：以指定的时间间隔（以毫秒计）调用一次函数的定时器
2. `setInterval(func[, delay, params_1, params_2, ...])`：以指定的时间间隔（以毫秒计）重复调用一个函数的定时器

> `setTimeout`函数的参数说明

- 第一个参数`func`：表示定时器要执行的函数名
- 第二个参数`delay`：表示时间间隔，默认是`0`，单位是毫秒
- 第三个参数`params`：表示定时器执行函数的第一个参数，一次类推传入多个执行函数对应的参数

```JavaScript
function run_test(){
    console.log("这是一个测试函数");
}

/* setTimeout: 延迟执行一次 */
setTimeout(run_test, 3000);  // timeout: 单位是毫秒
```

> `setInterval`函数的参数说明

- 第一个参数`func`：表示定时器要执行的函数名
- 第二个参数`delay`：表示时间间隔，默认是`0`，单位是毫秒
- 第三个参数`params`：表示定时器执行函数的第一个参数，一次类推传入多个执行函数对应的参数

```JavaScript
function run_test(){
    console.log("这是一个测试函数");

/* setInterval: 周期性执行 */
setInterval(run_test, 2000);
```

### 2. 清除定时器

`JavaScript`清除定时器分别是：

- `clearTimeout(timeoutID)`：清除只执行一次的定时器（`setTimeout`函数）
- `clearInterval(timeoutID)`：清除反复执行的定时器（`setInterval`函数）

> `clearTimeout`函数的参数说明

`timeoutID`为调用`setTimeout`函数时所获得的返回值，使用该返回标识符作为参数，可以取消该 `setTimeout`所设定的定时执行操作

```JavaScript
function run_test(){
    console.log("这是一个测试函数");
}

// 创建定时器
var obj = setTimeout(run_test, 3000);

// 清除只执行一次的定时器
clearTimeout(obj);
```

> `clearInterval`函数的参数说明

`timeoutID`为调用`clearInterval`函数时所获得的返回值，使用该返回标识符作为参数，可以取消该 `setTimeout`所设定的定时执行操作

```JavaScript
function run_test(){
    console.log("这是一个测试函数");
}

var handler = setInterval(run_test, 1000);
clearInterval(handler);
```

### 3. 方法置空

将`setInterval`函数原有的引用修改成自行创建的函数引用

```JavaScript
function run_test(){
    console.log("这是一个测试函数");
}

setInterval = function () {
    // console.log(arguments[0]);
    arguments[0]();
}

setInterval(run_test)
```

## 11. 异步编程

异步代码是指不按照代码的顺序执行，而是在某个事件触发之后才会执行。也就是说，异步代码不会阻塞代码的执行，可以在等待某些操作完成的同时继续执行其他代码。

异步代码的应用主要是在一些需要等待操作结果的复杂操作中，比如网络请求、文件读写等。这些操作需要等待一定时间才能获取结果，如果使用同步代码来实现，就会导致代码的执行被阻塞。

### 1. `setTimeout` 异步示例

> 同步代码

```JavaScript
// 1. 同步代码示例
console.log('===== 同步代码示例 =====');
console.log('第一行');
console.log('第二行');
console.log('第三行');
```

> 异步代码示例（使用定时器）

```JavaScript
console.log('\n===== 异步代码示例 =====');
console.log('开始');

// setTimeout的基本使用
function printMessage() {
  console.log('我是2秒后才执行的代码');
}

setTimeout(printMessage, 2000);  // 2000表示2000毫秒，也就是2秒
console.log('结束');
```

> 多个定时器的执行顺序

```JavaScript
console.log('\n===== 多个定时器示例 =====');
console.log('开始执行');

function print_1() {
    console.log('第一个定时器：等待1秒');
}

function print_2() {
    console.log('第二个定时器：等待2秒');
}

setTimeout(print_1, 1000);  // 1秒后执行
setTimeout(print_2, 2000);  // 2秒后执行

console.log('结束执行');
```

执行代码后观察代码结果：

1. 同步代码会立即执行
2. `setTimeout`中的代码会延迟执行
3. 程序不会等待`setTimeout`，会继续执行后面的代码

> 补充：回调地狱（不要学习这种代码编写风格）

```JavaScript
console.log('\n=== 回调地狱示例 ===');
setTimeout(() => {
    console.log('第一层');
    setTimeout(() => {
        console.log('第二层');
        setTimeout(() => {
            console.log('第三层');
        }, 1000);
    }, 1000);
}, 1000);
```

### 2. `Promise` 执行异步

`Promise`是一种异步代码实现方式，它可以更好地处理异步操作的结果。在`JavaScript`中，`Promise`对象代表了一个异步操作的最终完成或失败状态，并提供了相应的方法处理异步操作的结果。

> `Promise`的定义方式

```JavaScript
/*
    1. Promise的基本概念：处理异步操作的一种方式
    2. Promise的三种状态：pending（进行中）、fulfilled（成功）、rejected（失败）
    3. Promise的基本用法
*/

new Promise(function (resolve, reject) {
    // 要做的事情...
});
```

`resolve`和`reject`是`Promise`构造函数中传递的两个回调函数参数，用于控制`Promise`的状态。

1. `resolve`
	1. 用于将`Promise`的状态从`pending`（进行中）变为`fulfilled`（成功）
	2. 调用`resolve`时，可以传递一个值，这个值会作为`then`方法中回调函数的参数

2. `reject`
	1. 用于将`Promise`的状态从`pending`（进行中）变为`rejected`（失败）
	2. 调用`reject`时，可以传递一个错误信息，这个信息作为`catch`方法中回调函数的参数

> 代码示例

```JavaScript
function simplePromise() {
    var promise = new Promise(function (resolve, reject) {
        // 模拟异步操作
        var success = true;

        if (success) {
            // resolve表示操作成功并返回结果
            resolve('操作成功...');
        } else {
            // reject表示操作失败并返回错误信息
            reject('操作失败...');
        }
    });
    return promise;
}

// 使用promise
var obj = simplePromise();
obj.then(function (result) {  // then: 类似python中的gather返回任务结果
    console.log('成功:', result);
}).catch(function (error) {  // catch用于处理错误, 类似python中的except
    console.log('失败:', error);
});
```

> 带延时的`Promise`示例

```JavaScript
function delayPromise() {
    var promise = new Promise(function (resolve, reject) {
        console.log('promise开始执行...');
        // 模拟延迟操作
        setTimeout(function () {
            resolve('延迟1秒后操作成功...');
        }, 1000);
    });
    return promise;
}

console.log('调用延迟promise之前...')
delayPromise().then(function (result) {
    console.log('成功:', result);
});
console.log('调用延迟promise之后...')
```

> `Promise` 链式调用示例

```JavaScript
function step_1() {
    return new Promise(function (resolve) {
        setTimeout(function () {
            console.log('第一步完成...');
            resolve('result: step_1...');
        }, 1000);
    });
}

function step_2(step_1_result) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            console.log('第二步完成, 第一步的结果为:', step_1_result);
            resolve('result: step_2...');
        }, 1000);
    });
}

function step_3(step_2_result) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            console.log('第三步完成, 第二步的结果为:', step_2_result);
            resolve('result: step_3...');
        }, 1000);
    });
}

// 执行链式调用
step_1().then(step_2).then(step_3).then(function (result) {
    console.log('最终结果:', result);
});
```

### 3. `setTimeout` 和 `Promise` 的主要区别

1. 功能不同：
	1. `setTimeout`主要用于延时执行
	2. `Promise`用于处理异步操作的结果（成功/失败）

2. 使用方式不同：
	1. `setTimeout`只需要传入回调函数和时间
	2. `Promise`可以通过`then`和`catch`处理成功和失败的情况

3. 代码可读性：
	1. `setTimeout`容易产生回调地狱
	2. `Promise`可以通过链式调用，代码更清晰

4. 错误处理：
	1. `setTimeout`需要在回调函数中自己处理错误
	2. `Promise`可以统一用`catch`处理错误

### 4. `async` 与 `await`

`sync / await`是`ES7`中新增的异步代码实现方式，它可以更好地处理异步操作的结果。

```JavaScript
function run_async() {
    return new Promise(function (resolve) {
        var async_result = 'async data';
        resolve(async_result);
    });
}

async function get_async_result() {
    var result = await run_async();
    console.log(result);
}

console.log(get_async_result());

// 使用箭头函数返回任务信息
// get_async_result().then(() => {
//     console.log('async/await执行完毕');
// });

// 使用普通调用方式返回任务信息
// get_async_result().then(function() {
//     console.log('async/await执行完毕');
// });
```

## 12. `JSON` 数据格式




```JavaScript


```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```

```


```JavaScript

```


```JavaScript

```


```JavaScript

```



```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript


```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```


```JavaScript

```
