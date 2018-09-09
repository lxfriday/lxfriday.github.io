---
title: vue中的双向绑定
comments: false
date: 2018-09-08 10:23:18
categories: vue
tags: [vue双向数据绑定,模拟实现]
---

本文主要介绍两大内容

1. vue 数据双向绑定的原理
1. 实现简单版 vue 的过程，主要实现 ` { { } } `、v-model 和事件指令的功能

[多版本github仓库地址](https://github.com/lxfriday/vue-data-bind)

vue 数据双向绑定是通过数据劫持结合发布-订阅模式的方式来实现的， 数据劫持通过 `Object.defineProperty()` 来实现。

`set` 给属性赋值的时候调用， `get` 就是在获取属性的值的时候调用的

本仓库中各文件的作用

- index.html html
- index.js Vue 的处理核心
- observer.js 数据劫持
- watcher.js 订阅-发布
- compile.js html 和 数据、事件属性的绑定

### [v1 版本](https://github.com/lxfriday/vue-data-bind/tree/master/v1)
最基本的处理
- SelfVue 先做比较简单的处理，直接传入 element、data、属性名
- observer 中实现对数据的拦截
- watcher 实现变动监听
- 没有 compile 实现

```html
<!--html-->
<div id="app"></div>
<script src="./observer.js"></script>
<script src="./watcher.js"></script>
<script src="./index.js"></script>
<script>
var selfVue = new SelfVue(
  // root
  document.querySelector('#app'),
  // data
  {
    name: 'lxfriday',
  },
  'name', // exp
);
setTimeout(() => {
  selfVue.name = '止水、';
}, 1000);
</script>
```

```js
// observer.js
// 对传入进来的对象进行属性扫描，对属性的描述符进行修改，添加监听器
function Observer(data) {
  this.data = data;
  this.walk(data);
}

Observer.prototype = {
  walk(data) {
    var self = this;
    Object.keys(data).forEach(key => self.defineReactive(data, key, data[key]));
  },
  // 定义了属性的 set 和 get，在获取或者设定值得时候会触发相应的通知操作
  defineReactive(data, key, val) {
    var dep = new Dep();
    var childObj = observe(val);
    Object.defineProperty(data, key, {
      enumerable: true,
      configurable: true,
      set(newVal) {
        // 前后两次的值是相同的则不往后面执行
        if (val === newVal) {
          return;
        }
        val = newVal;
        console.log('属性 ' + key + ' 已经被监听了，现在值为：' + newVal);
        // 在设置属性的时候，前后的值不同会触发事件通知
        dep.notify();
      },
      get() {
        if (Dep.target) {
          // 如果确定需要监听该目标，则在获取该目标属性的时候
          // 会将该属性添加到事件发布的队列里面去
          dep.addSub(Dep.target);
        }
        return val;
      },
    });
  },
};

// 调用此函数，实现所有的属性观察
// 在属性get的时候，添加观察期，在属性set的时候，进行变化通知
function observe(value, vm) {
  // 设定添加监听的终止条件
  if (!value || typeof value !== 'object') {
    return;
  }
  return new Observer(value);
}

// 队列，将所有要进行的通知全部进行缓存，等待属性变化的时候，进行通知，从而触发View层的变化
function Dep() {
  this.subs = [];
}

Dep.prototype = {
  constructor: Dep,
  // 添加广播器
  addSub(sub) {
    this.subs.push(sub);
  },
  // 通知属性发生了变化
  notify() {
    this.subs.forEach(sub => sub.update());
  },
};

Dep.target = null;
```

```js
// watcher.js
// 属性变化之后，执行cb回调，从而实现 Model -> View 层的同步
function Watcher(vm, exp, cb) {
  this.cb = cb;
  this.vm = vm;
  this.exp = exp;
  this.value = this.get(); // 将自己添加到订阅器的操作
}

Watcher.prototype = {
  constructor: Watcher,
  update() {
    this.run();
  },
  // 监听到属性发生了变化
  // 以新的value作为参数，调用实例化的时候赋予的回调
  run() {
    var value = this.vm.data[this.exp];
    var oldVal = this.value;
    if (value !== oldVal) {
      this.value = value;
      this.cb.call(this.vm, value, oldVal);
    }
  },
  // 调用 get，会将该watcher发到队里里面
  get() {
    // 缓存自己
    Dep.target = this;
    // 强制执行监听器里面的函数，get的时候回自动将该 Dep.target 添加到队列数组中
    var value = this.vm.data[this.exp];
    Dep.target = null;
    return value;
  },
};

```

```js
// index.js
function SelfVue(ele, data, exp) {
  var self = this;
  this.data = data;
  Object.keys(this.data).forEach(key => self.proxyKey(key));
  observe(this.data);
  ele.innerHTML = this.data[exp];
  new Watcher(this, exp, function (value) {
    ele.innerHTML = value;
  });
  return this;
}

SelfVue.prototype = {
  constructor: SelfVue,
  proxyKey(key) {
    var self = this;
    Object.defineProperty(self, key, {
      enumerable: false,
      configurable: true,
      set(v) {
        self.data[key] = v;
      },
      get() {
        return self.data[key];
      },
    });
  },
};
```

这一版的 watcher 和 observer 和后面两版是一致的，该版本没有 Vue 的参数类似的模式，是直接将 Dom 对象、数据和属性名传递进去，调用 observer 对所有的数据进行观察，同时添加了对该元素的watcher。

### [v2 版本](https://github.com/lxfriday/vue-data-bind/tree/master/v2)
- 优化 SelfVue，设计成 Vue 的模式
- 添加 Compile，实现字段和视图的绑定，不修改元素属性

```html
<!--html-->
<div id="app">
    <h1>{{title}}</h1>
    <h2>{{name}}</h2>
  </div>
  <script src="./observer.js"></script>
  <script src="./watcher.js"></script>
  <script src="./compile.js"></script>
  <script src="./index.js"></script>
  <script>
    var selfVue = new SelfVue({
      el: '#app',
      data: {
        title: 'hello !',
        name: 'lxfriday',
      },
    });
    // 过1s之后修改值
    setTimeout(() => {
      selfVue.title = '你好';
    }, 1000);
    setTimeout(() => {
      selfVue.name = '止水、';
    }, 1000);
  </script>
```

```js
// compile.js
function Compile(el, vm) {
  this.el = document.querySelector(el);
  this.vm = vm;
  this.fragment = null; // 用来装载编译后的 node
  this.init();
}

Compile.prototype = {
  constructor: Compile,
  // 初始化方法
  init() {
    this.fragment = this.nodeToFragment(this.el);
    this.compileElement(this.fragment);
    this.el.appendChild(this.fragment);
  },
  // 将 dom 结点变成 node
  nodeToFragment(el) {
    const fragment = document.createDocumentFragment();
    let child = el.firstChild;
    while(child) {
      fragment.appendChild(child);
      child = el.firstChild;
    }
    return fragment;
  },
  // 对结点进行属性绑定
  compileElement(fragment) {
    const self = this;
    const childNodes = fragment.childNodes;
    const reg = /\{\{(.*)\}\}/;
    [].forEach.call(childNodes, function (node) {
      const text = node.textContent;
      // nodeType === 3
      if (self.isTextNode(node) && reg.test(text)) {
        self.compileText(node, reg.exec(text)[1]);
      }
      // 递归循环，把所有的结点全部循环完
      if (node.childNodes && node.childNodes.length) {
        self.compileElement(node);
      }
    });
  },
  compileText(node, exp) {
    const self = this;
    self.updateText(node, self.vm.data[exp]);
    new Watcher(self.vm, exp, function (value) {
      self.updateText(node, value);
    });
  },
  updateText(node, value) {
    node.textContent = typeof value === 'undefined'? '': value;
  },
  isTextNode(node) {
    return node.nodeType === 3;
  },
};
```

```js
// index.js
function SelfVue(options) {
  var self = this;
  this.vm = this;
  this.data = options.data;
  // data 属性代理
  Object.keys(this.data).forEach(key => self.proxyKey(key));
  // 添加数据拦截器
  observe(this.data);
  // 实现属性数据和模板的绑定
  new Compile(options.el, this.vm);

  return this;
}

SelfVue.prototype = {
  constructor: SelfVue,
  // 添加 this 的属性代理
  proxyKey(key) {
    var self = this;
    Object.defineProperty(self, key, {
      enumerable: false,
      configurable: true,
      set(v) {
        self.data[key] = v;
      },
      get() {
        return self.data[key];
      },
    });
  },
};

```

这个版本实现了参数输入方法和 vue 完全相同的模式，同时将属性和对应模板上的值对应起来，实现完全绑定

### [v3 版本](https://github.com/lxfriday/vue-data-bind/tree/master/v3)
- Compiler 添加指令(v-on、v-model)

```html
<!--html-->
<div id="app">
    <h1 v-on:click="getContent">{{title}}</h1>
    <h2 v-on:click="getContent">{{name}}</h2>
    <h3 v-on:click="getContent">{{age}}</h3>
    <b>message:</b> <input type="text" v-model="message">
    <br />
    <b>message2:</b> <input type="text" class="message2">
  </div>
  <script src="./observer.js"></script>
  <script src="./watcher.js"></script>
  <script src="./compile.js"></script>
  <script src="./index.js"></script>
  <script>
    var selfVue = new SelfVue({
      el: '#app',
      data: {
        title: 'hello world',
        name: 'lxfriday',
        age: 0,
        message: '',
      },
      // 自己设定的方法
      methods: {
        getContent(event) {
          // 绑定 this
          // event.preventDefault();
          event.stopPropagation();
          console.log(event);
        },
      },
      // 声明周期函数
      beforeCreate() {
        console.log('before create');
      },
      created() {
        console.log('created');
        setTimeout(() => {
          this.title = '你好';
        }, 1000);
        setTimeout(() => {
          this.name = '止水、';
        }, 1000);
        setTimeout(() => {
          this.age = 23;
        }, 1000);
      },
      beforeMount() {
        console.log('beforeMount');
      },
      mounted() {
        console.log('mounted');
      },
    });
  </script>
```

```js
// compile.js
function Compile(el, vm, lifecycle) {
  this.vm = vm;
  this.lifecycle = lifecycle; // beforeMount 和 mounted 生命周期函数的挂载点
  this.el = document.querySelector(el);
  this.fragment = null;
  this.init();
}

Compile.prototype = {
  constructor: Compile,
  init() {
    if (this.el) {
      // 变成内存节点
      var {
        beforeMount,
        mounted,
      } = this.lifecycle;
      beforeMount && beforeMount.call(this.vm);
      this.fragment = this.nodeToFragment(this.el);
      // 实现结点的数据绑定
      this.compileElement(this.fragment);
      // 将 el 的子节点全部虚拟化绑定之后，再重新放置到 el 中，显示效果原样不变
      this.el.appendChild(this.fragment);

      // mounted 组件挂载好了
      mounted && mounted.call(this.vm);
    } else {
      console.log('DOM 元素不存在');
    }
  },
  // dom 结点变成内存结点
  nodeToFragment(el) {
    var fragment = document.createDocumentFragment();
    var child = el.firstChild;
    while(child) {
      // 将dom元素移到 fragment 中
      fragment.appendChild(child);
      child = el.firstChild;
    }
    return fragment;
  },
  compileElement(el) {
    var self = this;
    var childNodes = el.childNodes;
    // 循环遍历所有结点及其子节点
    [].slice.call(childNodes).forEach(node => {
      var reg = /\{\{(.*)\}\}/;
      var text = node.textContent;
      // 是否是元素结点 nodeType === 1
      if (self.isElementNode(node)) {
        self.compile(node);
      } else if (self.isTextNode(node) && reg.test(text)) {
      // 是文本结点，而且内容符合 {{}} 规则
        // 判断是否是符合 {{}} 的指令
        self.compileText(node, reg.exec(text)[1]);
      }
      if (node.childNodes && node.childNodes.length) {
        // 会遍历到最底层
        self.compileElement(node);
      }
    });
  },
  compile(node) {
    var self = this;
    var nodeAttrs = node.attributes;

    // 对每个结点的每个属性都做一次遍历，筛选出要处理的指令
    Array.prototype.forEach.call(nodeAttrs, function(attr) {
      var attrName = attr.name;
      if (self.isDirective(attrName)) {
        var exp = attr.value;
        var dir = attrName.substring(2);
        if (self.isEventDirective(dir)) {
          // event 命令
          self.compileEvent(node, self.vm, exp, dir);
        } else {
          // v-model 命令
          self.compileModel(node, self.vm, exp, dir);
        }
        // 移除属性名
        node.removeAttribute(attrName);
      }
    });
  },
  // 编译 event
  compileEvent(node, vm, exp, dir) {
    var eventType = dir.split(':')[1];
    var cb = vm.methods && vm.methods[exp];
    if (eventType && cb) {
      node.addEventListener(eventType, cb.bind(vm), false);
    }
  },
  // 编译 model
  compileModel(node, vm, exp, dir) {
    var self = this;
    var val = self.vm[exp];
    this.modelUpdater(node, self.vm[exp]);

    // -------------------------------
    // Model -> View
    // 对 model 绑定的属性添加监听
    // 当 self.vm[exp] 的值更改的时候，会执行后面的回调函数，从而换新视图
    new Watcher(self.vm, exp, function (value) {
      self.modelUpdater(node, value);
    });

    // --------------------------------
    // View -> Model View层触发Model层的数据改变
    // 给结点添加输入的时候时间监听绑定
    node.addEventListener('input', function (event) {
      var newValue = event.target.value;
      if (val === newValue) {
        return;
      }
      self.vm[exp] = newValue;
      val = newValue;
    });
  },
  // 将属性以及拥有该属性的结点进行数据绑定
  // 每次调用该方法的时候，都会对属性和 node 结点进行绑定，以实现数据更新，视图更新
  compileText(node, exp) {
    var self = this;
    var initText = this.vm[exp]; // 获取到该属性的值
    this.updateText(node, initText); // 将初始化的数据初始化到视图中
    // 生成订阅器并绑定新函数
    // 如果 exp 属性变化了，则会执行下面的回调函数，用以更新属性值
    new Watcher(this.vm, exp, function(value) {
      self.updateText(node, value);
    });
  },
  // 将 value 值更新到结点的属性上
  updateText(node, value) {
    node.textContent = typeof value === 'undefined'? '': value;
  },
  // 给结点的 value 赋值
  modelUpdater(node, value, oldValue) {
    node.value = typeof value === 'undefined'? '': value;
  },
  // 是不是指令？以 v- 开头
  isDirective(attr) {
    return attr.indexOf('v-') === 0;
  },
  // 是否是时间指令？以 on: 开头
  isEventDirective(dir) {
    return dir.indexOf('on:') === 0;
  },
  // 是否是元素结点
  isElementNode(node) {
    return node.nodeType === 1;
  },
  // 判断是不是最原始的 Text 结点，纯文本结点，不是标签
  isTextNode(node) {
    return node.nodeType === 3;
  },
};
```

## 参考
- [vue的双向绑定原理及实现](https://www.cnblogs.com/libin-1/p/6893712.html) 
