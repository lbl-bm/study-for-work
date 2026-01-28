# ES6+ 新特性与面试题典

本文档按照面试中的常考程度和重要程度对 ES6+ 新特性进行排序和整理。

## 1. Promise 与 Async/Await

### 1.1 基本概念
*   **Promise**: 异步编程的一种解决方案，比传统的解决方案（回调函数）更合理和更强大。它是一个对象，从它可以获取异步操作的消息。有三种状态：`pending`（进行中）、`fulfilled`（已成功）和 `rejected`（已失败）。
*   **Async/Await**: ES8 引入的异步编程语法糖，基于 Promise 实现，使异步代码看起来像同步代码，增强了代码的可读性。

### 1.2 应用场景
*   网络请求（Fetch API, Axios）。
*   文件读写等耗时操作。
*   解决回调地狱（Callback Hell）。

### 1.3 面试常见题

#### Q1: Promise 的状态及转换规则？
**答**：
*   状态：`pending` => `fulfilled` 或 `pending` => `rejected`。
*   特点：状态一旦改变就不会再变（不可逆），任何时候都可以得到这个结果。

#### Q2: Async/Await 与 Promise 的区别？
**答**：
*   `async/await` 是基于 `Promise` 的语法糖。
*   `async/await` 使异步代码看起来像同步代码，可读性更好，调试更方便（try/catch）。
*   `Promise` 链式调用过长时，错误处理和参数传递仍较繁琐。

#### Q3: 代码输出题 (Event Loop)
```javascript
console.log('script start');

async function async1() {
  await async2();
  console.log('async1 end');
}
async function async2() {
  console.log('async2 end');
}
async1();

setTimeout(function() {
  console.log('setTimeout');
}, 0);

new Promise(resolve => {
  console.log('Promise');
  resolve();
})
.then(function() {
  console.log('promise1');
})
.then(function() {
  console.log('promise2');
});

console.log('script end');
```
**答案与解析**：
输出顺序：
1.  `script start`
2.  `async2 end`
3.  `Promise`
4.  `script end`
5.  `async1 end`
6.  `promise1`
7.  `promise2`
8.  `setTimeout`

**核心点**：
*   `await` 后面的代码（同一行执行完后）会放入微任务队列（Microtask）。
*   `Promise.then` 是微任务。
*   `setTimeout` 是宏任务（Macrotask）。
*   同步代码优先执行。

#### Q4: 手写 Promise.all
```javascript
function myPromiseAll(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises)) {
      return reject(new TypeError('Argument must be an array'));
    }
    
    let resolvedCounter = 0;
    const results = [];
    const len = promises.length;

    if (len === 0) return resolve([]);

    for (let i = 0; i < len; i++) {
      // 使用 Promise.resolve 包装，防止传入非 Promise 值
      Promise.resolve(promises[i]).then(value => {
        results[i] = value; // 保证结果顺序与输入顺序一致
        resolvedCounter++;
        if (resolvedCounter === len) {
          resolve(results);
        }
      }, error => {
        reject(error); // 只要有一个失败，整体直接失败
      });
    }
  });
}
```

---

## 2. Let 与 Const

### 2.1 基本概念
*   **let**: 声明变量，具有块级作用域，不存在变量提升，存在暂时性死区（TDZ）。
*   **const**: 声明常量，声明时必须赋值，内存地址不可修改（对于对象，属性可改）。

### 2.2 面试常见题

#### Q1: var, let, const 的区别？
| 特性 | var | let | const |
| :--- | :--- | :--- | :--- |
| 作用域 | 函数作用域 | 块级作用域 | 块级作用域 |
| 变量提升 | 有（初始化为undefined） | 无 | 无 |
| 暂时性死区 | 无 | 有 | 有 |
| 重复声明 | 允许 | 不允许 | 不允许 |
| 初始值 | 可选 | 可选 | 必须 |
| 重新赋值 | 允许 | 允许 | 不允许（值/引用地址） |

#### Q2: 暂时性死区 (TDZ) 代码题
```javascript
var a = 10;
function test() {
  console.log(a);
  let a = 20;
}
test();
```
**答案**：报错 `ReferenceError: Cannot access 'a' before initialization`。
**解析**：虽然外部有 `var a`，但函数内部 `let a` 绑定了块级作用域，在声明前访问会触发 TDZ。

---

## 3. 箭头函数

### 3.1 基本概念
*   ES6 允许使用“箭头”（`=>`）定义函数。语法简洁，没有自己的 `this`，`arguments`，`super` 或 `new.target`。

### 3.2 面试常见题

#### Q1: 箭头函数与普通函数的区别？
1.  **语法**：箭头函数更简洁。
2.  **this**：箭头函数没有自己的 `this`，捕获定义上下文的 `this`；普通函数的 `this` 指向调用者。
3.  **构造函数**：箭头函数不能作为构造函数，不能使用 `new`（没有 `prototype`）。
4.  **arguments**：箭头函数没有 `arguments` 对象，可用剩余参数 `...args` 代替。
5.  **Generator**：箭头函数不能用作 Generator 函数，不能使用 `yield`。

