---
title: CSS选择器及优先级
comments: false
date: 2018-09-03 10:48:03
categories: css
tags: css选择器优先级
---
## CSS 三大特性
- 继承：即子类元素继承父类的样式
- 优先级：是指不同类别样式的权重比较
- 层叠：当数量相同时，通过层叠的方式进行处理（后者覆盖前者）

## CSS 选择器分类
### id 选择器

```css
#selector {}
```

### class 选择器(类)
 
```css
.selector {}
```

### 标签选择器(div span)

```css
div {}
```

### 全局选择器（*）

```css
* {}
```

### 组合选择器(.head.head-fixed)

```css
.head.head-fixed {}
```

### 伪类、伪元素
#### 伪类
- `:hover` 鼠标悬浮在元素上方时，向元素添加样式（ `:hover` 和 `:active`同时用时，`:hover` 会覆盖 `:active`）
- `:visited` 向已经被访问的链接添加样式
- `:link` 向未被访问的链接添加样式
- `:active` 向被激活的元素添加样式
- `:focus` 向拥有键盘输入焦点的元素添加样式
- `:first-child` 向元素的第一个子元素添加样式
- `:lang` `:lang(no)` 向带有指定 lang 属性的元素添加样式
- `:first-child` `p:first-child` 选择属于父元素的第一个子元素的每个 `<p>` 元素
- `:first-of-type` `p:first-of-type` 选择属于其父元素的首个 `<p>` 元素的每个 `<p>` 元素 
- `:last-of-type` `p:last-of-type` 选择属于其父元素的最后 `<p>` 元素的每个 `<p>` 元素 
- `:only-of-type` `p:only-of-type` 选择属于其父元素唯一的 `<p>` 元素的每个 `<p>` 元素 
- `:only-child` `p:only-child` 选择属于其父元素的唯一子元素的每个 `<p>` 元素
- `:nth-child(n)` 选择第n个子元素
- `:nth-last-child(n)` 从最后一个开始计数的的第n个（倒数第n个）
- `:nth-of-type(n)` `p:nth-of-type(3)` 其父元素第3个 `<p>` 元素的所有 `<p>` 元素
- `:nth-last-of-type(n)` `p:nth-last-of-type` 从最后一个开始计数
- `:last-child` `p:last-child` 父元素的最后一个 `<p>` 元素
- `:root` 根元素 `<html>`
- `:empty` `p:empty` 选择没有子元素的每个空元素
- `:enabled` `input:enabled` 选择启用的 `<input>` 元素
- `:disabled` `input:disabled` 选择禁用的 `<input>` 元素
- `:checked` `input:checked` 选择每个被选中的 `<input>` 元素
- `:not(selector)` `:not(p)` 选择非 `<p>` 元素的每个元素
- `::selection` 选择被用户选取的元素部分
- `:before` 可以在元素的内容前面插入新内容
- `:after` 可以在元素的内容之后插入新内容
- `:first-line` 用于向文本的首行设置特殊样式
    下面的属性可应用于 `:first-line` 伪元素
    - font
    - color
    - background
    - word-spacing
    - letter-spacing
    - text-decoration
    - vertical-align
    - text-transform
    - line-height
    - clear
- `:first-letter` 向文本的首字母设置特殊样式
    下面的属性可应用于 `first-letter` 伪元素
    - font
    - color
    - background
    - margin
    - padding
    - border
    - text-decoration
    - vertical-align (仅当 float 为 none 时)
    - text-transform
    - line-height
    - float
    - clear

### 属性选择器
- `[attr^=value]` `a[src^="https"]` 选择其 src 属性值以 "https" 开头的每个 `<a>` 元素，等同于 `a[src^=https]`
- `[attr*=value]` `a[src*="https"]` 选择其 src 属性值包含 "https" 的每个 `<a>` 元素，等同于 `a[src*=https]`
- `[attr$=value]` `a[src$="https"]` 选择其 src 属性值以 "https" 开头的每个 `<a>` 元素，等同于 `a[src$=https]`
- `[attr]` `[target]` 选择带有 target 属性的所有元素
- `[attr=value]` `[target=_blank]` 选择 `target="_blank"` 的所有元素

### 关系选择符(+、 >、 ~、' '、 ,)
- `+` `div+span` 选择所有 `<div>` 元素后面紧接着的 `<span>` 元素（选中 `<div>` 元素后面的第一个 `<span>` 元素）
- `>` `div>span` 选择 `<div>` 的所有直接 `<span>` 元素
- `~` `div~span` 选择前面有 `<div>` 元素的每个 `<span>` 元素
- 空格 `div span` 选择 `<div>` 元素的所有 `<span>` 子元素
- 紧挨着 `div.container` 选择所有有 `.container` 类选择器的 `<div>` 元素
- `,` `div,span,h1,h2` 选择所有的 `<div>` `<span>` `<h1>` `<h2>`

## CSS 优先级
当两个规则都作用到了同一个html元素上时，如果定义的属性有冲突，则应该要按照一套规则来进行。

浏览器通过 **优先级** 来判断哪一些属性值与一个元素最为相关，从而在该元素上应用这些属性值。优先级是基于不同种类选择器组成的匹配规则。

1. `!important` 会覆盖页面内任何位置定义的元素样式
1. 内联样式
1. id选择器
1. class选择器
1. 标签选择器
1. 通配符选择器
1. 浏览器自定义或者继承

### 优先级是如何计算的
优先级就是分配给指定的CSS声明的一个权重，它由 **匹配的选择器** 中的每一种选择器的类型的 **数值** 决定。

当优先级与多个CSS声明中任意一个声明的优先级相等的时候，CSS中最后的那个声明将会应用到元素上（优先级都相同的则应用最后一个）。

当同一个元素有多个声明的时候，优先级才会有意义。因为每一个直接作用于元素的CSS规则总是会接管/覆盖（take over）该元素从祖先元素继承而来的规则。

### `!important` 规则
当在一个样式声明中使用 `!important` 规则时，此声明将覆盖任何其他声明。当两条互相冲突的带有 `!important` 规则的声明被应用到相同的元素上时，拥有更大优先级的声明将会被采用。

**怎样覆盖 `!important`**

- 只需再添加一条带 `!important` 规则的CSS规则，给这个选择器更高的优先级（添加一个标签，ID或类）。
- 使用相同的选择器，但是置于已有样式之后。

## 参考
- [CSS 选择器优先级总结](https://www.cnblogs.com/zxjwlh/p/6213239.html)
- [CSS 选择器参考手册](http://www.w3school.com.cn/cssref/css_selectors.asp)
- [伪类](http://www.w3school.com.cn/css/css_pseudo_classes.asp)
- [伪元素](http://www.w3school.com.cn/css/css_pseudo_elements.asp)
- [MDN 优先级是如何计算的？
](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)
- [为什么 CSS 选择器是从右往左解析](https://blog.csdn.net/jinboker/article/details/52126021)