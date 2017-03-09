---
layout:     post
title:      "Difference between React and Vue"
subtitle:   ""
date:       2017-03-05 12:00:00
header-img: "img/post-bg-2015.jpg"
tags:
    - javascript React Vue 
---

## DOM建立

React中，先是通过JSX或者是通过React.createElement()这种建立内存DOM的方式，建立内存DOM，然后mount到某个根DOM节点上。 也就是说React对用户开放的是操作React Element(内存DOM)，最后由底层自己去建立真正的DOM。

Vue使用了模板语法，看似像Angular的用法（是在HTML中使用了DOM)，但其实被Vue实例Mount的DOM节点，经过Vue的处理：
1. Compile el's outerHtml as template 
2. Create vm.$el and replace el with it
实质上也变成了Virtual DOM.


以下文字引用自Vue的官方文档中模版语法部分：	
> 在底层的实现上， Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。
如果你熟悉虚拟 DOM 并且偏爱 JavaScript 的原始力量，你也可以不用模板，直接写渲染（render）函数，使用可选的 JSX 语法。

所以说虽然实现方式不一样：React比较直接一点，直接使用JSX建立React Element（React中的Virtual DOM），Vue在创建Vue实例时，会取得DOM，并干掉DOM，采用compile出来的Virtual DOM控制它。但本质都是用的Virtual DOM。


## 数据绑定

React中采用一种自上而下的数据流方式。上层的数据以props的形势传递给下层，去渲染整个页面。在需要交互和修改的情况下，是通过State来实现的，需要自己去监听事件修改State，并使用SetState去触发UI的更新。没有实现自动的数据双向绑定。

Vue通过hijack的方式实现了数据双向绑定。这个另讲， 可以根angularjs的双向绑定做一个比较～


## 开发
React 推荐的做法是 JSX + inline style，也就是把 HTML 和 CSS 全都整进 JavaScript了。Vue 的默认 API 是以简单易上手为目标，但是进阶之后推荐的是使用 webpack + vue-loader 的单文件组件格式. 使用vue-loader以后， 你会发现：HTML 和 CSS，但是可以放在一个文件里。而且你还可以使用你想要的预处理器，比如 LESS, Jade, Coffee, Babel，都可以。

## 使用场景
React 配合严格的 Flux 架构，适合超大规模多人协作的复杂项目。目前React Native蛮火，社区也很强大。
Vue相对还在快速发展中。都可以吧～


## 总结
Vue是一个结合了React和Angular的家伙～