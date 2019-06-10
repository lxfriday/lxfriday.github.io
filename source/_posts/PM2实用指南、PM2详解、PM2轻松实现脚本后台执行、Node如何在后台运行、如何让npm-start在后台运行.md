---
title: PM2实用指南、PM2详解、PM2轻松实现脚本后台执行、Node如何在后台运行、如何让npm start在后台运行
comments: true
top: false
date: 2019-06-10 12:59:04
categories: pm2
tags: pm2
img: https://qiniu1.lxfriday.xyz/WaterM/ff22e15f-8365-4158-87bc-0774af30973f_WX20190610-130037-pm2.png
---


<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
<link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f57960eb32.css">
<div id="content_views" class="markdown_views prism-github-gist">
<p>如何让已经写好的 Node 代码在后台运行呢？即使关闭了命令行也能运行，这怎么做到呢？这里推荐一个工具 PM2</p>
<p><a href="https://pm2.io/doc/en/runtime/overview/" rel="nofollow" target="_blank">PM2 官网</a><br>
<a href="https://github.com/Unitech/pm2" rel="nofollow" target="_blank">PM2 github</a></p>
<p><img src="https://img-blog.csdnimg.cn/20190610100658325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyMjgxNDcx,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
大概解释下官网的介绍</p>
<p>PM2 是一个应用管理器，它自身具备负载均衡能力，它可以让应用在后台一直运行，也可以让无缝重启应用，中间不会停止服务，可以开机启动，对开发者来讲可以极大地方便应用部署，降低维护成本。</p>
<h1><a name="t0"></a><a id="_21" target="_blank"></a>安装</h1>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)"><span class="token function">npm</span> i -g pm2
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li></ul></pre>
<h1><a name="t1"></a><a id="_26" target="_blank"></a>启动</h1>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)">pm2 start app.js --watch   <span class="token comment">#实时监控app.js的方式启动，当app.js文件有变动时，pm2会自动reload</span>
pm2 start app.js -i max  <span class="token comment"># 根据有效CPU数目启动最大进程数目</span>
pm2 start app.js -n appname -i max -e err.log -o out.log <span class="token comment"># 以 appname 启动 app.js，错误当前目录 err.log 为 error log，out.log 为输出 log</span>
pm2 start bb.sh  --interpreter <span class="token function">bash</span> <span class="token comment"># 用 bash 执行脚本</span>
pm2 start test.py--interpreter python3 <span class="token comment"># 用 python 环境执行脚本</span>
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li><li style="color: rgb(153, 153, 153);">2</li><li style="color: rgb(153, 153, 153);">3</li><li style="color: rgb(153, 153, 153);">4</li><li style="color: rgb(153, 153, 153);">5</li></ul></pre>
<p>启动类似 <code>node xxx.js</code> 的项目</p>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)">pm2 start --name xxxsname xxx.js  <span class="token comment"># 将运行的实例命名为 xxxsname</span>
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li></ul></pre>
<p>启动类似 <code>npm start</code> 的项目</p>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)">pm2 start --name appname <span class="token function">npm</span> -- start <span class="token comment"># 设置应用名为 appname</span>
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li></ul></pre>
<p>启动类似 <code>npm run serve</code> 的项目</p>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)">pm2 start --name servename <span class="token function">npm</span> -- run serve <span class="token comment"># 设置应用名为 servename</span>
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li></ul></pre>
<p><code>--name xxx</code> 或者 <code>-n xxx</code> 表示将应用命名为 xxx</p>
<p>以配置文件的形式启动</p>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)">pm2 start pm2config.json
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li></ul></pre>
<h1><a name="t2"></a><a id="_PM2__60" target="_blank"></a>查看 PM2 部署的应用</h1>
<pre class="prettyprint"><code class="prism language-bash has-numbering" onclick="mdcp.copyCode(event)">pm2 list               <span class="token comment"># 显示所有进程状态</span>
pm2 <span class="token function">ls</span>                 <span class="token comment"># 显示所有进程状态</span>
pm2 show 0			   <span class="token comment"># 显示某个应用的详细信息</span>
pm2 monit              <span class="token comment"># 监视所有进程</span>
pm2 logs               <span class="token comment"># 显示所有进程日志</span>
pm2 log 0 	           <span class="token comment"># 查看 0 应用的日志</span>
pm2 stop all           <span class="token comment"># 停止所有进程</span>
pm2 restart all        <span class="token comment"># 重启所有进程</span>
pm2 reload all         <span class="token comment"># 0秒停机重载进程</span>
pm2 stop 0             <span class="token comment"># 停止指定的进程，0 是应用 id</span>
pm2 restart 0          <span class="token comment"># 重启指定的进程，0 是应用 id</span>
pm2 startup            <span class="token comment"># 产生 init 脚本 保持进程活着，startup 是指系统boot, 开机进程自启动</span>
pm2 unstartup          <span class="token comment"># 禁用开机进程自启动</span>
pm2 delete 0           <span class="token comment"># 杀死指定的进程，0 是应用 id，会删除该应用</span>
pm2 delete all         <span class="token comment"># 杀死全部进程，会删除所有应用</span>
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li><li style="color: rgb(153, 153, 153);">2</li><li style="color: rgb(153, 153, 153);">3</li><li style="color: rgb(153, 153, 153);">4</li><li style="color: rgb(153, 153, 153);">5</li><li style="color: rgb(153, 153, 153);">6</li><li style="color: rgb(153, 153, 153);">7</li><li style="color: rgb(153, 153, 153);">8</li><li style="color: rgb(153, 153, 153);">9</li><li style="color: rgb(153, 153, 153);">10</li><li style="color: rgb(153, 153, 153);">11</li><li style="color: rgb(153, 153, 153);">12</li><li style="color: rgb(153, 153, 153);">13</li><li style="color: rgb(153, 153, 153);">14</li><li style="color: rgb(153, 153, 153);">15</li></ul></pre>
<p><code>pm2 list</code><br>
<img src="https://img-blog.csdnimg.cn/20190610104031393.png" alt="在这里插入图片描述"><br>
<code>pm2 show</code><br>
<img src="https://img-blog.csdnimg.cn/20190610104122473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyMjgxNDcx,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<code>pm2 monit</code><br>
<img src="https://img-blog.csdnimg.cn/20190610104150751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyMjgxNDcx,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<code>pm2 logs</code><br>
<img src="https://img-blog.csdnimg.cn/20190610104222553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyMjgxNDcx,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<code>pm2 log bb</code><br>
<img src="https://img-blog.csdnimg.cn/20190610121507570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyMjgxNDcx,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h1><a name="t3"></a><a id="PM2__89" target="_blank"></a>PM2 文件目录</h1>
<p>PM2 文件默认放在 $HOME/.pm2<br>
<img src="https://img-blog.csdnimg.cn/2019061012172324.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyMjgxNDcx,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h1><a name="t4"></a><a id="_92" target="_blank"></a>自定义启动文件</h1>
<pre class="prettyprint"><code class="prism language-json has-numbering" onclick="mdcp.copyCode(event)"><span class="token punctuation">{</span>
  <span class="token string">"apps"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token punctuation">{</span>
    <span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"appname"</span><span class="token punctuation">,</span> # 应用名
    <span class="token string">"exec_interpreter"</span><span class="token punctuation">:</span> <span class="token string">"node"</span><span class="token punctuation">,</span> # 执行环境
    <span class="token string">"script"</span><span class="token punctuation">:</span> <span class="token string">"./b.js"</span><span class="token punctuation">,</span> # 要执行的脚本
    <span class="token string">"cwd"</span><span class="token punctuation">:</span> <span class="token string">"/home/uftp/test-pm2"</span><span class="token punctuation">,</span> # 项目路径
    <span class="token string">"exec_mode"</span><span class="token punctuation">:</span> <span class="token string">"fork"</span><span class="token punctuation">,</span>
    <span class="token string">"max_memory_restart"</span><span class="token punctuation">:</span> <span class="token string">"1G"</span><span class="token punctuation">,</span>
    <span class="token string">"autorestart"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">,</span> # 出错自动重启
    <span class="token string">"node_args"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">,</span>
    <span class="token string">"watch"</span><span class="token punctuation">:</span> <span class="token boolean">false</span>
    <span class="token string">"error_file"</span> <span class="token punctuation">:</span> <span class="token string">"./test-err.log"</span><span class="token punctuation">,</span> # 错误日志文件位置
    <span class="token string">"out_file"</span><span class="token punctuation">:</span> <span class="token string">"./test-out.log"</span><span class="token punctuation">,</span>  # 输出日志文件位置
    <span class="token string">"pid_file"</span><span class="token punctuation">:</span> <span class="token string">"./test.pid"</span><span class="token punctuation">,</span>  # 进程相关文件位置
    <span class="token string">"env"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
       <span class="token string">"NODE_ENV"</span><span class="token punctuation">:</span> <span class="token string">"development"</span>
    <span class="token punctuation">}</span><span class="token punctuation">,</span>
    <span class="token string">"min_uptime"</span><span class="token punctuation">:</span> <span class="token string">"60s"</span><span class="token punctuation">,</span>
    <span class="token string">"max_restarts"</span><span class="token punctuation">:</span> <span class="token number">30</span>
  <span class="token punctuation">}</span><span class="token punctuation">]</span>
