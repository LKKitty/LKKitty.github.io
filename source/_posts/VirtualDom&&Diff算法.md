---
title: Virtual Dom && Diff算法
date: 2019-04-29 09:39:27
tags:
  - js
---
  总结一下脑袋里的熔浆！！！！！
  <!-- more -->
  # 关于Virtual Dom
 Virtual Dom 翻译为虚拟DOM,这个词语在我的脑袋中已经存在了很久，但一直不了解这是个什么东东！！！！！现在让我们来看一下他到底是个什么东西！！！！！
  Virtual Dom在下文中简称为vdom（本人英语渣渣，实在太长了，就用简写吧，小声哔哔）。
  ## vdom是什么？
  vdom是虚拟的dom,简单来说vdom可以看作是一个使用javascript模拟了DOM结构的树形结构，这个树形结构包含了整个DOM结构的信息，如下图：
  ![](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/vdom/vdom1.png)
  
  在上图中，左边是真实的DOM树结构，右边是用JavaScript模拟的DOM树结构，从图中可以看出，javascript模拟的dom树结构一一对应左边的真实dom(标签名/标签属性/标签的子结构)。
  ##### 顺便值得一提的是，在前端语言(html/css/js)中，只有js可以模拟dom,因为只有js是图灵完备语言(可以写逻辑)，Dom变化的对比，放在JS层来做

  ## vdom为何会存在？
  在解答这个问题之前，我们先来看一个用jQuery写的一个例子:
  <p class="codepen" data-height="265" data-theme-id="0" data-default-tab="js,result" data-user="LKKitty" data-slug-hash="OGqaEa" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="OGqaEa">
  <span>See the Pen <a href="https://codepen.io/LKKitty/pen/OGqaEa/">
  OGqaEa</a> by LKKitty (<a href="https://codepen.io/LKKitty">@LKKitty</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
 </p>
 <script async src="https://static.codepen.io/assets/embed/ei.js"></script>

  这是一个用JQuery写的间的table表单，在例子当点击change按钮时，表单数据会进行变化。
  在代码中我们发现，当我们点击change按钮时我们需要再一次执行render()函数，即再一次进行渲染，而每一次执行render()，都需要清空容器（清空container，上一次执行的结果会继续保留），再把修改后的dom结构放到container中，而我们需改的数据只是其中的一两个，却要整个dom树全部删除再重绘，这是非常的耗费性能的（修改dom结构是昂贵的）。
  ##### 因此 vdom就出现了
  vdom就是为了解决dom树全部删除再重绘的低性能问题而存在的啦（个人理解哈），使用js来模拟dom树结构，将模拟的dom与真实dom用diff算法（下文将会进行介绍）进行比较差异，将需要修改的dom结构进行修改，没有变化的dom原封不动。
  由于dom操作是非常耗性能的，也是非常昂贵的，所以我们要尽量少的进行dom操作，要尽可能多的用js来代替这些操作的执行。
  + DOM操作时“昂贵”的，js运行效率高
  + 将DOM操作对比放在JS层，提高效率
  + 尽量减少DOM操作，而不是“推倒重来”
  + 项目越复杂，影响越严重
  + vdom即可解决这个问题
  + 提高重绘性能


## vdom如何应用？ 核心API是什么
  vdom是一类技术库，是比较独立的，下面我们将通过介绍[snabbdom](https://github.com/snabbdom/snabbdom)来了解vdom的用法

### 介绍snabbdom
  snabbdom是一个虚拟DOM库，专注于简单性，模块化，强大的功能和性能。
  其中官方有给出一个例子：
  ```js
  var snabbdom = require('snabbdom');
  var patch = snabbdom.init([ // Init patch function with chosen modules
    require('snabbdom/modules/class').default, // makes it easy to toggle classes
    require('snabbdom/modules/props').default, // for setting properties on DOM elements
    require('snabbdom/modules/style').default, // handles styling on elements with support for animations
    require('snabbdom/modules/eventlisteners').default, // attaches event listeners
  ]);
  var h = require('snabbdom/h').default; // helper function for creating vnodes

  var container = document.getElementById('container');

  var vnode = h('div#container.two.classes', {on: {click: someFn}}, [
    h('span', {style: {fontWeight: 'bold'}}, 'This is bold'),
    ' and this is just normal text',
    h('a', {props: {href: '/foo'}}, 'I\'ll take you places!')
  ]);
  // Patch into empty DOM element – this modifies the DOM as a side effect
  patch(container, vnode);

  var newVnode = h('div#container.two.classes', {on: {click: anotherEventHandler}}, [
    h('span', {style: {fontWeight: 'normal', fontStyle: 'italic'}}, 'This is now italic type'),
    ' and this is still just normal text',
    h('a', {props: {href: '/bar'}}, 'I\'ll take you places!')
  ]);
  // Second `patch` invocation
  patch(vnode, newVnode); // Snabbdom efficiently updates the old view to the new state
  ```
  这里可以看到列出来的两个核心函数，即h()函数和patch()函数。
  #### h()
  首先我们先来了解h()
  ![vdom](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/vdom/vdom2.png)
  从图中可以看出h()通过传入参数，返回的是一个vnode节点，模拟的是一个真实的dom节点，创建的虚拟DOM树里面的结构在左边的vnode里都有体现，所以现在看来我们的虚拟DOM结构树和snabbdom中的h()函数是完全可以对应起来的，可以通过一个方法将虚拟DOM结构转化成vnode；而上图中newVnode则指的是虚拟DOM树中的数据发生变化之后生成的vnode。
  #### patch()
  patch()分为两种情况，两种情况都分别要传入两个参数
  + 第一种情况 
  ```js
  patch(container,vnode)
  ```
  第一个参数为container，是一个真实的dom节点，是一个存放节点的容器，第二个参数vnode就是上面h()所返回的vnode,此时的patch()函数的作用是用来将初始化生成的真实的dom结构全部渲染到指定的空白容器container上
  + 第二种情况
  ```js
  patch(vnode,newVnode)
  ```
  当数据源有变化时，就要进行patch(vnode,newVnode)，第一个参数为上一次h()函数返回的vnode,第二个参数时当数据改变后h()返回的新的newVnode节点，此时patch函数的作用是使用diff算法对比两个参数的差异，进而更新参数变化的DOM节点。即根据diff算法找出数据改变前后的差异，只改动需要改动的dom节点，没有改变的dom节点，完全不动它，原来是什么样的现在就是什么样的。这样就减少了dom操作

  ### snabbdom的简单使用
  <p class="codepen" data-height="265" data-theme-id="0" data-default-tab="js,result" data-user="LKKitty" data-slug-hash="vMPMRM" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="snabbdom-demo">
  <span>See the Pen <a href="https://codepen.io/LKKitty/pen/vMPMRM/">
  snabbdom-demo</a> by LKKitty (<a href="https://codepen.io/LKKitty">@LKKitty</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
将这个例子放在浏览器中运行，在控制台中观看Elements节点的变化：

![vdom](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/vdom/vdom3.png)
从图中可以看出，改变的只有后面两个Item,Ietm1并没有任何的变化（没有闪烁说明没有改动还是原来的dom）。这相比于jq的全部删除再填充，snabbdom的vdom就大大提高了了性能，这也能从侧面说明了vdom存在的重要性。
  # 关于Diff算法
  在上文中我们了解了snabbdom库的主要核心函数有h()和patch()两个函数，其中patch()的第二个用法就是对比新旧vnode的差异在进行渲染，那么patch()是如何来对比新旧vnode的差异的呢？这里就涉及了一个算法叫 ***DIFF*** 算法。

  ## 简单介绍diff算法
  diff其实是linux中的一个基础的命令用来对比两个文本文件之间的差异。
  vue中应用diff算法是为了找出需要更新的节点。
  其实在我们日常开发中我们都在接触类似与diff算法的一些软件，比如svn可以看到当前代码和svn服务器上代码的不同之处，再如Beyond Compare这款软件也可以为我们指出两个对比文件的不同之处。
  简单使用如下：
  ```js
  var vnode = 
  {
    tag: 'ul',
    attrs: {id: 'list'},
    children:[
      {
        tag: 'li',
        attrs:{
          className:'item',
          children:['Item1']
        }
      },{
        tag: 'li',
        attrs:{
          className:'item',
          children:['Item2']
        }
      }
    ]
  }

  function createElement(vnode) {
    var tag = vnode.tag
    var attrs = vnode.attrs || {}
    var children = vnode.children || []
    if(!tag) {
      return null
    }

    // 创建元素
    var elem = document.createElement(tag)
    // 属性
    var attrName
    for (attrName in attrs) {
      if(atrrs.hasOwnProperty(attrName)) {
        elem.setAttribute(attrName,attrs[attrName])
      }
    }
    // 子元素
    children.forEach(function (childVnode) {
      // 给elem添加子元素
      elem.appendChild(createElement(childVnode))
    })
    // 返回真实的Dom
    return elem
  }

  // 对比elem
  function updateChildren(vnode,newVnode) {
    var children = vnode.children || []
    var newChildren = newVnode.children || []
    children.forEach(function (childVnode,index) {
      var newChildVnode = newChildren[index]
      if(childVnode.tag === newChildVnode.tag) {
        // 深层次对比（递归）
        updateChildren(childVnode,newChildVnode)
      } else {
        // 替换
        replaceNode(childVnode,newChildVnode)
      }
    })
  }
  ```
  # 小结
  啊！总结真的好难写，望坚持！

  # 参考资料
  [什么是虚拟DOM](https://www.cnblogs.com/gaosong-shuhong/p/9253959.html)
  [揭秘一线互联网企业前端JavaScript高级面试](https://coding.imooc.com/class/190.html)