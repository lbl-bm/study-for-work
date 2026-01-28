# All of this

## 基础概念

this 是 JavaScript 中的一个关键字，它引用了当前执行上下文的对象，this指向谁取决于调用方式。

1. 严格模式与非严格模式：

- 在严格模式下，this 指向 undefined，而不是全局对象。
- 在非严格模式下，全局环境和普通函数调用时，this 指向全局对象（浏览器环境下是 window 对象）。
- 在模块、ES6+默认严格模式中，顶层的 this 指向 undefined。

2. 调用方式：

- 默认绑定：作为普通函数调用时，this 指向全局对象（非严格模式下）或 undefined（严格模式下）。
- 隐式绑定：作为对象的方法调用时，this 指向该对象。
- 显式绑定：使用 call、apply、bind 方法显式绑定 this 时，this 指向绑定的对象。
- new 绑定：使用 new 关键字调用构造函数时，this 指向新创建的实例对象。
- 特殊：使用箭头函数时，this 指向定义时所在的上下文，而不是调用时的上下文。

3. 调用优先级顺序：

- new 绑定 > 显式绑定 > 隐式绑定 > 默认绑定

## 实际场景

1. 普通函数调用：

```javascript
function foo() {
  console.log(this);
}
foo(); // 非严格模式下指向全局对象（浏览器环境下是 window 对象），严格模式下指向 undefined
```

2. 作为对象的方法调用：

```javascript
const obj = {
  name: "Baimu",
  sayName() {
    console.log(this.name);
  },
};
obj.sayName(); // 输出 'Baimu'，this 指向 obj 对象
```

3. 显式绑定：

```javascript
function foo() {
  console.log(this);
}
const obj = { name: "Baimu" };
foo.call(obj); // 输出 { name: 'Baimu' }，this 指向 obj 对象
```

4. new 绑定：

```javascript
function Foo() {
  this.name = "Baimu";
}
const foo = new Foo();
console.log(foo.name); // 输出 'Baimu'，this 指向新创建的实例对象 foo
```

5. 箭头函数调用：

```javascript
const obj = {
  name: "Baimu",
  sayName: () => {
    console.log(this.name);
  },
};
obj.sayName(); // 输出 undefined，箭头函数没有自己的 this，指向定义时所在的上下文（全局对象）
```

- 注意：
- 箭头函数没有自己的 this，它的 this 指向定义时所在的上下文，而不是调用时的上下文。
- 构造函数（new 绑定）中使用箭头函数会报错，因为箭头函数没有自己的 this，不能作为构造函数。
- 箭头函数不能使用 call、apply、bind 方法显式绑定 this，因为箭头函数没有 this。

## 一些关键特性

1. class 与 this：

- class 中的方法默认是严格模式，this 指向实例对象。
- class 中的方法可以使用 this 访问实例对象的属性和方法。
- 将方法作为回调时，this 指向调用时的上下文，而不是类的实例对象，所以 this 可能会丢失，需要注意使用 bind 方法绑定 this 或使用箭头函数定义类的方法。
- 可以使用箭头函数定义类的方法，这样 this 就指向类的实例对象。

```javascript
class Foo {
  constructor() {
    this.name = "Baimu";
  }
  sayName = () => {
    console.log(this.name);
  };
}
const foo = new Foo();
foo.sayName(); // 输出 'Baimu'，this 指向类的实例对象 foo
```

2. 事件处理中的 this：

- 事件处理函数中的 this 指向触发事件的元素（DOM 元素）。
- 可以使用 bind 方法绑定 this 或使用箭头函数定义事件处理函数，这样 this 就指向类的实例对象。

```javascript
class Foo {
  constructor() {
    this.name = "Baimu";
  }
  sayName = () => {
    console.log(this.name);
  };
}
const foo = new Foo();
document.getElementById("btn").addEventListener("click", foo.sayName); // 点击按钮时输出 'Baimu'，因为使用了箭头函数定义事件处理函数，所以 this 指向类的实例对象 foo
```

3. 箭头函数的作用域捕获：