#### Q2: 为什么箭头函数不能作为构造函数？
**答**：
*   因为箭头函数没有自己的 `this`，无法在 `new` 调用时将 `this` 绑定到新实例。
*   箭头函数没有 `prototype` 属性。

---

## 4. 解构赋值与展开运算符

### 4.1 基本概念
*   **解构赋值**：按照一定模式，从数组和对象中提取值，对变量进行赋值。
*   **展开运算符 (`...`)**：将数组或对象展开为用逗号分隔的参数序列。

### 4.2 面试常见题

#### Q1: 使用解构交换变量
```javascript
let a = 1;
let b = 2;
[a, b] = [b, a];
```

#### Q2: 展开运算符是深拷贝还是浅拷贝？
**答**：**浅拷贝**（第一层）。
```javascript
let obj = { a: 1, b: { c: 2 } };
let obj2 = { ...obj };
obj2.b.c = 3;
console.log(obj.b.c); // 3，说明是浅拷贝
```

---

## 5. ES6 Module 模块化

### 5.1 基本概念
*   使用 `import` 和 `export` 关键字。
*   设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系（Tree Shaking 的基础）。

### 5.2 面试常见题

#### Q1: ES Module 与 CommonJS 的区别？
| 特性 | CommonJS (require/module.exports) | ES Module (import/export) |
| :--- | :--- | :--- |
| 加载方式 | 运行时加载 | 编译时输出接口（静态） |
| 输出值 | 值的**拷贝**（浅拷贝） | 值的**引用** |
| this | 指向当前模块 | `undefined` |
| 异步/同步 | 同步加载（主要用于服务端） | 浏览器端支持异步加载 |

**重点解析 - 值引用 vs 值拷贝**：
*   CommonJS 模块输出的是一个值的拷贝，模块内部变化不会影响导出的值（除非导出的是对象引用）。
*   ES Module 模块输出的是值的引用，JS 引擎对脚本静态分析的时候，遇到模块加载命令 `import`，就会生成一个只读引用。等到脚本真正执行时，再根据这个引用，到被加载的那个模块里面去取值。

---

## 6. Class 类

### 6.1 基本概念
*   ES6 提供了更接近传统语言的写法，引入了 `Class`（类）这个概念，作为对象的模板。通过 `class` 关键字，可以定义类。
*   绝大部分功能，ES5 都可以做到，`class` 写法只是让对象原型的写法更加清晰、更像面向对象编程的语法糖。

### 6.2 面试常见题

#### Q1: ES5 继承与 ES6 继承的区别？
*   **ES5**：先创建子类的实例对象 `this`，然后再将父类的方法添加到 `this` 上面（`Parent.apply(this)`）。
*   **ES6**：先将父类实例对象的属性和方法，加到 `this` 上面（所以必须先调用 `super` 方法），然后再用子类的构造函数修改 `this`。

#### Q2: super 关键字的作用？
*   作为函数调用时（`super()`），代表父类的构造函数。
*   作为对象调用时（`super.prop`），在普通方法中指向父类的原型对象；在静态方法中指向父类。

---

## 7. Proxy 与 Reflect

### 7.1 基本概念
*   **Proxy**: 用于修改某些操作的默认行为，等同于在语言层面做出修改，属于一种“元编程”。
*   **Reflect**: 将 Object 对象的一些明显属于语言内部的方法（如 `Object.defineProperty`），放到 `Reflect` 对象上。

### 7.2 面试常见题

#### Q1: Vue2 和 Vue3 响应式原理的区别？
*   **Vue2**: 使用 `Object.defineProperty` 劫持对象属性的 `get` 和 `set`。
    *   缺点：无法检测对象属性的新增和删除；无法监控数组下标的变化（需要重写数组方法）。
*   **Vue3**: 使用 `Proxy` 代理整个对象。
    *   优点：可以拦截对象的所有操作（包括新增/删除属性）；可以监听数组变化；性能更好（懒代理）。

---

## 8. Map, Set, WeakMap, WeakSet

### 8.1 基本概念
*   **Set**: 类似于数组，但是成员的值都是唯一的，没有重复的值。
*   **Map**: 类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
*   **WeakMap/WeakSet**: 键必须是对象（WeakSet 成员必须是对象），且是弱引用，垃圾回收机制不考虑对该对象的引用。

### 8.2 面试常见题

#### Q1: Set 的应用场景？
*   数组去重：`[...new Set(array)]`。
*   求交集、并集、差集。

#### Q2: WeakMap 的应用场景？
*   **防止内存泄漏**：在 DOM 元素上存储数据。当 DOM 元素被清除时，对应的 WeakMap 记录也会自动被垃圾回收。
*   部署私有属性。
