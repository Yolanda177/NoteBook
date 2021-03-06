# JS

> https://github.com/stephentian/33-js-concepts#6-this-call-apply-%E5%92%8C-bind

<!-- <TOC /> -->
[[toc]]

## 基础

### 执行上下文

执行上下文是评估和执行JavaScript代码的环境，有三种类型的执行上下文：
- 全局执行上下文：默认、基础的上下文，任何不在函数内的代码都在全局上下文中。它会执行两件事：创建一个全局的`window`对象，并设置`this`的值等于这个**全局对象**。一个程序中只会有一个全局上下文

- 函数执行上下文：每当一个函数被调用时，都会为这个函数创建一个新的执行上下文，每个函数都有它自己的执行上下文。每当一个新的执行上下文被创建，它会按定义的顺序执行一系列的步骤

- Eval 函数执行上下文：执行在`eval`函数内的代码也会有它自己的执行上下文，但较少使用该函数

### 执行栈

**执行栈**，也就是其他编程语言中所说的“调用栈”，是一种拥有“后进先出”(LIFO)数据结构的栈，被用来存储代码运行时创建的所有执行上下文

当 JavaScript 引擎第一次遇到脚本时，会创建一个全局的执行上下文并压入当前的执行栈。每当引擎遇到一个函数调用，它会为该函数创建一个新的执行上下文并压入栈的顶部

引擎会执行那些上下文位于栈顶的函数，当该函数执行结束时，执行上下文从栈中弹出，控制流程到达当前栈中的下一个上下文

```js
function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}

function second() {
  console.log('Inside second function');
}

first();
consolg.log('Inside Global Execution Context');
```

![](../.vuepress/public/images/zhixingzhan.png)

当上述代码在浏览器加载时，JavaScript 引擎创建了一个全局执行上下文并把它压入当前栈。当遇到`first()`函数调用时，JavaScript引擎为该函数创建了一个新的执行上下文并把它压入当前栈的顶部

当`first()`函数内部调用`second()`函数时，JavaScript引擎为`second()`函数创建了一个新的执行上下文，并把它压入当前栈的顶部。当`second()`函数执行完毕，它的执行上下文会从当前栈弹出，并且控制流程到达下一个执行上下文，即`first()`函数的执行上下文

当`first()`执行完毕，它的执行上下文从栈弹出，控制流程到达全局执行上下文，一旦所有代码执行完毕，JavaScript 引擎从当前栈中移除全局执行上下文


例：以下两段代码有什么区别？执行结果是什么？
```js
var scope = 'global scope';
function checkscope() {
  var scope = 'local scope';
  function f() {
    return scope;
  }
  return f();
}
checkscope();

var scope = 'global scope';
function checkscope() {
  var scope = 'local scope';
  function f() {
    return scope;
  }
  return f;
}
checkscope()();
```

执行结果都是`local scope`，它们的调用栈顺序不同：

第一段：全局——>函数checkscope()——>函数f()——>出栈——>出栈——>出栈

第二段：全局——>函数checkscope()——>出栈——>函数f()——>出栈——>出栈


**this 绑定**

在全局执行上下文中，`this`的值指向全局对象(在浏览器中，`this`引用Window对象)

在函数执行上下文中，`this`的值取决于该函数是如何被调用的。如果它被一个引用对象调用，那么`this`就会被设置为那个对象，否则`this`的值被设置为全局对象或`undefined`(严格模式下)

```js
let foo = {
  baz: function() {
    console.log(this)
  }
}

foo.baz(); // this 指向 foo

let bar = foo.baz();
bar(); // this 指向 window 对象
```

**变量对象**

每一个执行上下文都会分配一个变量对象，变量对象的属性由**变量和函数声明**构成。在函数上下文情况中，参数列表也会被加入到变量对象中作为属性，变量对象与当前作用域息息相关。不同作用域的变量对象互不相同，它保存了当前作用域的所有函数和变量
(要注意的是，**函数声明**会被加入到变量对象中，但**函数表达式**则不会)

```js
function a() {}
console.log(typeof a); // function

var a = function _a() {};
console.log(typeof a); // function
console.log(typeof _a); // undefined

```


进入执行上下文时，首先会处理**函数声明**，其次会处理变量声明，如果变量名跟已经声明的形式参数或函数相同时，则变量声明不会干扰已经存在的这类属性，例：

```js
console.log(foo); // 打印函数

function foo() {
  console.log('foo');
}
var foo = 1;
```

**作用域**

JavaScript 采用词法作用域(静态作用域)，作用域决定了变量的生命周期及其可见性；函数的作用域在函数定义的时候就决定了。当我们创建了一个函数或`{}`块，就会生成一个新的作用域，需要注意的是，通过`var`创建的变量只有函数作用域，而通过`let`或`const`创建的变量既有函数作用域，也有块作用域

```js
var value = 1;
function foo() {
  consloe.log(value);
}

function bar() {
  var value = 2;
  foo();
}

bar(); // 打印 1
```

### 面向对象与原型

### ajax

![ajax](../.vuepress/public/images/ajax.png)

代码例子，不借助任何库实现 `XMLHttpRequest`

```js
const xhr = new XMLHttpRequest();
// Step1: 监听状态
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    // 异步调用完毕
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
      // 异步调用成功
      console.log(xhr.responseText);
    } else {
      alert('Request was unsuccessful: ' + xhr.status);
    }
  }
};
// xhr.open(method: [get, post], url: string, async: [true, false])
// async: 默认是 true； 代表异步请求，不会阻塞页面 UI。
// 如果 async 为 false，代表同步请求，会阻塞页面直至请求成功后。
// Step2: 打开请求
xhr.open('GET', 'README.md'); // GET // 相对路径
// Step3: 发送请求
xhr.send(null);
```

#### ajax 有没有破坏 JS 单线程机制

- ajax 设置为 异步
- ajax 设置为同步
需要等待赋值成功后，再执行后就只能使用回调，可以使用 deferred 、 promise、 asycn/await ，但是注意滥用 async/await 有可能导致同步。

我们先看下浏览器线程机制，一般情况下，浏览器有如下四种线程：
- GUI 渲染线程
- javaScript 引擎线程
- 浏览器事件触发线程
- HTTP 请求线程

那么这么多线程，它们究竟是怎么同 JS 引擎线程交互的呢？

通常，它们的线程间交互**以事件的方式**发生，通过**事件回调方式**予以通知。<u>而事件回调，又是以先进先出的方式添加到`任务队列`的末尾</u>，等到 js 引擎空闲时，`任务队列`中排队的任务将会依次被执行。这些事件回调包括 setTimeout, setInterval, click, ajax 异步请求等回调。

**浏览器中，js 引擎线程会循环从`任务队列` 中读取事件并且执行，这种运行机制称作 `Event Loop`（事件循环）。**

浏览器中，js 引擎首先生成 `XMLHttpRequest` 实例对象，open 过后再调用 send 方法。至此，所有的语句都是同步执行。但从 send 方法内部开始，浏览器为将要发生的网络请求创建了**新的 http 请求线程**，这个线程独立于 js 引擎线程。于是网络请求异步被发送出去了。另一方面，js 引擎并不会等待 ajax 发起的 http 请求收到结果，而是直接顺序往下执行。

当 ajax 请求被服务器响应并且收到 response 后，浏览器事件触发线程捕获到 ajax 的回调事件 `onreadystatechange(当然也可能触发 onload，或者 onerror 等等)`。该回调事件并没有被立即执行，而是被添加到`任务队列`的末尾，直到 js 空闲了，`任务队列`的任务才被捞出来，按照添加顺序，挨个执行，当然也包括刚刚 append 到队列末尾的 `onreadystatechange` 事件。

