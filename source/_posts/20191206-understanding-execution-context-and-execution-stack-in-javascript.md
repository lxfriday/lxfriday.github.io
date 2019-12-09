---
title: 前端面试必会 | 一文读懂 JavaScript 中的执行上下文和执行上下文栈
comments: true
top: false
date: 2019-12-06 20:34:00
img: https://qiniu1.lxfriday.xyz/blog/bingpic1_20191206203415.jpg
categories: javascript
tags:
  - 执行上下文
  - 执行上下文栈
  - js原理
---

<section id="nice" style="font-size: 16px; color: black; padding: 10px; line-height: 1.6; word-spacing: 0px; letter-spacing: 0px; word-break: break-word; word-wrap: break-word; text-align: left; font-family: Optima-Regular, Optima, PingFangSC-light, PingFangTC-light, 'PingFang SC', Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;"><figure style="margin: 0; margin-top: 10px; margin-bottom: 10px;"><img src="https://qiniu1.lxfriday.xyz/blog/bingpic1_20191206203415.jpg" alt style="display: block; margin: 0 auto; width: 100%;"></figure>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">一起看看 JavaScript 程序内部是如何执行的。</p>
<blockquote style="display: block; font-size: 0.9em; overflow: auto; overflow-scrolling: touch; border-left: 3px solid rgba(0, 0, 0, 0.4); background: rgba(0, 0, 0, 0.05); color: #6a737d; padding-top: 10px; padding-bottom: 10px; padding-left: 20px; padding-right: 10px; margin-bottom: 20px; margin-top: 20px;">
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0px; color: black; line-height: 26px;">本文翻译自 <a href="https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0" style="text-decoration: none; color: #1e6bb8; word-wrap: break-word; font-weight: bold; border-bottom: 1px solid #1e6bb8;">https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0</a>，作者 Sukhjinder Arora，有部分删改。</p>
</blockquote>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">如果你想成为一个合格的 JavaScript 开发者，你必须知道它的内部是如何执行的。掌握 JavaScript 执行上下文和执行栈对理解变量提升、作用域和闭包非常重要。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">理解执行上下文和执行栈将使你成为一个更加优秀的 JavaScript 开发者。</p>
<h2 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 24px;"><span>执行上下文是什么？</span></h2>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;"><strong style="font-weight: bold; color: black;">执行上下文是一个 JavaScript 代码运行的环境</strong>。任何 JavaScript 代码执行的时候都是处于一个执行上下文中。</p>
<h3 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 20px;"><span>执行上下文的类型</span></h3>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">JavaScript 中一共有三种执行上下文。</p>
<ul style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: disc;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;">全局执行上下文(Global)</strong> -- 它是默认的基本执行上下文。代码要么在全局执行上下文要么在函数执行上下文。它有两个特征：它会创建一个全局对象（在浏览器中就是 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">window</code>）并且会把 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">this</code> 设置为全局对象 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">windows</code>。在一个程序中只会有一个全局执行上下文。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;">函数执行上下文</strong> -- 当函数执行的时候，一个新的函数执行上下文就会创建。每个函数都有自己的执行上下文，当函数执行的时候上下文会被创建。函数执行上下文可以创建任意多个，每个执行上下文被创建的时候会经历若干步骤，接下来将会讨论。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;"><code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">eval</code> 函数执行上下文</strong> -- 在 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">eval</code> 函数中执行的代码也会有自己的自行上下文，但由于 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">eval</code> 已经不常用了，所以不做讨论。</section></li></ul>
<h2 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 24px;"><span>执行栈</span></h2>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">执行栈（执行上下文栈），在其他编程语言中也叫<strong style="font-weight: bold; color: black;">调用栈</strong>，是一个后进先出的结构。它用来存储代码执行过程中创建的所有执行上下文。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">当 JavaScript 引擎执行你的代码时，它会创建一个全局执行上下文并且将它推入当前的执行栈。当执行碰到函数调用的时候，它会为这个函数创建执行上下文并把这个执行上下文推入执行栈顶部。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">引擎执行处于栈顶的上下文对应的函数。当函数执行完毕，它的上下文就会从栈顶弹出，引擎接着继续执行新处于顶部的上下文对应的函数。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">看看下面的例子：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">let</span> a = <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'Hello World!'</span>;
<span class="hljs-function" style="line-height: 26px;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">function</span> <span class="hljs-title" style="color: #a6e22e; font-weight: bold; line-height: 26px;">first</span>(<span class="hljs-params" style="line-height: 26px;"></span>) </span>{
  <span class="hljs-built_in" style="color: #a6e22e; line-height: 26px;">console</span>.log(<span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'Inside first function'</span>);
  second();
  <span class="hljs-built_in" style="color: #a6e22e; line-height: 26px;">console</span>.log(<span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'Again inside first function'</span>);
}
<span class="hljs-function" style="line-height: 26px;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">function</span> <span class="hljs-title" style="color: #a6e22e; font-weight: bold; line-height: 26px;">second</span>(<span class="hljs-params" style="line-height: 26px;"></span>) </span>{
  <span class="hljs-built_in" style="color: #a6e22e; line-height: 26px;">console</span>.log(<span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'Inside second function'</span>);
}
first();
<span class="hljs-built_in" style="color: #a6e22e; line-height: 26px;">console</span>.log(<span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'Inside Global Execution Context'</span>);
</code></pre>
<figure style="margin: 0; margin-top: 10px; margin-bottom: 10px;"><img src="https://qiniu1.lxfriday.xyz/blog/image_20191205231941.png" alt="上面代码的执行上下文栈" style="display: block; margin: 0 auto; width: 100%;"><figcaption style="margin-top: 5px; text-align: center; color: #888; font-size: 14px;">上面代码的执行上下文栈</figcaption></figure>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">上面代码在浏览器中执行时，JavaScript 引擎会先创建一个全局执行上下文并把它推出执行栈中。碰到 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">first()</code> 执行时，引擎给这个函数创建一个新的执行上下文，然后把它推入执行栈顶部。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">当 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">second()</code> 在 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">first()</code> 函数内部执行时，引擎会给 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">second</code> 创建上下文并把它推入执行栈顶，当 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">second</code> 函数执行完毕，它的执行上下文就会从执行栈顶弹出，指针会指向它下面的上下文，也就是 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">first</code> 函数的上下文。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">当 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">first</code> 函数执行完毕，它的执行栈也会从栈顶弹出，指针就指向了全局执行上下文。当所有的代码执行完毕，引擎会把全局执行上下文也从执行栈中移出。</p>
<h2 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 24px;"><span>执行上下文是如何创建的</span></h2>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">从上面的过程，我们已经了解了 JavaScript 引擎是如何管理执行上下文的，接下来我们看看引擎是如何创建执行上下文的。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">执行上下文会经历两个阶段：1 创建阶段；2 执行阶段。</p>
<h3 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 20px;"><span>创建阶段</span></h3>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">执行上下文在创建阶段就会被创建。创建阶段做下面两件事：</p>
<ol style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: decimal;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">创建<strong style="font-weight: bold; color: black;">词法环境(LexicalEnvironment)</strong>；</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">创建<strong style="font-weight: bold; color: black;">变量环境(VariableEnvironment)</strong>；</section></li></ol>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">所以从概念上说，执行上下文可以用下面的方式表示：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">ExecutionContext = {
  LexicalEnvironment = &lt;ref. to LexicalEnvironment in memory&gt;,
  VariableEnvironment = &lt;ref. to VariableEnvironment in  memory&gt;,
}
</code></pre>
<h4 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 18px;"><span>词法环境(Lexical Environment)</span></h4>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;"><a href="http://ecma-international.org/ecma-262/6.0/" style="text-decoration: none; color: #1e6bb8; word-wrap: break-word; font-weight: bold; border-bottom: 1px solid #1e6bb8;">ES6 官方文档</a>是这样定义词法环境的</p>
<blockquote style="display: block; font-size: 0.9em; overflow: auto; overflow-scrolling: touch; border-left: 3px solid rgba(0, 0, 0, 0.4); background: rgba(0, 0, 0, 0.05); color: #6a737d; padding-top: 10px; padding-bottom: 10px; padding-left: 20px; padding-right: 10px; margin-bottom: 20px; margin-top: 20px;">
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0px; color: black; line-height: 26px;">A Lexical Environment is a specification type used to define the association of Identifiers to specific variables and functions based upon the lexical nesting structure of ECMAScript code. A Lexical Environment consists of an Environment Record and a possibly null reference to an outer Lexical Environment.</p>
</blockquote>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">简单来说，<strong style="font-weight: bold; color: black;">词法环境是一种表示标识符和变量的映射关系的环境。在词法环境中，标识符指向变量或者函数，变量是指对象（包括函数对象和数组对象）或者原始值。</strong></p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">举个例子，看看下面的代码</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">var</span> a = <span class="hljs-number" style="line-height: 26px;">20</span>;
<span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">var</span> b = <span class="hljs-number" style="line-height: 26px;">40</span>;
<span class="hljs-function" style="line-height: 26px;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">function</span> <span class="hljs-title" style="color: #a6e22e; font-weight: bold; line-height: 26px;">foo</span>(<span class="hljs-params" style="line-height: 26px;"></span>) </span>{
  <span class="hljs-built_in" style="color: #a6e22e; line-height: 26px;">console</span>.log(<span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'bar'</span>);
}
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">上面代码的词法环境如下</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">lexicalEnvironment = {
  a: 20,
  b: 40,
  foo: <span class="xml" style="line-height: 26px;"><span class="hljs-tag" style="color: #f92672; line-height: 26px;">&lt;<span class="hljs-name" style="color: #f92672; line-height: 26px;">ref.</span> <span class="hljs-attr" style="line-height: 26px;">to</span> <span class="hljs-attr" style="line-height: 26px;">foo</span> <span class="hljs-attr" style="line-height: 26px;">function</span>&gt;</span></span>
}
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">每个词法环境由三个部分组成：</p>
<ol style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: decimal;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">词法环境内部的环境记录（Environment Record）；</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">一个指向外层词法环境的可空引用（Reference to the outer environment）；</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">this</code></section></li></ol>
<h5 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 16px;"><span>环境记录(Environment Record)</span></h5>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">Environment Record 是在词法环境中存储变量和函数的地方。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">Environment Record 有下面两种：</p>
<ul style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: disc;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;">Declarative environment record</strong> -- As its name suggests stores variable and function declarations. The lexical environment for function code contains a declarative environment record.</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;">Object environment record</strong> --  The lexical environment for global code contains a objective environment record. Apart from variable and function declarations, the object environment record also stores a global binding object (window object in browsers). So for each of binding object’s property (in case of browsers, it contains properties and methods provided by browser to the window object), a new entry is created in the record.</section></li></ul>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">上面是原文，简单解释下：</p>
<ul style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: disc;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;">Declarative environment record</strong> -- 用来放变量或者函数声明，函数中的词法环境都是这种；</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><strong style="font-weight: bold; color: black;">Object environment record</strong> -- 指向全局对象 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">window</code>（在浏览器中），全局词法环境是这种；</section></li></ul>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">注意：对于<strong style="font-weight: bold; color: black;">函数</strong>，环境记录也包括一个 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">arguments</code> 对象。<code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">arguments</code> 是一个类数组对象，它包含索引和参数值的映射。看看下面的例子：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;"><span class="hljs-function" style="line-height: 26px;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">function</span> <span class="hljs-title" style="color: #a6e22e; font-weight: bold; line-height: 26px;">foo</span>(<span class="hljs-params" style="line-height: 26px;">a, b</span>) </span>{
  <span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">var</span> c = a + b;
}
foo(<span class="hljs-number" style="line-height: 26px;">2</span>, <span class="hljs-number" style="line-height: 26px;">3</span>);
<span class="hljs-comment" style="color: #75715e; line-height: 26px;">// argument object</span>
Arguments: {<span class="hljs-number" style="line-height: 26px;">0</span>: <span class="hljs-number" style="line-height: 26px;">2</span>, <span class="hljs-number" style="line-height: 26px;">1</span>: <span class="hljs-number" style="line-height: 26px;">3</span>, <span class="hljs-attr" style="line-height: 26px;">length</span>: <span class="hljs-number" style="line-height: 26px;">2</span>},
</code></pre>
<h5 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 16px;"><span><code>outer</code> 是什么</span></h5>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;"><code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">outer</code> 表示一个作用域指向的外层词法环境。在查找变量时，如果在当前的词法环境里面没有找到变量，那就通过 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">outer</code> 找到外层的词法环境，然后再在外层的词法环境里面查找变量，如果还没有找到，则会继续往外层找，一直找到全局作用域。</p>
<h5 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 16px;"><span><code>this</code> 怎么确定</span></h5>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">在全局执行上下文中，<code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">this</code> 指向全局对象 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">window</code>（在浏览器中）。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;"><strong style="font-weight: bold; color: black;">在函数执行上下文中，<code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">this</code> 取决于函数是如何被调用的</strong>。这是我们经常弄混的一点。如果是通过对象调用的函数，那 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">this</code> 指向这个对象。否则 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">this</code> 将会指向全局对象（在浏览器中是 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">window</code>）或者 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">undefined</code>（严格模式下） 。 看下面的例子：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">const</span> person = {
  <span class="hljs-attr" style="line-height: 26px;">name</span>: <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">'peter'</span>,
  <span class="hljs-attr" style="line-height: 26px;">birthYear</span>: <span class="hljs-number" style="line-height: 26px;">1994</span>,
  <span class="hljs-attr" style="line-height: 26px;">calcAge</span>: <span class="hljs-function" style="line-height: 26px;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">function</span>(<span class="hljs-params" style="line-height: 26px;"></span>) </span>{
    <span class="hljs-built_in" style="color: #a6e22e; line-height: 26px;">console</span>.log(<span class="hljs-number" style="line-height: 26px;">2018</span> - <span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">this</span>.birthYear);
  }
}
person.calcAge();
<span class="hljs-comment" style="color: #75715e; line-height: 26px;">// 'this' 指向 'person'， 因为 'calcAge' 是通过 `person` 对象调用的。</span>
<span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">const</span> calculateAge = person.calcAge;
calculateAge();
<span class="hljs-comment" style="color: #75715e; line-height: 26px;">// 'this' 指向全局对象，因为函数不是通过对象引用的方式调用的。</span>
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">词法作用域用伪代码表示是这样的：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">GlobalExectionContext = {
  <span class="hljs-attr" style="line-height: 26px;">LexicalEnvironment</span>: {
    <span class="hljs-attr" style="line-height: 26px;">EnvironmentRecord</span>: {
      <span class="hljs-attr" style="line-height: 26px;">Type</span>: <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">"Object"</span>,
      <span class="hljs-comment" style="color: #75715e; line-height: 26px;">// Identifier bindings go here</span>
    }
    outer: &lt;null&gt;,
    this: &lt;global object&gt;
  }
}
FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
    }
    outer: &lt;Global or outer function environment reference&gt;,
    this: &lt;depends on how function is called&gt;
  }
}
</code></pre>
<h4 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 18px;"><span>变量环境(Variable Environment)</span></h4>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">变量环境也是一个词法环境，它和词法环境长得一样。区别在于，在 ES6 中，词法环境用来存储函数声明和 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">let</code>、<code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">const</code> 声明的变量，变量环境仅仅用来存储 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">var</code> 声明的变量。</p>
<h3 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 20px;"><span>执行阶段</span></h3>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">在执行阶段会完成变量的赋值，代码会被执行。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">看下面的例子：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">let</span> a = <span class="hljs-number" style="line-height: 26px;">20</span>;
<span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">const</span> b = <span class="hljs-number" style="line-height: 26px;">30</span>;
<span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">var</span> c;
<span class="hljs-function" style="line-height: 26px;"><span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">function</span> <span class="hljs-title" style="color: #a6e22e; font-weight: bold; line-height: 26px;">multiply</span>(<span class="hljs-params" style="line-height: 26px;">e, f</span>) </span>{
 <span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">var</span> g = <span class="hljs-number" style="line-height: 26px;">20</span>;
 <span class="hljs-keyword" style="color: #f92672; font-weight: bold; line-height: 26px;">return</span> e * f * g;
}
c = multiply(<span class="hljs-number" style="line-height: 26px;">20</span>, <span class="hljs-number" style="line-height: 26px;">30</span>);
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">当上面的代码执行的时候，JavaScript 引擎会创建一个全局执行上下文来执行全局的代码。所以在创建阶段（<strong style="font-weight: bold; color: black;">creation phase</strong>）全局执行上下文是像这样的：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">GlobalExectionContext = {
  <span class="hljs-attr" style="line-height: 26px;">LexicalEnvironment</span>: {
    <span class="hljs-attr" style="line-height: 26px;">EnvironmentRecord</span>: {
      <span class="hljs-attr" style="line-height: 26px;">Type</span>: <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">"Object"</span>,
      <span class="hljs-comment" style="color: #75715e; line-height: 26px;">// Identifier bindings go here</span>
      a: &lt; uninitialized &gt;,
      b: &lt; uninitialized &gt;,
      multiply: &lt; func &gt;
    }
    outer: &lt;null&gt;,
    ThisBinding: &lt;Global Object&gt;
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      c: undefined,
    }
    outer: &lt;null&gt;,
    ThisBinding: &lt;Global Object&gt;
  }
}
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">在执行阶段（<strong style="font-weight: bold; color: black;">execution phase</strong>），会进行变量赋值。全局执行上下文将会变成下面这样：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">GlobalExectionContext = {
  <span class="hljs-attr" style="line-height: 26px;">LexicalEnvironment</span>: {
    <span class="hljs-attr" style="line-height: 26px;">EnvironmentRecord</span>: {
      <span class="hljs-attr" style="line-height: 26px;">Type</span>: <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">"Object"</span>,
      <span class="hljs-comment" style="color: #75715e; line-height: 26px;">// Identifier bindings go here</span>
      a: <span class="hljs-number" style="line-height: 26px;">20</span>,
      <span class="hljs-attr" style="line-height: 26px;">b</span>: <span class="hljs-number" style="line-height: 26px;">30</span>,
      <span class="hljs-attr" style="line-height: 26px;">multiply</span>: &lt; func &gt;
    }
    outer: &lt;null&gt;,
    ThisBinding: &lt;Global Object&gt;
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      c: undefined,
    }
    outer: &lt;null&gt;,
    ThisBinding: &lt;Global Object&gt;
  }
}
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">当碰到要执行 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">multiply(20, 30)</code> 时，一个新的函数执行上下文会创建。在创建阶段（<strong style="font-weight: bold; color: black;">creation phase</strong>）函数执行上下文会像下面这样：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">FunctionExectionContext = {
  <span class="hljs-attr" style="line-height: 26px;">LexicalEnvironment</span>: {
    <span class="hljs-attr" style="line-height: 26px;">EnvironmentRecord</span>: {
      <span class="hljs-attr" style="line-height: 26px;">Type</span>: <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">"Declarative"</span>,
      <span class="hljs-comment" style="color: #75715e; line-height: 26px;">// Identifier bindings go here</span>
      Arguments: {<span class="hljs-number" style="line-height: 26px;">0</span>: <span class="hljs-number" style="line-height: 26px;">20</span>, <span class="hljs-number" style="line-height: 26px;">1</span>: <span class="hljs-number" style="line-height: 26px;">30</span>, <span class="hljs-attr" style="line-height: 26px;">length</span>: <span class="hljs-number" style="line-height: 26px;">2</span>}, <span class="hljs-comment" style="color: #75715e; line-height: 26px;">// 函数的参数也在词法环境中</span>
    },
    <span class="hljs-attr" style="line-height: 26px;">outer</span>: &lt;GlobalLexicalEnvironment&gt;,
    ThisBinding: &lt;Global Object or undefined&gt;,
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      g: undefined
    },
    outer: &lt;GlobalLexicalEnvironment&gt;,
    ThisBinding: &lt;Global Object or undefined&gt;
  }
}
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">在执行阶段（<strong style="font-weight: bold; color: black;">execution phase</strong>）会进行变量赋值。赋值之后的函数执行上下文如下：</p>
<pre class="custom" style="margin-top: 10px; margin-bottom: 10px;"><code class="hljs" style="overflow-x: auto; padding: 16px; background: #272822; color: #ddd; font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; border-radius: 0px; font-size: 12px; -webkit-overflow-scrolling: touch; display: -webkit-box !important;">FunctionExectionContext = {
  <span class="hljs-attr" style="line-height: 26px;">LexicalEnvironment</span>: {
    <span class="hljs-attr" style="line-height: 26px;">EnvironmentRecord</span>: {
      <span class="hljs-attr" style="line-height: 26px;">Type</span>: <span class="hljs-string" style="color: #a6e22e; line-height: 26px;">"Declarative"</span>,
      <span class="hljs-comment" style="color: #75715e; line-height: 26px;">// Identifier bindings go here</span>
      Arguments: {<span class="hljs-number" style="line-height: 26px;">0</span>: <span class="hljs-number" style="line-height: 26px;">20</span>, <span class="hljs-number" style="line-height: 26px;">1</span>: <span class="hljs-number" style="line-height: 26px;">30</span>, <span class="hljs-attr" style="line-height: 26px;">length</span>: <span class="hljs-number" style="line-height: 26px;">2</span>},
    },
    <span class="hljs-attr" style="line-height: 26px;">outer</span>: &lt;GlobalLexicalEnvironment&gt;,
    ThisBinding: &lt;Global Object or undefined&gt;,
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      g: 20
    },
    outer: &lt;GlobalLexicalEnvironment&gt;,
    ThisBinding: &lt;Global Object or undefined&gt;
  }
}
</code></pre>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">函数执行完成时，返回的值将会赋值给 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">c</code>，全局词法环境将会更新，然后所有代码执行完毕，程序结束。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;"><strong style="font-weight: bold; color: black;">你可能注意到了 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">let</code> 和 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">const</code> 声明的变量在创建阶段（creation phase） 和它的值没有任何关联，但是 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">var</code> 声明的变量被赋予了 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">undefined</code>。</strong></p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">这是因为在创建阶段 JavaScript 引擎会扫描到变量和函数声明。用 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">var</code> 声明的变量被初始化为 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">undefined</code>，用 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">let</code> <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">const</code> 声明的变量将不会被初始化。后者将会形成暂时性死区，提前使用它们将会报错。</p>
<figure style="margin: 0; margin-top: 10px; margin-bottom: 10px;"><img src="https://qiniu1.lxfriday.xyz/blog/image_20191206194554.png" alt="暂时性死区" style="display: block; margin: 0 auto; width: 100%;"><figcaption style="margin-top: 5px; text-align: center; color: #888; font-size: 14px;">暂时性死区</figcaption></figure>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">这就是变量提升。</p>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">注意，在执行阶段，如果引擎发现 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">let</code> 声明的变量并没有被赋值，引擎将会把它赋值为 <code style="font-size: 14px; word-wrap: break-word; padding: 2px 4px; border-radius: 4px; margin: 0 2px; color: #1e6bb8; background-color: rgba(27,31,35,.05); font-family: Operator Mono, Consolas, Monaco, Menlo, monospace; word-break: break-all;">undefined</code>。</p>
<figure style="margin: 0; margin-top: 10px; margin-bottom: 10px;"><img src="https://qiniu1.lxfriday.xyz/blog/image_20191206194846.png" alt style="display: block; margin: 0 auto; width: 100%;"></figure>
<h2 style="margin-top: 40px; margin-bottom: 20px; font-weight: bold; color: black; font-size: 24px;"><span>最后</span></h2>
<p style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">感谢阅读，欢迎关注我的公众号 <strong style="font-weight: bold; color: black;">云影 sky</strong>，带你解读前端技术。关注公众号可以拉你进讨论群，有任何问题都会回复。</p>
<figure style="margin: 0; margin-top: 10px; margin-bottom: 10px;"><img src="https://qiniu1.lxfriday.xyz/blog/image_20191206195201.png" alt="公众号" style="display: block; margin: 0 auto; width: 100%;"><figcaption style="margin-top: 5px; text-align: center; color: #888; font-size: 14px;">公众号</figcaption></figure>
</section>
