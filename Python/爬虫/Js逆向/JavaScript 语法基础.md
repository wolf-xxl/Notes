
## 1. JavaScript 简介

### 什么是 JavaScript？
- 一种轻量级的解释型编程语言
- 主要用于网页交互，现在也可用于服务器端(Node.js)
- 支持面向对象、命令式和函数式编程风格

### JavaScript 的组成
- **ECMAScript**: 语言核心
- **DOM**: 文档对象模型
- **BOM**: 浏览器对象模型

## 2. 变量和数据类型

### 2.1 变量声明
```javascript
// var (函数作用域，可重复声明)
var name = "John";
var name = "Doe"; // 允许重复声明

// let (块级作用域，不可重复声明)
let age = 25;
// let age = 30; // 报错：不能重复声明

// const (块级作用域，常量)
const PI = 3.14159;
// PI = 3.14; // 报错：常量不能重新赋值
```

### 2.2 数据类型

#### 基本数据类型 (Primitive Types)
```javascript
// 1. String
let name = "Alice";
let message = 'Hello World';
let template = `My name is ${name}`; // 模板字符串

// 2. Number
let integer = 42;
let float = 3.14;
let scientific = 1.5e3; // 1500
let infinity = Infinity;
let notANumber = NaN;

// 3. Boolean
let isActive = true;
let isCompleted = false;

// 4. Undefined
let undefinedVar; // 值为 undefined

// 5. Null
let emptyValue = null;

// 6. Symbol (ES6)
let sym = Symbol("description");

// 7. BigInt (ES2020)
let bigNumber = 123456789012345678901234567890n;
```

#### 引用数据类型 (Reference Types)
```javascript
// Object
let person = {
    name: "John",
    age: 30,
    isStudent: false
};

// Array
let fruits = ["apple", "banana", "orange"];

// Function
function greet() {
    return "Hello!";
}

// Date
let today = new Date();
```

### 2.3 类型检查
```javascript
// typeof 操作符
console.log(typeof "hello");     // "string"
console.log(typeof 42);          // "number"
console.log(typeof true);        // "boolean"
console.log(typeof undefined);   // "undefined"
console.log(typeof null);        // "object" (历史遗留问题)
console.log(typeof {});          // "object"
console.log(typeof []);          // "object"
console.log(typeof function(){});// "function"

// 更准确的类型检查
console.log(Array.isArray([]));  // true
console.log([] instanceof Array); // true
```

### 2.4 类型转换
```javascript
// 显式转换
let num = Number("123");     // 字符串转数字
let str = String(123);       // 数字转字符串
let bool = Boolean(1);       // 真值转换

// 隐式转换
let result = "5" + 2;        // "52" (字符串连接)
let sum = "5" - 2;           // 3 (数字运算)

//  truthy 和 falsy 值
console.log(Boolean(""));     // false
console.log(Boolean(0));      // false  
console.log(Boolean(null));   // false
console.log(Boolean(undefined)); // false
console.log(Boolean(NaN));    // false
console.log(Boolean("hello")); // true
console.log(Boolean(1));      // true
console.log(Boolean([]));     // true
```

## 3. 运算符

### 3.1 算术运算符
```javascript
let a = 10, b = 3;

console.log(a + b);   // 13
console.log(a - b);   // 7
console.log(a * b);   // 30
console.log(a / b);   // 3.333...
console.log(a % b);   // 1 (取余)
console.log(a ** b);  // 1000 (幂运算)

// 自增自减
let x = 5;
console.log(x++);     // 5 (后置)
console.log(++x);     // 7 (前置)
```

### 3.2 比较运算符
```javascript
console.log(5 == "5");    // true (值相等)
console.log(5 === "5");   // false (严格相等)
console.log(5 != "5");    // false
console.log(5 !== "5");   // true

console.log(5 > 3);       // true
console.log(5 < 3);       // false
console.log(5 >= 5);      // true
console.log(5 <= 3);      // false
```

### 3.3 逻辑运算符
```javascript
let isTrue = true, isFalse = false;

console.log(isTrue && isFalse);   // false (与)
console.log(isTrue || isFalse);   // true (或)
console.log(!isTrue);             // false (非)

// 短路求值
let name = "";
let displayName = name || "Anonymous"; // "Anonymous"
```

### 3.4 赋值运算符
```javascript
let x = 10;
x += 5;  // x = x + 5 → 15
x -= 3;  // x = x - 3 → 12
x *= 2;  // x = x * 2 → 24
x /= 4;  // x = x / 4 → 6
```

### 3.5 三元运算符
```javascript
let age = 20;
let canVote = age >= 18 ? "Yes" : "No"; // "Yes"
```

## 4. 流程控制