在`onreadystatechange`事件内部，有可能对 dom 进行操作。此时浏览器便会挂起 js 引擎线程，转而执行 GUI 渲染线程，进行 UI 重绘（repaint）或者回流（reflow）。当 js 引擎重新执行时，GUI 渲染线程又会被挂起，GUI 更新将被保存起来，等到 js 引擎空闲时立即被执行。

以上整个 ajax 请求过程中，有涉及到浏览器的4种线程，其中除了 `GUI 渲染线程`和`js 引擎线程`是互斥的，其他线程相互之间，都是可以并行执行的，通过这样的一种方式，ajax 并没有破坏 js 的单线程机制。

然后要注意的是，当你设置 ajax 的 async 为 false 时（一般是为了先获取请求后的数据，给指定变量进行赋值或执行特点函数），这个时候进行的同步操作处理。这个时候并没有启动单独的线程，还是在 js 主线程执行，所以浏览器的 `GUI 渲染线程`会被阻塞掉。因此，针对这种情况的话，只能通过回调来处理。（deferred、promise 等）

### Fetch API

### 函数

JS 中的函数调用

- 作为一个函数调用
- 函数作为方法调用
- 使用构造函数调用函数
- 作为函数方法调用函数

### 事件

#### 事件阶段

- 事件冒泡
  事件从最开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播较为不具体对的节点（文档）。

下面点击了页面中的 `<div>`元素后，click 事件首先在 `<div>`发生，然后 click 事件沿着 DOM 树向上传播，在每一级节点上都会发生，直至 document 对象。

![事件冒泡](../.vuepress/public/images/event_bubbling.png)

- 事件捕获
  事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。

下面点击了页面中的 `<div>`元素后，click 事件首先在 `document`对象发生，然后 click 事件沿着 DOM 树向下传播，在每一级节点上都会发生，直至 `<div>`元素。

![事件捕获](../.vuepress/public/images/event_capturing.png)

- DOM 事件流（现代浏览器）

下面点击了页面中的 `<div>`元素后，click 事件首先在 `document`对象发生，然后 click 事件沿着 DOM 树向下传播，在每一级节点上都会发生，直至 `<div>`元素，然后继续往上冒泡。

![事件冒泡](../.vuepress/public/images/dom_event.png)

了解事件的传播有利于我们针对元素进行合适的事件监听。

#### 事件监听

现代浏览器的 DOM 2 级，事件流模型：捕获阶段-目标阶段-冒泡阶段

**DOM0级事件处理程序**

<u>通过 JavaScript 指定事件处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性。</u>这种为事件处理程序赋值的方法是在第四代 Web 浏览器中出现的，而且至今仍然为所有现代浏览器所支持。原因一是简单，二是具有跨浏览器的优势。要使用 JavaScript 指定事件处理程序，首先必须取得一个要操作的对象的引用。

每个元素（包括 window 和 document）都有自己的事件处理程序属性，这些属性通常全部小写。例如 `onclick`。将这种属性的值设置为一个函数，就可以指定事件处理程序：
```js
const btn = document.getElementById('myBtn');
btn.onclick = function() { // this 指向当前元素
  alert('Clicked');
}

// 删除事件处理哦程序
btn.onclick = null;
```
以这种方式添加的事件处理程序会在事件流的冒泡阶段被处理。

**DOM2事件处理程序**

```js
/**
 * @description: 事件监听
 * @param {String} 字符串，指定事件名
 * @param {Function} 指定要事件触发时执行的函数
 * @param {Boolean} 布尔值，指定事件是否在捕获或冒泡阶段执行
 * @return:
 */
element.addEventListener(event, function, userCapture)
```

```js
/**
 * @description: 事件移除
 * @param {String} 字符串，指定事件名
 * @param {Function} 指定要事件触发时执行的函数
 * @param {Boolean} 布尔值，指定事件是否在捕获或冒泡阶段执行
 * @return:
 */
element.removeEventListener(event, function, userCapture)
```

Internet Explorer 8 及更早 IE 版本: 目标阶段-冒泡阶段

```js
// 事件监听：
element.attatchEvent(event, function)
// 代码移除事件监听：
element.detachEvent(event, function)
```

#### 事件对象

- DOM 事件模型中的事件对象常用属性：
  - type 用于获取事件类型
  - target 获取事件目标
  - stopPropagation 阻止事件冒泡
  - preventDefault 阻止事件默认行为
- IE 事件模型中常见的事件对象常用属性
  - type 用于获取事件类型
  - srcElement 获取事件目标
  - cancelBubble 阻止事件冒泡
  - returnValue 阻止事件默认行为

#### 事件委托/代理

使用事件委托的原因：首先，在页面中添加了大量的事件处理程序，每个函数都是对象，都会占用内存；内存中的对象越多，性能就越差。其次，必须事先指定所有事件处理程序而导致的 DOM 访问次数，会延迟整个页面的交互就绪时间。

因此，对“事件处理程序过多”的问题解决方案就是事件委托，原理是利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。例如，click 事件会一直冒泡到 document 层次。也就是说，我们可以为整个页面指定一个 onclick 事件处理程序，而不必给每个可单击的元素分别添加事件处理程序。

常见的例子，是针对列表：

```html
<ul id="parent">
  <li class="child">one</li>
  <li class="child">two</li>
  <li class="child">three</li>
</ul>
<script>
  // 父元素
  const dom = document.querySelector('#parent');

  // 父元素绑定事件，代理子元素的点击事件
  dom.onclick = function(event) {
    const event = event || window.event;
    const curTarget = event.target || event.scrElement;

    if (curTarget.tagName.toLowerCase() === 'li') {
      // 事件处理
    }
  };
</script>
```

总结：事件委托可以节省内存占用，减少事件注册；对于新增子对象时无需再次对其绑定事件，适合动态添加元素。除了事件委托外，在页面卸载时，我们要及时移除事件处理程序，避免造成内存泄漏。在移除 dom 元素时，先把它绑定的事件处理程序移除。

#### 实现事件模型

todo

#### 事件广播 📢

```js
const event = new Event('build');
// listener for the event
element.addEventListener('build', function(e) {...}, false);

// Dispatch the event
element.dispatchEvent(event); // 人工触发
```

### 创建一个类

#### 面向对象的方式（ES5）

组合使用构造函数模式与原型模式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。

```js
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function() {
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);
```

#### ES6 的 class

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

**函数声明**和**类声明**之间的一个重要区别是函数声明会提升，类声明不会。你首先需要声明你的类，然后访问它，否则像下面的代码会抛出一个`ReferenceError`：

```js
let p = new Rectangle();
// ReferenceError
class Rectangle {}
```

#### jQuery 的链式调用

模拟 JQuery 的链式调用

```js
function JQuery(selector) {
  this.elements = [];
  val nodeLists = document.getElementByTagName(selector);
  for (var i = 0; i < nodeLists.length; i++) {
    this.elements.push(nodeLists[i]);
  }
  return this;
}

JQuery.prototype = {
  eq: function(num) {
    this.elements = [this.elements[num]];
    return this;
  },
  css: function(prop, val) {
    this.elements.forEach(function(el) {
      el.style[prop] = val;
    })
    return this;
  },
  show: function() {
    this.css('display', 'block');
    return this;
  }
}

window.$ = function(selector) {
  return new JQuery(selector);
}

// 使用
$('div').eq(0).css('width', '200px').show();
```

jQuery 之所以能实现链式调用，关键就在于通过 `return this`，返回调用对象。

```js
var jQuery = {
  eq: function() {
    console.log('调用 eq 方法')；
    return this;
  },
  show: function() {
    console.log('调用 show 方法')；
    return this;
  }
}
jQuery.eq().show();
```

#### jQuery 插件