- 箭头函数没有自己的作用域，它的作用域是定义时所在的上下文。
- 可以在箭头函数中访问定义时所在上下文的变量和函数。
- 对回调函数使用箭头函数，可以避免频繁绑定导致的 this 变化问题，但要注意解绑引用的模式，否则可能会导致内存泄漏。

```javascript
const name = "Baimu";
const obj = {
  name: "LBL",
  sayName: () => {
    console.log(this.name);
  },
};
obj.sayName(); // 输出 'Baimu'，箭头函数没有自己的 this，指向定义时所在的上下文（全局对象）
```

4. this 的回收与内存管理：

- 当一个函数执行完成后，它的 this 引用会被回收，指向 undefined。
- 对回调函数使用箭头函数时，要注意解绑引用的模式，否则可能会导致内存泄漏。

5. 模块化、严格模式的关系：

- 在模块和类的代码往往更倾向于使用严格模式，导致默认全局 this 指向 undefined。

## call、apply、bind 方法

1. call 方法：
   - call 方法可以调用一个函数，并指定该函数的 this 值和参数。
   - 第一个参数是要绑定的 this 值，后面的参数是要传递给函数的参数。
   ```javascript
   function foo(a, b) {
     console.log(this, a, b);
   }
   const obj = { name: "Baimu" };
   foo.call(obj, 1, 2); // 输出 { name: 'Baimu' } 1 2，this 指向 obj 对象
   ```
   自定义 call 方法：
   ```javascript
   Function.prototype.myCall = function (context, ...args) {
     context = context || window;
     context.fn = this; // 将调用 call 方法的函数赋值给 context 对象的 fn 属性
     const result = context.fn(...args); // 调用函数并传递参数
     delete context.fn; // 删除临时属性
     return result;
   };
   ```
2. apply 方法：
   - apply 方法与 call 方法类似，不同之处在于参数的传递方式。
   - 第一个参数是要绑定的 this 值，第二个参数是一个参数数组。
   ```javascript
   function foo(a, b) {
     console.log(this, a, b);
   }
   const obj = { name: "Baimu" };
   foo.apply(obj, [1, 2]); // 输出 { name: 'Baimu' } 1 2，this 指向 obj 对象
   ```
   自定义 apply 方法：
   ```javascript
   Function.prototype.myApply = function (context, argsArray) {
     context = context || window;
     context.fn = this; // 将调用 apply 方法的函数赋值给 context 对象的 fn 属性
     const result = context.fn(...argsArray); // 调用函数并传递参数数组
     delete context.fn; // 删除临时属性
     return result;
   };
   ```
3. bind 方法：
   - bind 方法与 call、apply 方法不同，它不会立即调用函数，而是返回一个新的函数。
   - 第一个参数是要绑定的 this 值，后面的参数是要传递给函数的参数。
   ```javascript
   function foo(a, b) {
     console.log(this, a, b);
   }
   const obj = { name: "Baimu" };
   const boundFoo = foo.bind(obj, 1, 2);
   boundFoo(); // 输出 { name: 'Baimu' } 1 2，this 指向 obj 对象
   ```
   自定义 bind 方法：
   ```javascript
   Function.prototype.myBind = function (context, ...args) {
     context = context || window;
     context.fn = this; // 将调用 bind 方法的函数赋值给 context 对象的 fn 属性
     return function (...args2) {
       const result = context.fn(...args, ...args2); // 调用函数并传递参数
       delete context.fn; // 删除临时属性
       return result;
     };
   };
   ```

## 面试常见场景题

### 1. 隐式丢失问题

**代码：**

```javascript
const obj = {
  name: "Baimu",
  getName: function () {
    return this.name;
  },
};

const getName = obj.getName;
console.log(getName());
console.log(obj.getName());
```

**答案与解析：**

- `getName()` 输出 `undefined` (非严格模式下如果全局有name则输出全局name，严格模式报错或undefined)。
  - **原因**：`const getName = obj.getName` 只是将函数引用赋值给变量 `getName`。调用 `getName()` 时，属于**默认绑定**，`this` 指向全局对象（浏览器 window，Node global）。
- `obj.getName()` 输出 `'Baimu'`。
  - **原因**：这是**隐式绑定**，函数作为对象的方法被调用，`this` 指向调用它的对象 `obj`。