### 4.1 条件语句
```javascript
// if-else
let score = 85;

if (score >= 90) {
    console.log("优秀");
} else if (score >= 70) {
    console.log("良好");
} else {
    console.log("需要努力");
}

// switch
let day = "Monday";
switch (day) {
    case "Monday":
        console.log("开始新的一周");
        break;
    case "Friday":
        console.log("周末快到了");
        break;
    default:
        console.log("普通的一天");
}
```

### 4.2 循环语句
```javascript
// for 循环
for (let i = 0; i < 5; i++) {
    console.log(i); // 0,1,2,3,4
}

// while 循环
let count = 0;
while (count < 3) {
    console.log(count); // 0,1,2
    count++;
}

// do-while 循环
let num = 0;
do {
    console.log(num); // 0
    num++;
} while (num < 1);

// for...of (遍历可迭代对象)
let fruits = ["apple", "banana", "orange"];
for (let fruit of fruits) {
    console.log(fruit);
}

// for...in (遍历对象属性)
let person = {name: "John", age: 30};
for (let key in person) {
    console.log(key + ": " + person[key]);
}
```

### 4.3 循环控制
```javascript
// break
for (let i = 0; i < 10; i++) {
    if (i === 5) break;
    console.log(i); // 0,1,2,3,4
}

// continue
for (let i = 0; i < 5; i++) {
    if (i === 2) continue;
    console.log(i); // 0,1,3,4
}
```

## 5. 函数

### 5.1 函数声明
```javascript
// 函数声明
function greet(name) {
    return `Hello, ${name}!`;
}

// 函数表达式
const multiply = function(a, b) {
    return a * b;
};

// 箭头函数 (ES6)
const divide = (a, b) => a / b;

// 调用函数
console.log(greet("Alice"));     // "Hello, Alice!"
console.log(multiply(4, 5));     // 20
console.log(divide(10, 2));      // 5
```

### 5.2 函数参数
```javascript
// 默认参数
function createUser(name, age = 18) {
    return {name, age};
}

// 剩余参数
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4)); // 10

// 参数解构
function printPerson({name, age}) {
    console.log(`${name} is ${age} years old`);
}
```

### 5.3 高阶函数
```javascript
// 函数作为参数
function operate(a, b, operation) {
    return operation(a, b);
}

const result = operate(10, 5, (x, y) => x + y);
console.log(result); // 15

// 返回函数
function createMultiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = createMultiplier(2);
console.log(double(5)); // 10
```

## 6. 数组

### 6.1 数组创建和操作
```javascript
// 创建数组
let fruits = ["apple", "banana", "orange"];
let numbers = new Array(1, 2, 3);

// 访问元素
console.log(fruits[0]);      // "apple"
console.log(fruits.length);  // 3

// 修改数组
fruits.push("grape");        // 末尾添加
fruits.pop();                // 末尾删除
fruits.unshift("melon");     // 开头添加
fruits.shift();              // 开头删除
```

### 6.2 数组方法
```javascript
let numbers = [1, 2, 3, 4, 5];

// 遍历方法
numbers.forEach(num => console.log(num));

// 映射
let doubled = numbers.map(num => num * 2); // [2,4,6,8,10]

// 过滤
let even = numbers.filter(num => num % 2 === 0); // [2,4]

// 查找
let found = numbers.find(num => num > 3); // 4
let index = numbers.findIndex(num => num > 3); // 3

// 归约
let sum = numbers.reduce((total, num) => total + num, 0); // 15

// 排序
let sorted = numbers.sort((a, b) => b - a); // 降序
```

## 7. 对象

### 7.1 对象创建和访问
```javascript
// 对象字面量
let person = {
    name: "John",
    age: 30,
    "favorite color": "blue", // 包含空格的属性名
    greet: function() {
        return `Hello, I'm ${this.name}`;
    }
};

// 访问属性
console.log(person.name);                // "John"
console.log(person["favorite color"]);   // "blue"
console.log(person.greet());             // "Hello, I'm John"

// 添加/修改属性
person.email = "john@example.com";
person.age = 31;

// 删除属性
delete person["favorite color"];
```

### 7.2 对象方法
```javascript
let car = {
    brand: "Toyota",
    model: "Camry",
    year: 2020,
    
    // 方法简写 (ES6)
    getInfo() {
        return `${this.brand} ${this.model} (${this.year})`;
    },
    
    // 计算属性名
    ["model" + "Year"]: "2020Model"
};

console.log(car.getInfo()); // "Toyota Camry (2020)"
```

### 7.3 对象解构
```javascript
let user = {
    name: "Alice",
    age: 25,
    email: "alice@example.com"
};

// 解构赋值
let {name, age} = user;
console.log(name, age); // "Alice" 25

// 重命名
let {name: userName, email: userEmail} = user;