<span class="token punctuation">}</span>
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li><li style="color: rgb(153, 153, 153);">2</li><li style="color: rgb(153, 153, 153);">3</li><li style="color: rgb(153, 153, 153);">4</li><li style="color: rgb(153, 153, 153);">5</li><li style="color: rgb(153, 153, 153);">6</li><li style="color: rgb(153, 153, 153);">7</li><li style="color: rgb(153, 153, 153);">8</li><li style="color: rgb(153, 153, 153);">9</li><li style="color: rgb(153, 153, 153);">10</li><li style="color: rgb(153, 153, 153);">11</li><li style="color: rgb(153, 153, 153);">12</li><li style="color: rgb(153, 153, 153);">13</li><li style="color: rgb(153, 153, 153);">14</li><li style="color: rgb(153, 153, 153);">15</li><li style="color: rgb(153, 153, 153);">16</li><li style="color: rgb(153, 153, 153);">17</li><li style="color: rgb(153, 153, 153);">18</li><li style="color: rgb(153, 153, 153);">19</li><li style="color: rgb(153, 153, 153);">20</li><li style="color: rgb(153, 153, 153);">21</li></ul></pre>
<p><code>apps</code>: 是一个数组，数组中的每一个对象表示一个运行中的应用，可以配置多个应用，同时启动<br>
<code>exec_interpreter</code> 执行环境，<code>node</code> <code>bash</code> <code>python</code> 等等<br>
<code>script</code>: 将要执行的脚本<br>
<code>cwd</code>: 应用的路径<br>
<code>exec_mode</code>: 应用程序启动模式，默认是<code>fork</code>，也可设置<code>cluster_mode</code><br>
<code>max_memory_restart</code>: 超过这个指定的值之后将会重启应用 <code>1G</code> <code>500M</code><br>
<code>autorestart</code>:  出现故障之后是否自动重启，默认 <code>true</code><br>
<code>watch</code>: 监听文件更改，更改文件之后自动重启<br>
<code>error_file</code>: error log 错误日志<br>
<code>out_file</code>: out log 输出日期<br>
<code>pid_file</code>: 进程文件<br>
<code>min_uptime</code>: 最短的运行时间，如果少于这个时间就退出了，则会触发 max_restarts<br>
<code>max_restarts</code>: 最大重启次数</p>
<hr>
<p>关注公众号 止水聊技术，即送技术资料，您的支持是我最大的动力<br>
<img src="https://img-blog.csdnimg.cn/20190609155836183.png" alt="lxfriday_xyz"></p>

</div>
<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-258a4616f7.css" rel="stylesheet">
</div>