### 2. 嵌套函数与箭头函数

**代码：**

```javascript
const obj = {
  name: "Baimu",
  showName: function () {
    console.log("Outer:", this.name);

    function inner() {
      console.log("Inner:", this.name);
    }

    const arrowInner = () => {
      console.log("Arrow Inner:", this.name);
    };

    inner();
    arrowInner();
  },
};

obj.showName();
```

**答案与解析：**

- `Outer: Baimu`
  - **原因**：`obj.showName()` 调用，`this` 隐式绑定到 `obj`。
- `Inner: undefined` (非严格模式下可能为 window.name)
  - **原因**：`inner()` 是在 `showName` 内部作为**普通函数**调用的，属于**默认绑定**，`this` 指向全局对象（严格模式下是 undefined）。
- `Arrow Inner: Baimu`
  - **原因**：箭头函数没有自己的 `this`，它会捕获定义时上下文的 `this`。这里定义在 `showName` 函数作用域中，而 `showName` 的 `this` 是 `obj`，所以箭头函数的 `this` 也是 `obj`。

### 3. 定时器中的 this

**代码：**

```javascript
const obj = {
  name: "Baimu",
  say: function () {
    setTimeout(function () {
      console.log("setTimeout:", this.name);
    }, 100);

    setTimeout(() => {
      console.log("Arrow setTimeout:", this.name);
    }, 100);
  },
};

obj.say();
```

**答案与解析：**

- `setTimeout: undefined`
  - **原因**：`setTimeout` 的回调函数通常是在全局上下文中调用的（或者由定时器线程调用），如果不使用箭头函数或 `bind`，普通函数的 `this` 会指向全局对象。
- `Arrow setTimeout: Baimu`
  - **原因**：箭头函数的 `this` 继承自外层 `say` 函数的 `this`，也就是 `obj`。

### 4. 构造函数返回值

**代码：**

```javascript
function Person(name) {
  this.name = name;
  return { name: "Interloper" };
}

function Animal(name) {
  this.name = name;
  return "Not an object";
}

const p = new Person("Baimu");
const a = new Animal("Cat");

console.log(p.name);
console.log(a.name);
```

**答案与解析：**

- `Interloper`
  - **原因**：构造函数如果显式返回一个**对象**，那么 `new` 表达式的结果就是这个返回的对象，而不是新创建的实例。
- `Cat`
  - **原因**：构造函数如果返回一个**非对象类型**（如字符串、数字、null等），该返回值会被忽略，`new` 表达式的结果仍然是新创建的实例。

### 5. 综合题：赋值表达式

**代码：**

```javascript
let name = "Global";
const obj = {
  name: "Obj",
  getName: function () {
    return this.name;
  },
};

const obj2 = {
  name: "Obj2",
};

console.log((obj2.getName = obj.getName)());
```

**答案与解析：**

- `Global` (浏览器非严格模式) 或 `undefined`
  - **原因**：`(obj2.getName = obj.getName)` 是一个赋值表达式，它的返回值是目标函数本身（即 `obj.getName` 指向的那个函数）。
  - 紧接着 `()` 立即调用这个函数。此时变成了普通函数调用（Default Binding），而不是方法调用。因此 `this` 指向全局对象。

### 6. 参数中的 this

**代码：**

```javascript
var length = 10;
function fn() {
  console.log(this.length);
}

var obj = {
  length: 5,
  method: function (fn) {
    fn();
    arguments[0]();
  },
};

obj.method(fn, 1);
```

**答案与解析：**

- `10` (非严格模式浏览器)
  - **原因**：`fn()` 直接调用，`this` 指向全局 window，`window.length` 通常等于 iframe 数量，这里假设代码运行在声明了 `var length = 10` 的全局环境下。
- `2`
  - **原因**：`arguments[0]()` 调用的是 `fn`，但此时 `fn` 是作为 `arguments` 对象的一个属性（索引 `0`）被调用的。所以 `this` 指向 `arguments` 对象。`obj.method(fn, 1)` 传入了两个参数，所以 `arguments.length` 是 2。