// 默认值
let {name, country = "Unknown"} = user;
```

## 8. 字符串操作

### 8.1 字符串方法
```javascript
let text = "Hello JavaScript World";

console.log(text.length);                // 23
console.log(text.toUpperCase());         // "HELLO JAVASCRIPT WORLD"
console.log(text.toLowerCase());         // "hello javascript world"
console.log(text.indexOf("Java"));       // 6
console.log(text.slice(6, 16));          // "JavaScript"
console.log(text.replace("World", "Earth")); // "Hello JavaScript Earth"
console.log(text.split(" "));            // ["Hello", "JavaScript", "World"]
```

### 8.2 模板字符串
```javascript
let name = "Bob";
let age = 28;

// 多行字符串
let message = `
  姓名: ${name}
  年龄: ${age}
  状态: ${age >= 18 ? "成年" : "未成年"}
`;

console.log(message);
```

## 9. 错误处理

### 9.1 try-catch
```javascript
try {
    // 可能出错的代码
    let result = riskyOperation();
    console.log(result);
} catch (error) {
    // 处理错误
    console.log("发生错误:", error.message);
} finally {
    // 无论是否出错都会执行
    console.log("操作完成");
}
```

### 9.2 抛出错误
```javascript
function divide(a, b) {
    if (b === 0) {
        throw new Error("除数不能为零");
    }
    return a / b;
}

try {
    let result = divide(10, 0);
} catch (error) {
    console.log(error.message); // "除数不能为零"
}
```

## 10. 现代 JavaScript 特性

### 10.1 ES6+ 新特性
```javascript
// 解构赋值
let [a, b] = [1, 2];
let {x, y} = {x: 10, y: 20};

// 扩展运算符
let arr1 = [1, 2, 3];
let arr2 = [...arr1, 4, 5]; // [1,2,3,4,5]

let obj1 = {a: 1, b: 2};
let obj2 = {...obj1, c: 3}; // {a:1, b:2, c:3}

// 可选链操作符 (ES2020)
let user = {profile: {name: "John"}};
console.log(user?.profile?.name); // "John"
console.log(user?.address?.city); // undefined

// 空值合并运算符 (ES2020)
let input = null;
let value = input ?? "默认值"; // "默认值"
```

## 11. 综合示例

### 简单的待办事项应用
```javascript
// 待办事项管理器
class TodoManager {
    constructor() {
        this.todos = [];
    }
    
    addTodo(text) {
        const todo = {
            id: Date.now(),
            text: text,
            completed: false,
            createdAt: new Date()
        };
        this.todos.push(todo);
        return todo;
    }
    
    completeTodo(id) {
        const todo = this.todos.find(t => t.id === id);
        if (todo) {
            todo.completed = true;
            return true;
        }
        return false;
    }
    
    getPendingTodos() {
        return this.todos.filter(todo => !todo.completed);
    }
    
    getCompletedTodos() {
        return this.todos.filter(todo => todo.completed);
    }
    
    // 使用数组方法统计
    getStats() {
        const total = this.todos.length;
        const completed = this.getCompletedTodos().length;
        const pending = this.getPendingTodos().length;
        
        return {
            total,
            completed,
            pending,
            completionRate: total > 0 ? (completed / total * 100).toFixed(1) + '%' : '0%'
        };
    }
}

// 使用示例
const todoManager = new TodoManager();
todoManager.addTodo("学习 JavaScript");
todoManager.addTodo("完成项目");
todoManager.addTodo("锻炼身体");

todoManager.completeTodo(todoManager.todos[0].id);

console.log("所有待办:", todoManager.todos);
console.log("进行中:", todoManager.getPendingTodos());
console.log("统计:", todoManager.getStats());
```

## 12. 学习建议

### 最佳实践
1. **使用 const 和 let** 而不是 var
2. **使用严格相等** (=== 和 !==)
3. **使用模板字符串** 而不是字符串连接
4. **使用箭头函数** 保持 this 上下文
5. **使用解构和扩展运算符** 简化代码

### 下一步学习
- DOM 操作和事件处理
- 异步编程 (Promise, async/await)
- 模块化开发
- 面向对象编程
- 函数式编程概念

### 调试技巧
```javascript
// 使用 console 调试
console.log("普通日志");
console.warn("警告信息");
console.error("错误信息");
console.table([{name: "John", age: 30}, {name: "Alice", age: 25}]);

// 使用 debugger 语句
function complexFunction() {
    debugger; // 浏览器会在此处暂停
    // 复杂逻辑...
}
```

这份笔记涵盖了 JavaScript 的基础语法核心概念，建议结合实际编码练习来巩固理解。每个概念都配有实际示例，可以帮助你更好地掌握 JavaScript 编程。