另外，了解这个链式调用原理，也有利于我们理解 jQuery 的插件开发，源码中 jquery.fn = jquery.prototype，也就说 `jquery.fn` 对象就是 `jQuery` 的原型对象，`jQuery` 插件就是用来扩展 `jQuery` 原型对象的一个方法，而`jQuery`插件的使用方法就是 `jQuery` 对象方法的调用。

```js
(function($) {
  $.fn.changeStyle = function(colorStr) {
    this.css('color', colorStr);
    return this; // 满足链式调用
  };
})(jQuery); // 使用了立即执行函数，避免 $ 符号的污染其他 js 库
```

参考资料：[掌握 jQuery 插件开发，这篇文章就够了](https://juejin.im/entry/57a1b817c4c971005af56343) -- 从概念到实战，讲清了 jQuery 插件的开发。

## 进阶

### setTimeout, setInterval 和 requestAnimationFrame

### this, call, apply 和 bind

#### this 的指向

在 ES5 中，其实 this 的指向，始终坚持一个原理：**this 永远指向最后调用它的那个对象**

1. 例子：这里调用 `a` 的地方 `a()`;，前面没有调用的对象那么就是全局对象 `window`，这就相当于是 `window.a()`；注意，这里我们没有使用严格模式，如果使用严格模式的话，全局对象就是 `undefined`，那么就会报错 `Uncaught TypeError: Cannot read property 'name' of undefined。`

```js
var name = 'windowsName';
function a() {
  var name = 'Cherry';
  console.log(this.name); // windowsName
  console.log('inner:' + this); // inner: Window
}
a();
console.log('outer: ' + this); // outer: Window
```

2. 例子： 函数 fn 是对象 a 调用的，所以打印的值就是 a 中的 name 的值，上下文对象调用。

```js
var name = 'windowsName';
var a = {
  name: 'Cherry',
  fn: function() {
    console.log(this.name); // Cherry
  }
};
a.fn(); // 上下文对象调用
```

3. 函数别名

```js
var name = 'windowName';
var a = {
  name: 'Cherry',
  fn: function() {
    console.log(this.name); // Cherry
  }
};
a.fn();

var b = a.fn;
b(); // undefined b 被全局对象调用
```

4. 例子：传入回调函数，参数赋值，在不使用箭头函数的情况下，是会报错的，因为最后调用 `setTimeout` 的对象是 `window`，但是在 `window` 中并没有 `func1` 函数。
   setTimeout 方法本身就是 window 对象的一个方法，所以`setTimeout`在调用的时候，本身就是`window`在调用它，方法体内的`this`根据【方法的 this 永远指向最后调用它的 this】,

```js
var name = 'windowsName';
var a = {
  name: 'Cherry',

  func1: function() {
    console.log(this.name);
  },

  func2: function() {
    setTimeout(function() {
      this.func1();
    }, 100);
  }
};
a.func2(); // this.func1 is not a function
```

#### 改变 this 的指向

##### 使用 ES6 的箭头函数

##### 在函数内部使用 `_this=this`

##### new 实例化一个对象

##### 使用`apply`、`call`、`bind`

1. apply
   - apply() 方法调用一个函数，其具有一个指定的 this 值，以及作为一个数组（或类似数组的对象）提供的参数。
   - `fun.apply(thisArg, [argsArray])`
     - thisArg: 在 fun 函数运行时指定的 this 值。需要注意的是，指定的 this 值并不一定是该函数执行时真正的 this 值，如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是 window 对象），同时值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的自动包装对象。
     - argsArray：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数。如果该参数的值为 null 或 undefined，则表示不需要传入任何参数。

```js
var a = {
  name: 'Cherry',
  fn: function(a, b) {
    console.log(a + b);
  }
};

var b = a.fn;
b.apply(a, [1, 2]); // 3
```

2. call
   - `fun.call(thisArg, [, arg1[, arg2[, ...]]])`
   - apply 和 call 的区别时只是传入的参数不同，call 方法接受的是若干个参数列表，而 apply 接受的是一个包含多个参数的数组。

```js
var a = {
  name: 'Cherry',
  fn: function(a, b) {
    console.log(a + b);
  }
};

var b = a.fn;
b.call(a, 1, 2); // 3
```

3. bind[MDN bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

- `bind()` 方法创建一个新的函数，在`bind()`被调用时，这个新函数的`this`被 bind 的第一个参数指定，其余的参数将作为新函数的参数供调用时使用。

```js
const a = {
  name: 'Cherry',
  fn: function(a, b) {
    console.log('a + b =', a + b);
  }
};
const b = a.fn;
b.apply(a, [1, 2]); // 3
b.call(a, 1, 2); // 3
const newFn = b.bind(a, 1, 2); // bind 是创建一个新的函数，我们必须手动去调用
newFn();
```

#### 小结

如果要判断一个运行中函数的 `this`绑定，**就需要找到这个函数的直接调用位置**。找到之后就可以顺序应用下面这四条规则来判断 this 的绑定对象。

1. 由 `new` 调用？<u>绑定到新创建的对象。</u>
2. 由 `call` 或者 `apply` （或者 `bind`）调用？<u>绑定到指定的对象</u>。
3. 由上下文对象调用？<u>绑定到那个上下文对象。</u>
4. 默认：在严格模式下绑定到 `undefined`，否则<u>绑定到全局对象</u>。

一定要注意，有些调用可能在无意中使用默认绑定规则。如果想“更安全”地忽略 `this` 绑定，你可以使用一个 DMZ 对象，比如 `⌀ = Object.create(null)`，以保护全局对象。
ES6 中的箭头函数不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 this，具体来说，箭头函数会继承外层函数调用的 this 绑定（无论 this 绑定到什么）。这其实和 ES6 之前代码中的 `self = this`机制一样。

### 实现一个 call 函数

### Promise

#### 回调

JavaScript 是单线程的，这意味者任何两句代码都不能同时运行，它们得一个接一个来。在浏览器中，JavaScript 和 其他任务共享一个线程，不同的浏览器略有差异，但大体上这些和 JavaScript 共享线程的任务包括重绘、更新样式、用户交互等，所有这些任务操作都会阻塞其他任务。

作为人类，你是多线程的。你可以用多个手指同时敲键盘，也可以一边开车一边听电话。唯一的全局阻塞函数是打喷嚏，打喷嚏期间所有其他事务都会暂停。

你应该用事件加回调的办法来处理这类情况：
```js
var img1 = document.querySelector('.img-1');
 
img1.addEventListener('load', function() {
  // woo yey image loaded
});
 
img1.addEventListener('error', function() {
  // argh everything's broken
});
```
上面的例子唯一的问题是，事件有可能在我们绑定监听器之前就已经发生，所以我们先要检查图片的 complete 属性：
```js

var img1 = document.querySelector('.img-1');
 
function loaded() {
  // woo yey image loaded
}
 
if (img1.complete) {
  loaded();
}
else {
  img1.addEventListener('load', loaded);
}
 
img1.addEventListener('error', function() {
  // argh everything's broken
});
```
这样还不够，如果在添加监听函数之前图片加载发生错误，我们的监听函数还是白费，不幸的是 DOM 也没有为这个需求提供解决方法。而且，这只是处理一张图片的情况，如果有一堆图片要处理那就更麻烦了。

初次之外，你还可能会遇到这种情况，回调的不断嵌套。
```js
input.onblur = isUserTooYoung;
function isUserTooYoung(id, callback) {
   openDatabase(db, 'users', function(col) {
     find(col, { 'id': id}, function(result) {
       result.filter(function(user) {
         callback(user.age < cutoffAge);
       })
     })
   })
 }
```

#### Promise

事件不是万金油

事件机制最适合处理同一个对象上反复发生的事情——keyup、touchstart 等等。你不需要考虑当绑定监听器之前所发生的事情，当碰到异步请求成功/失败的时候，你想要的通常是这样：
```js
img1.callThisIfLoadedOrWhenLoaded(function() {
  // loaded
}).orIfFailedCallThis(function() {
  // failed
})

// and ..
whenAllTheseHaveLoaded([img1, img2]).callThis(function() {
  // all loaded
}).orIfSomeFailedCallThis(function() {
  // one or more failed
});
```

这就是 Promise。如果 HTML 图片元素有一个 ready() 方法的话，我们就可以这样：
```js
img1.ready().then(function() {
  // loaded
}, function() {
  // failed
})

// and...
Promise.all([img1.ready(), img2.ready()]).then(function() {
  // all loaded
}, function() {
  // one or more failed
})
```

基本上 Promise 还是有点像事件回调，除了：
- 一个 Promise 只能成功或失败一次，并且状态无法改变（不能从成功变为失败，反之亦然）
- 如果一个 Promise 成功或失败之后，你为其添加针对成功/失败的回调，则相应的回调函数会立即执行。

这些特性非常适合处理异步操作的成功/失败情景，你无需担心事件发生的时间点，而只需对其做出响应。

#### Promise 相关术语

`Promise` 是一个对象，它代表了一个异步操作的最终完成或者失败。
本质上，Promise 是一个被某些函数传出的对象，我们附加回调函数（callback）使用它，而不是将回调函数传入那些函数内部。

一个 Promise 的状态可以是：
- 确认（fulfilled）：成功了。
- 否定（rejected）：失败了。
- 等待（pending）：还没有确认活着否定，进行中。
- 结束（settled）：已经确认或者否定了。

#### 实战

- 显示一个加载指示图标
- 加载一篇小说的 JSON，包含小说名和每一章内容的 URL。
- 在页面中填上小说名
- 加载所有章节正文
- 在页面中添加章节正文
- 停止加载指示
...这个过程中如果发生了什么错误要通知用户，并且把加载指示停掉，不然它就会不停转下去。**这个模式是典型的 API 请求模式：获取多个数据，当它们全部完成之后再做一些事件。**

**将 Promise 用于 XMLHttpRequest**
```js
function get(url) {
    // 返回一个 Promise
    return new Promise(function(resolve, reject) {
      const req = new XMLHttpRequest();
      req.open('GET', url);

      req.onload = function() {
        if (req.status === 200) {
          resolve(req.response);
        } else {
          reject(Error(req.statusText));
        }
      }

      // 处理网络错误
      req.onerror = function() {
        reject(Error('Network Error'));
      }

      // 发起请求
      req.send();
    })
  }  

  // 调用
  get('story.json').then(function(response) {
    console.log('Success!', response);
  }, function(error) {
    console.log('Failed! :', error);
  })
```

**链式调用：**

1. 处理值
```js
// 调用
    get('story.json').then(function(response) {
      console.log('Success!', response);
      return JSON.parse(response); // JSON 解析
    }).then(function(response) {
      console.log('Yey JSON!', response);
    })
```
2. 处理异步
```js
function getJSON(url) {
   return get(url).then(JSON.parse);
}
getJSON('story.json').then(function(response) {
      console.log('Success!', response);
      return getJSON(response.chapterUrls[0])
    }).then(function(response) {
      console.log('Got chapter 1', response);
    })
```
**错误处理**
```js
 // 调用
 getJSON('story.json').then(function(response) {
   console.log('Success!', response);
   // return getJSON(response.chapterUrls[01])
   return getJSON(response.chapterUrls[03]) // 测试请求失败
   // 如果请求 story.chapterUrls[0] 失败（http 500 或者用户掉线什么的）了，它会跳过之后所有针对成功的回调，包括 getJSON 中将响应解析为 JSON 的回调，和这里把第一张内容添加到页面里的回调。JavaScript 的执行会进入 catch 回调。
 }).then(function(response) {
   console.log('Got chapter 1', response);
   addHtmlToPage(response.html);
 }).catch(function(err) {
   console.log(err); 
   // 如果只是要捕捉异常做记录输出，不打算在用户界面上对错误进行反馈的话，只要抛出 Error 就行了
   // throw (err);
   addTexdtToPage('Failed to show chapter')
 }).then(function() { // 捕获完错误后，下面的代码继续执行
   document.querySelector('.spinner-border').style.display = "none";
 })
```
**串行请求**
```js
 // 调用
 getJSON('story.json').then(function(response) {
   console.log('Success!', response);
   // 利用 reduce 及 Promise.resolve() 并法队列调用，按照加入的顺序，浏览器按顺序进行请求
   return response.chapterUrls.reduce(function(sequence, chapterUrl) {
     return sequence.then(function() {
       // 获取下一章节
       return getJSON(chapterUrl);
     }).then(function(chapter) {
       addHtmlToPage(chapter.html);
     });
   }, Promise.resolve());
 }).then(function(response) {
     addTextToPage("All done");
   }).catch(function(err) {
     console.log('err', err);
     addTextToPage('Failed to show chapter')
   }).then(function() {
     document.querySelector('.spinner-border').style.display = "none";
   })
```
**并行请求**
```js
// 调用
getJSON('story.json').then(function(response) {
  console.log('Success!', response);
  return Promise.all(response.chapterUrls.map(getJSON));
}).then(function(chapters) {
  chapters.forEach(function(chapter) {
    addHtmlToPage(chapter.html);
  })
  addTextToPage("All done");
}).catch(function(err) {
  console.log('err', err);
  addTextToPage('Failed to show chapter')
}).then(function() {
  document.querySelector('.spinner-border').style.display = "none";
})
```

#### Promise 和 Generator

[Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)，允许函数在特定地方像 return 一样退出，但是稍后又能恢复到这个位置和状态上继续执行。

注意函数名前的星号，这表示该函数是一个 Generator。关键字 yield 标记了暂停/继续的位置
```js
function *addGenerator() {
   let i = 0;
   while(true) {
     i += yield i;
   }
 }

 const adder = addGenerator(); // 生成器对象
 console.log(adder.next().value); // 0
 console.log(adder.next(5).value); // 5
 console.log(adder.next(5).value); // 10
 console.log(adder.next(5).value); // 15
 console.log(adder.next(50).value); // 65
```

这对 Promise 有什么用呢？可以使用这种暂停/继续的机制写出来和同步代码看起来差不多的代码，假设如下使用：
```js
function *main() {
  let ret = yield step1();

  try {
    ret = yield step2(ret);
  } catch(err) {
    ret = yield step2Failed( err )
  }

  ret = yield Promise.all( [
    step3a(ret),
    step3b(ret),
    step3c(ret)
  ])

  yield step4(ret)
}
```

**具体实现**：

#### 手写 Promise

### Async/Await

#### 什么是 Async/Await

Async - 定义异步函数（`async function someName(）{...}`)
- 自动把函数转为 Promise
- 当调用异步函数时，函数返回值会被 resolve 处理
- 异步函数内部可以使用 await 

Await - 暂停异步函数的执行（`var result = await someAsyncCall()`）
- 当使用在 Promise 前面时，`await`等待 Promise 完成，并返回 Promise 的结果。
- `await` 只能和 Promise 一起使用，不能和 callback 一起使用
- `await` 只能用在 `async` 函数中。

#### Async/Await 是否会取代 Promise

不会。

- Async/Await 底层依然使用了 Promise。
- 多个异步函数同时执行时，需要借助 `Promise.all`

每次遇到 await 关键字时，Promise 都会停下在，一直到运行结束。`await` 把异步代码变成了同步代码。
```js
 (async function() {
    try {
      let story = await getJSON('story.json');
      addHtmlToPage(story.heading);

      // 1. 串行请求按顺序执行
      // for (let url of story.chapterUrls) {
      //   let chapter = await getJSON(url); // 返回 promise.resolve 的值
      //   addHtmlToPage(chapter.html);
      // }
      
      // 2. 并行请求
      let chapterPromises = story.chapterUrls.map(getJSON);
      const chapters = await Promise.all(chapterPromises);
      chapters.forEach(chapter => {
        addHtmlToPage(chapter.html);
      })
      addTextToPage('All done');
    } catch (err) {
      addTextToPage('broken' + err.message);
    }
    document.querySelector('.spinner-border').style.display = 'none';
  })();
```

#### 捕获错误

```js
async function asyncAwaitTryCatch() {
  try {
    const api = new Api();
    const user = await api.getUser();
    const friends = await api.getFriend();

    await api.throwError();
    console.log('Error was not thrown');

    const photo = await api.getPhoto(user.id);
    console.log('async/await', { user, friends, photo });
  } catch (err) {
    console.log(err);
  }
}
```

#### 组合

- 调用 async 函数作为一个 promise 对象来返回数据

```js
async function getUserInfo() {
  const api = new Api()
  const user = await api.getUser()
  const friends= await api.getFriends(user.id)
  const photo = await api.getPhoto(user.id)
  return {user, friends, photo }
}

function promiseUserInfo() {
  getUserInfo().then({ user, friends, photo }) => {
    console.log('promiseUserInfo', { user, friends, photo })
  }
}

// 或者继续使用 async/await 语法
async function awaitUserInfo () {
  const { user, friends, photo } = await getUserInfo()
  console.log('awaitUserInfo', { user, friends, photo })
}
```

- 检索前十个用户的所有数据

```js
async function getLotsOfUserData() {
  const users = [];
  while (users.length < 10) {
    users.push(await getUserInfo());
  }
  console.log('getLotsOfUserData', users);
}
```

- 并发请求

```js
async function getLotsOfUserDataFaster() {
  try {
    const userPromises = Array(10).fill(getUserInfo());
    const users = await Promise.all(userPromises);
    console.log('getLotsOfUserDataFaster', users);
  } catch (err) {
    console.log(err);
  }
}
```

### 事件循环和任务队列

JavaScript 时单线程执行的，无法同时执行多段代码。当某一段代码正在执行的时候，所有后续的任务都必须等待，形成一个队列。一旦当前任务执行完毕，再从队列中取出下一个任务，这也常称为“阻塞式执行”。所以一次鼠标点击，或者计时器到达时间点，或是 Ajax 请求完成触发了回调函数，这些事件处理程序或回调函数都不会立即执行，而是立即排队，一旦线程有空闲就执行。

假如当前 JavaScript 线程正在执行一段很耗时的代码，此时发生了一次鼠标点击，那么事件处理程序就被阻塞，用户也无法立即看到反馈，事件处理程序会被放入任务队列，直到前面的代码结束以后才会开始执行。如果代码中设定了一个 `setTimeout`，那么浏览器便会在合适的时间，将代码插入任务队列，如果这个时间设为0，就代表立即插入队列，但不是立即执行，仍然要等待前面代码执行完毕。所以 `setTimeout` 并不能保证执行的时间，是否及时执行取决于 JavaScript 线程是拥挤还是空闲。

由上可知，`setTimeout`并不是异步的，而是将其操作插入到 js 线程中，排队执行，造成异步的假象，这时 setTimeout 立即将 ajax 排到 js 线程中，仍然会造成 ui 阻塞。

**事件循环**
![](../.vuepress/public/images/eventloop.png)

先看一个小栗子：当JS引擎运行时，会将代码压入执行栈进行执行
```js
function a() {
  b()
}
function b() {
  c()
}
function c() {
  console.log('c')
}
a()
```
入栈：当代码被解析后，函数依次被压入栈
![](../.vuepress/public/images/inloop.png)
出栈：当函数c执行完后，开始出栈
![](../.vuepress/public/images/outloop.png)

tip:
- Call Stack 执行栈 **先进后出**
- Callback Queue 回调队列 **先进先出**
- Web Apis 浏览器功能，专门维护事件

**当执行栈遇到异步**
```js
console.log('sync')
$.on('button', 'click', function() {
  console.log('hello')
})
setTimeOut(function() {
  console.log('2')
}, 3000)
```
总过程: 打印 > 添加点击事件 > setTimeout
![](../.vuepress/public/images/inloop2.png)

分析1: 浏览器在执行栈执行时，发现有异步任务后，会交给webapi去维护，而执行栈会继续执行后面的任务
![](../.vuepress/public/images/inloop3.png)

分析2: 同样，setTimeout也会被添加到webapi中，setTimeout旁边有个进度条，这个进度条就是设置的等待时间
![](../.vuepress/public/images/inloop4.png)

分析3: 当setTimeout执行结束后，是不是就回到执行栈呢？答案是否的，倒计时结束后setTimeout的可执行函数，被放入了回调队列，**只有当执行栈的任务为空时**，才会从回调队列中取出放入执行
![](../.vuepress/public/images/inCallbackQueue.png)

![](../.vuepress/public/images/inStack.png)

再看一个栗子：
```js
console.log(1)
setTimeout(() => {
  console.log(2)
  setTimeout(() => {
    console.log(3)
  }, 0)
}, 0)
setTimeout(() => {
  console.log(4)
})
console.log(5)
```
上面代码依次输出：1，5，2，4，3
分析：
1. 输出1，将2push进回调队列
2. 将4push进回调队列
3. 输出5
4. 清空执行栈，读取输出2，将3push进回调队列
5. 清空执行栈，读取输出4
6. 清空执行栈，读取输出3

### Macrotask(宏任务)、Microtask(微任务)
通过上面的例子，想必对 event loop 有了一定的了解，现在继续看一个例子
```js
  console.log(1)
  setTimeout(() => {
    console.log(2)
  })
  const p = new Promise(resolve => {
    console.log(3)
    resolve()
  })
  p.then(() => {
    console.log(4)
  })
  console.log(5)
```
按照 event loop 的概念，输出应该是1，3，5，2，4，因为 setTimeout 和 then 会被放入回调队列，然后先进先出，所以应该是2先输出，4后输出

但实际输出结果是1，3，5，4，2，为什么呢？因为 promise 的 then 方法被认为是在 Microtask 微任务队列中

**什么是Macrotask(宏任务)？**

宏任务就是前面介绍过的回调队列 Callback Queue

**什么是Microtask(微任务)？**

微任务同样也是一个任务队列，只是执行顺序是在清空执行栈之后

![](../.vuepress/public/images/eventloop.png)

从这张图可以看到，Macrotask(微任务)虽然是队列，但**并不是一个个放入执行栈**，而是当执行栈清空，就会执行全部Microtask(微任务)队列中的任务，最后才是取回调队列的第一个Macrotask(宏任务)

**如何分清宏任务还是微任务？**

除了 then 以外，还有几个事件也被记为微任务：
- process.nextTick (node环境下，浏览器环境会变成宏任务)
- Object.observe
- MutationObserver

注意：process.nextTick 和 then 不太一样，process.nextTick 是加入到执行栈底部，所以和其他微任务表现不一致

(node 环境)
```
console.log("1");
setTimeout(()=>{
    console.log(2)
    Promise.resolve().then(()=>{
        console.log(3);
        process.nextTick(function foo() {
            console.log(4);
        });
    })
})
Promise.resolve().then(()=>{
    console.log(5);    
    setTimeout(()=>{
        console.log(6)
    })
    Promise.resolve().then(()=>{
        console.log(7);
    })
})

process.nextTick(function foo() {
    console.log(8);
    process.nextTick(function foo() {
        console.log(9);
    });
});
console.log("10")

```

1. 输出1，将2push进回调队列
2. 将5push进微任务队列
3. 将8压入执行栈底部
4. 输出10
5. 输出8，将9压入执行栈底部
6. 输出9
7. 输出5，将6压入回调队列，将7push进微任务
8. 输出7
9. 输出2，将3push进微任务队列，将4压入执行栈底部
10. 输出3
11. 输出4
12. 输出6



### 闭包

#### 变量的作用域

要理解闭包，首先必须理解 JavaScript 特殊的变量作用域。

变量的作用域无非就是两种：全局变量和局部变量。

JavaScript 语言的特殊之处，就在于函数内部可以直接读取全局变量。
```js
var n = 999;
function f1() {
  alert(n);
}
f1(); // 999
```
另一方面，在函数外部无法读取函数内的局部变量
```js
function f2() {
  var m = 888;  // 这里要注意，函数内部声明变量的时候，一定要用 var 命令。如果不用的话，你实际上声明了一个全局变量。
}
alert(m); // error
```

#### 如何从外部读取局部变量

出于种种原因，我们有时候需要得到函数内的局部变量。但是，前面已经说过了，正常情况下，这是办不到的，只有通过变通方法才能实现。

那就是在函数的内部，再定义一个函数。
```js
function f1() {
  var n = 999;
  
  function f2() {
    alert(n);
  }
}
```
在上面的代码中，函数f2就被包括在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。但是反过来就不行，f2内部的局部变量，对f1就是不可见的。这就是 JavaScript 语言特有的“链式作用域”结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。

既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！
```js
function f1() {
  var n = 999;
  // 函数f2就被包括在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。
  // 但是反过来就不行，f2内部的局部变量，对f1就是不可见的
  function f2() {
    alert(n);
  }
  // 既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！
  return f2;
}
var result = f1();
result(); // 999

```

#### 闭包的概念

上一节代码中的 f2 函数，就是闭包。<u>闭包就是能够读取其他函数内部变量的函数。</u>由于在 JavaScript 语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

#### 闭包的用途

闭包可以用在许多地方。它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。（高阶函数）
```js
function f1() {
    var n = 999;
    nAdd = function() { n += 1}
    function f2() {
      alert(n);
    }
    return f2;
  }
  var result = f1();
  result(); // 999
  nAdd();
  result(); // 1000
```

在这段代码中，result 实际上就是闭包 f2 函数，它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数 f1 中的局部变量一直保存在内存中，并没有在 f1 调用后被自动清除。

为什么回这样呢？<u>原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致 f2 始终在内存中，而 f2 的存在依赖于f1，因此f2也始终内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。</u>

这段呢代码中另一个值得注意的地方，就是`nAdd=function() { n + 1}` 这一行，首先在 nAdd 前面没有使用 var 关键字，因此 nAdd 是一个全局变量，而不是局部变量。其次，nAdd 的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以 nAdd 相当于是一个 setter，可以在函数外部对函数内部的局部变量进行操作。


#### 使用闭包的注意点

1. 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
2. 闭包回在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

如立即执行函数 + 闭包，实现模块的基本方法。
```js
const module = (function() {
  const privateName = "inner"; // 私有变量
  const privateFunc = function() {
    console.log("私有函数")  // 私有函数
  }
  return {
    name: "Jecyu", // 公有属性
    sayName: function() { // 公有函数
      console.log(this.name);
    }
  }
})()
```

#### 闭包引起内存泄漏

### JS 前端模块化

#### 抛出问题

- 在开发中在导入模块时经常使用 `require` 和 `import`；
- 导出模块时使用 `module.exports/exports` 或者 `export/export default`;
- 有时候为了引用一个模块会使用 `require` 奇怪的是也可以使用`import`？它们之间有何区别？

#### 追溯根源，来到Js模块化规范

随着网站逐渐互联网应用程序，嵌入网页的 JavaScript 代码越来越判断
网页越来越像桌面程序，需要一个团队分工协作、进度管理、单元测试等等......开发者不得不使用软件工程的方法，管理网页的业务逻辑。

JavaScript 模块化编程，已经成为一个迫切的需求。理想情况下，<u>开发者只需要实现核心的业务逻辑，其他都可以加载别人已经写好的业务逻辑。</u>

前端模块化，主要解决两个问题——“命名空间冲突”，“文件依赖管理”。

1. **坑——命名空间冲突**
    - 我自己测试好的代码和大家合并后怎么起冲突了？
    - 页面脚本的变量或函数覆盖了公有脚本的。
2. **坑——文件依赖管理**
    - 明明项目需要引入的包都引进来了怎么还报缺少包？
    - 手动管理依赖，有天要更换某个插件，要深入代码内部进行修改。
    - 如下，在 F.js 中，分不清某个变量是来自 C.js，还是 E.js 。
        ```html
          <script src="A.js"></script>
          <script src="B.js"></script>
          <script src="C.js"></script>
          <script src="D.js"></script>
          <script src="E.js"></script>
          <script src="F.js"></script>
        ```
   - 两次加载同一个模块。比如引入了两遍 JQ。
3. **其他的坑**
   - 为了实现脚本复用，将一个很大的公用 `public` 文件引入各个页面中，其中的某些函数，只有个别页面用到。（资源浪费）
   - 某个功能的函数群函数，与另一个功能的函数群摆在一起，使用注释来分隔。

4. **目前解决的方法是：模块化**
   - 命名空间：各个模块的命名空间独立。A 模块的变量x不会覆盖B模块的变量x。
   - 模块的依赖关系：通过模块管理工具如 webpack/require/browserify 等管理。

#### 原始写法

模块就是实现特定功能的一组方法。
只要把不同的函数（以及记录状态的变量）简单放在一起，就算是一个模块。
```js
function m1() {
  //...
}
function m2() {
  //...
}
```
上面的函数 m1() 和 m2()，组成一个模块。使用的时候，直接调用就好了。
这种做法的缺点很明显：“污染”了全局变量，无法保证不与其他模块发生变量名冲突，而且模块成员之间看不出直接关系，这样子使得每想命名一个变量的时候都要三思。

#### 使用命名空间——对象写法

```js
const module1 = {
  _count: 0,
  m1: function() { 
    //...
  },
  m2: function() {
    //...
  }
}
```
上面的函数 m1() 和 m2()，都封装在 module1 对象里。使用的时候，就是调用这个对象的属性。
```js
module1.m1();
```
但是，这样的写法会暴露所有模块成员，内部状态可以被外部改写。比如，外部代码可以直接改变内部计数器的值。
```js
module1._count = 5;
```

#### 立即执行函数 + 闭包（实现模块的基本方法）

立即函数可以创建作用域，闭包则可以形成私有变量和函数。
```js
// 创建
const module = (function() {
  const privateName = "inner"; // 私有变量
  const privateFunc = function() {
    console.log("私有函数")  // 私有函数
  }
  return {
    name: "Jecyu", // 公有属性
    sayName: function() { // 公有函数
      console.log(this.name);
    }
  }
})()
// 使用
module.sayName(); // Jecyu
```
总结：这是目前比较常用的模块定义方式，可以区分私有成员和公有成员。公有变量和方法，和之前一样可以直接通过 `module.name` 的方式修改。私有变量和方法，是无法访问的，除非给你个修改私有成员的公有方法。

#### 放大模式

如果一个模块很大，必须分成几个部分，或者一个模块需要继承另一个模块，这时就有必要采用“放大模式”。
```js
const module1 = (function(mod) {
   const privateName = "inner1"; // 私有变量
  const privateFunc = function() {
    console.log("私有函数1")  // 私有函数
  }
  return {
    name: "Jecyu1", // 公有属性
    sayName: function() { // 公有函数
      console.log(this.name);
    },
    anotherName: mod.name, // 另一个模块上的公用参数
    sayAnotherName: mod.sayname // 另一个模块上的公有方法
  }
})(anotherModule) // 引入了另一个模块
// 使用
module1.sayOtherName
```
除了上面这种外，在浏览器环境中，模块的各个部分通常都是从网上获取的，有时无法知道哪个部分会先加载。一次，避免第一个执行的部分有可能加载一个不存在空对象，这时就采用“宽放大模式”。
```js
const module1 = (function()(mod) {
  // ...
}(window.anotherModule || {}))
```

可以看 Vue.js 的文件即是一个立即执行函数 + 闭包的模式，[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)。

#### 告别刀耕火种的时代——模块化构建工具（解决依赖管理）

js 模块是从立即执行函数开始的，但是等到真正使用构建工具的时候，业界采用的模块化方案，却并非是一个一个由立即函数+闭包形成的集群。而是采用了诸如 AMD/CMD/CommonJS/ES6等模块化实现。

这里面的原因可能有这几个：
- 闭包的性能问题。（使用闭包十分容易导致浏览器的内存泄漏，严重情况下会是浏览器挂死。）
- 当模块增多的时候，需要解决模块间依赖管理问题。关于依赖管理，如下：
    ```html
      <script src="A.js"></script>
      <script src="B.js"></script>
      <script src="C.js"></script>
      <script src="D.js"></script>
      <script src="E.js"></script>
      <script src="F.js"></script>
    ```
    HTML 中引入了两遍的 JQ，导致脚本报错。有个公用脚本，包含了 N 多的公用模块。有些页面明明只用到了一个模块，也必须全部加载一遍。
综上，必须使用模块化管理工具。

**几个概念：**

ES6规范/webpack/rollup 模块化处理
| 包管理工具 | 模块化构建工具 |模块化规范|
| --- | --- | --- |
|npm|webpack|es6模块 |
|bower（被干掉了）|requireJS/seaJS/browserify（正在衰落）|AMD/CMD/CommonJS|

- **包管理工具**：安装、卸载、更新、查看、搜索、发布包。比如你需要按照个 jq 等，通过 npm 来安装。npm 里有依赖管理，假如 jq 或者说 express 升级了，原来代码不能用了，帮助你解决这个问题的就是 npm。
- **模块化构建工具**：webpack/requireJS/seaJS，等是用来组织前端模块的构建工具（加载器）。通过使用模块化规范（AMD/CMD/CommonJS/es6）的语法来实现<u>按需加载</u>。举个例子，如果有有一天你不用维护一个很长很长的公用脚本文件，这得感谢它。
- **模块化规范**：AMD/CMD/CommomJS/es6模块等规范，规范了如何来组织你的代码。一般这种方式写的代码浏览器不认，需要用模块化构建工具来打包编译成浏览器可以识别的文件。

**npm与bower比较**
- 原来 bower 的使用优势就是适合前端模块管理，而 npm 被视为只适合后端的管理。但是随着 webpack的流行，这个已经 npm 胜出了。npm + webpack，可以实现良好的前端模块管理。

**webpack 与 requireJS 比较**：
|构建工具|支持的规范|特点|
|---|---|---|
|webpack|AMD/CMD/CommonJS/es6模块|功能强大，一统江湖|
|requireJS|AMD|每个页面必须引入 require.js，移动端不友好|
|seaJS|CMD|与 requireJS 类似，推崇依赖后置。社区衰微，不建议入|
|browserify|CommonJS|仅适合后端|

**几种模块化规范比较：**
|模块化规范|怎样加载模块|缺点|场景|
|---|---|---|---|
|es6 模块|依赖在编译时完成的（最佳）|目前对 es6 支持还不够，使用时需要用 babel 等转义|未来前端模块管理的规范|
|AMD|异步加载模块，依赖前置（依赖必须一开始写好）|写法冗余，依赖多的时候，很痛苦。|前端残留势力|
|CMD|异步假装模块，依赖后置（依赖可以就近书写）|与 AMD 类似|被创始人说已死的规范|
|CommonJS|同步加载|仅适合后端，浏览器端同步假装页面会阻塞的|Node.js 默认支持，而且不完全按 CommonJS 的标准来实现|

**CommonJS 规范（同步加载模块）**
- 允许模块通过 require 方法来同步加载所要依赖的其他模块，然后通过 exports 或 module.exports 来导出需要暴露的接口。
- 使用方式
    ```js
      // 导入
      require("module");
      require("../app.j");
      // 导出
      export.getStoreInfo = function() {};
      module.exports = someValue;
    ```
- 优点：
  - 简单容易使用
  - 服务端模块便于复用

**AMD（异步加载模块）**
- 采用异步方式加载模块，模块的加载不影响后面语句的运行。所有依赖模块的语句，都定义在一个回调函数中，等到加载完成之后，回调函数才执行。
    ```js
      // 定义
      define("module", ["dep1", "dep2"], function(d1, d2) {...});
      // 加载模块
      require["moduel", ".../app]", function(module, app) {...});
    ```
- 加载模块 `require([module], callback)`；第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数 callback 是加载成功之后的回调函数。
- Require.js 执行过程：
  1. require 函数检查依赖的模块，根据配置文件，获取 js 问及爱你的实际路径。
  2. 根据 js 文件实际路径，在 dom 中插入 script 节点，并绑定呢 onload 事件来获取该模块加载完成的通知。
  3. 依赖 script 全部加载完成后，调用回调函数。
  通过上面的步骤后，就真正解决的之前的文件依赖管理问题。

**UMD**
- UMD 是 AMD 和 CommonJS 的糅合。
- AMD 以浏览器第一原则发展异步加载模块。
- CommonJS 模块以服务器第一原则发展，选择同步假装，它的模块无需包装。
- UMD 先判断是否支持 Node.js 的模块（exports）是否存在，存在则使用 Node.js 模块模式；在判断是否支持 AMD（define是否存在），存在则使用 AMD 方式加载模块。
```js
(function (window, factory) {
  if (typeof exports === 'object') {
    module.exports = factory();
  } else if (typeof defiine === 'function' && define.amd) {
    define(factory);
  } else {
    window.eventUtil = factory();
  }
})(this, function() {
  // module...
});
```
像 vue.js 文件
```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = global || self, global.Vue = factory());
}(this, function (){});
```

**ES6模块化**
- ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。
- ES6 模块设计思想：尽量的静态化、使得编译时就能确定模块的依赖关系，以及输入和输出的变量。？（CommonJS 和 AMD 模块，都只能在运行时确定这些东西。）
- 使用方式
    ```js
      // 导入
      import "/app";
      import React from "react";
      // 导出
      export function multiply() {...};
      export const year = "2018";
    ```
- 优点：
  - 容易进行静态分析。
  - 面向未来的 ECMAScript 标准
- 缺点：
  - 原生浏览器端还没有实现该标准。
  - 全新的命令字，新版的 Node.js 才支持。

综上结论，就是使用搭配：**npm + webpack + es6 模块。** 

#### 回到问题“require 与 import 的区别“

- require 使用于 CommonJS 规范，import 使用于 ES6 模块规范，两种的区别实质是两种规范的区别。
- CommonJS
  - 对于基本数据类型，属于复制。即会被模块缓存；同时，在另一个模块可以对该模块输出的变量重新赋值。
  - 对于复杂数据类型，属于浅拷贝。由于两个模块引用的对象指向同一个内存空难功能键你，因此对该模块的值做修改时会影响另一个模块。
  - 当使用 require 命令加载某个模块时，就会运行整个模块的代码。
  - 当使用 require 命令加载同一个模块时，不会再执行该模块，而是取到缓存之中的值。也就是说，CommonJS 模块无论加载多少次，都只会再第一次加载时运行一次，以后再加载，就会烦第一次运行的结果，除非手动清除系统缓存。
  - 循环加载时，属于加载时执行。即脚本代码在 require 的时候，就会全部执行。一旦出现某个模块被“循环加载”，就只输出已经执行的部分，还未执行的部分不会输出。
- ES6 模块
  - ES6 模块中的值属于【动态只读引用】。
  - 对于只读来说，即不允许修改引入变量的值，import 的变量是只读的，无论是基本数据类型还是复杂数据类型。当模块遇到 import 命令时，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里去取值。
  - 循环加载时，ES6模块是动态引用。只要两个模块之间存在某个引用，代码就能够执行。
  - 最后：require/exports 是必要通用且必须的；因为事实上，目前你编写的 `import/export` 最终都是编译为 `require/exports` 来执行的。

**webpack/rollup 最终打包后的文件里，是如何解决依赖关系的，还会使用到闭包么？**

webpack 的打包成一个文件，也可以打包为多个文件，它跟 require.js 的实现有什么不同的？

#### npm + webpack + es6

**使用 es6 模块语法**

webpack 可以支持 es6 模块语法。我们需要先下载和配置 babel。
下载
```bash
yarn add babel-cli babel-core babel-eslin babel-loader babel-plugin-add-module-exports babel-plugin-transform-es2015-modules-umd babel-preset-env --dev
```
配置
```js
const config = {
  mode: "production",
  entry: __dirname + '/src/js/index.js',
  devtool: 'source-map',
  output: {
    path: __dirname + '/examples', // 打包文件的输出目录
    filename: outputFile,
    library: libraryName,
    libraryTarget: 'umd', // umd
    umdNamedDefine: true
  },
  module: {
    rules: [
      {
        test: /(\.jsx|\.js)$/,
        loader: 'babel-loader',
        exclude: /(node_modules)|bower_components/
      }
    ]
  }
}
```
之后就可以使用 `import/export` 语法了，最终使用 webpack 打包后的文件。
sum.js
```js
export default function(a, b) {
  return a + b;
}
```
app.js
```js
/**
 * webpack支持ES6、CommonJs和AMD规范
 */

// ES6
import sum from "./vendor/sum";
console.log("sum(1, 2) = ", sum(1, 2));

// CommonJs
var minus = require("./vendor/minus");
console.log("minus(1, 2) = ", minus(1, 2));

// AMD
require(["./vendor/multi"], function(multi) {
  console.log("multi(1, 2) = ", multi(1, 2));
});

```

## 工具函数大全

### 类型判断

#### 判断字符串类型是否为数字
```js
const isNumberStr = function (str) {
  return !isNaN(Number(str));
}
```

### 数组

> 参考实现：lodash https://lodash.com/docs/4.17.15#intersection

#### 求交集和并集

```js
let intersection = a.filter(v => b.includes(v));
let difference = a.concat(b).filter(v => !a.includes(v) || !b.includes(v));
```

#### 用 apply 将数组添加到另一个数组

```js
const array = ['a', 'b'];
const elements = [0, 1, 2];
array.push.apply(array, elements);
console.info(array); // ['a', 'b', 0, 1, 2]
```

### 浏览器操作及其它

#### 检查是否位浏览器环境

此代码段可用于确定当前运行环境是否为浏览器。这有助于避免在服务器（节点）上运行前端模块时出错。

```js
const isBrowser = () => ![typeof window, typeof document].includes('undefined');
isBrowser(); // true(browser)
isBrowser(); // fasle (Node)
```

#### 返回指定函数的生效样式

```js
const getStyle = (el, ruleName) => getComputedStyle(el)[ruleName];
getStyle(document.querySelector('p'), 'font-size');
```

#### smoothScroll: 滚动到指定元素区域

该代码块可将指定元素滚动到浏览器窗口的可见区域。

```js
const smoothScroll = element => {
  document.querySeletor(element).scrollIntoView({
    behavior: 'smooth'
  });
};
smoothScroll('#fooBar');
smoothScroll('.fooBar');
```

### 深拷贝

### 字符串拼接

```js
[1, 2, 3, 4, 5].join();
```

### JS生成某个范围的随机整数

> js没有提供一个现成的函数直接生成某个范围的随机数。
js只有一个Math.random() 函数返回一个浮点, 伪随机数在范围[0，1)。

我们只有利用Math.random() 函数，自己封装一些函数，实现生成某个范围的随机数。
实现生成某个范围色随机数也需要与一下函数配合使用：
- Math.ceil() 向上取整
- Math.floor() 向下取整
- Math.round() 四舍五入

#### 以 0 ～ 10 为例理解生成某个范围的随机数

首先我们以0～10为例，对生成某一个返回有一个简单的理解：
Math.random() * 10 会随机生成[0, 10]，但是是浮点数；

生成[0,10]的随机整数，Math.round(Math.random()*10)，通过四舍五入可以将大于9.5的数值转换为10；
生成[0,10)的随机整数，Math.floor(Math.random() * 10); // 向下取整数
生成(0,10]的随机整数，Math.ceil(Math.random() * 10); // 向上取整数

#### 生成随机整数的四种情况

1. min≤r≤max
```js
function Random(min, max) {
  return Math.round(Math.random() * (max - min)) + min;
}
```

### 内存管理

## 参考资料

- [如何写出一个惊艳面试官的深拷贝?](https://juejin.im/post/5d6aa4f96fb9a06b112ad5b1?utm_source=gold_browser_extension#heading-13)
- [从多线程到 Event Loop 全面梳理](https://juejin.im/post/5d5b4c2df265da03dd3d73e5#heading-15) —— 少有的从计算机方面讲解逐步引申到浏览器的线程文章。
- [内存管理速成教程](https://mp.weixin.qq.com/s/sVcGRUZqILCVgfhzRyODTg) —— 漫画式讲解 JS 内存管理。
- [JavaScript 工具函数大全（新）](https://juejin.im/post/5da1a04ae51d45783d6122bf?utm_source=gold_browser_extension#heading-36)
- [this, appy, call, bind](https://juejin.im/post/59bfe84351882531b730bac2#comment) —— 作者一步步讲解，浅显易懂。
- [Ajax 知识体系大梳理](https://juejin.im/post/58c883ecb123db005311861a#heading-61)—— 这是一篇万字长文, 系统梳理了ajax相关的知识体系, 几乎囊括了所有ajax的知识点.
- [Jquery ajax 同步阻塞引起的UI线程阻塞的坑（loading图片显示不出来，layer.load延迟）](https://blog.csdn.net/lianzhang861/article/details/79426385) -- ajax 设置为同步请求时的分析。
- [前端模块化——技术选型](https://segmentfault.com/a/1190000006966358#articleHeader2) -- 说明模块化要解决的问题以及使用模块化构建工具解决依赖管理。
- [Javascript 的匿名函数与自执行](https://juejin.im/entry/57fee360a22b9d005b1d9ae3) -- 匿名函数与闭包。
- [npm + webpack + es6 初体验](https://segmentfault.com/a/1190000006968235) -- 前端工程化是大势所趋，我们将不再人工去实现 依赖管理，代码压缩混淆，测试，上线等开发流程，转而交由工具去完成
- [Javascript模块化编程（一）：模块的写法](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html) -- 简洁、清晰、透彻
- [谈谈Js前端模块化规范](https://segmentfault.com/a/1190000015991869#articleHeader0) -- 详细的 JS 模块化规范对比。
- [npm + webpack + es6 初体验](npm + webpack + es6 初体验)
- [JavaScript Promise：去而复返](https://www.cnblogs.com/rubylouvre/p/3495286.html) -- 目前看过最好的 Promise 文章，讲解从为什么到怎么做。s