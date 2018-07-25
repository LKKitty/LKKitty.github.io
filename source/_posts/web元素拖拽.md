---
title: web元素拖拽
date: 2018-07-25 16:58:47
tags:
---
Today给自己列了一个小表格，emmmmm，要努力成长。。。。。
![](http://pbzngw3z7.bkt.clouddn.com/%E6%B5%B7%E8%B4%BC%E7%8E%8B.jpg)
<!-- more -->
# 拖放是一种常见的特性，即抓取对象以后拖到另一个位置
拖拽的基本原理就是根据鼠标的移动来移动被拖拽的元素。鼠标(手指)的移动也就是x、y坐标的变化;元素的移动就是style.position的 top和left的改变。当然，并不是任何时候移动鼠标都要造成元素的移动，而应该判断鼠标左键的状态是否为按下状态，是否是在可拖拽的元素上按下的。
# html代码
```html
<div id="wrapper">
    <img src="http://pbzngw3z7.bkt.clouddn.com/u=957704823,1531888896&fm=27&gp=0.jpg">
</div>
```
# css样式
```css
#wrapper {
  position: absolute;
  width: 2rem /* 300/40 */;
  height: 2rem /* 300/40 */;
  border-radius: 50%;
  background-color: #f00;
  overflow: hidden;
}
#wrapper img {
  width: 100%;
  height: 100%;
}
```
# js代码
```js
 // 是否允许拖拽
  var isMove = true;
  // 获取移动的元素
  var wrapper = document.querySelector("#wrapper");
  // 移动的最大宽度
  var maxW = document.body.clientWidth - wrapper.offsetWidth;
  // 移动的最大高度
  var maxH = document.body.clientHeight - wrapper.offsetHeight;
  phoneTouch();
  pCMove();
  // pc端拖拽
  function pCMove() {
    wrapper.onmousedown = function (e) {
      if (isMove === false) return;
      var ev = e || window.event;
      // 移动开始的坐标
       beginX = ev.clientX;
       beginY = ev.clientY;
      // 获取鼠标点击的点与左边框的距离
      var disX = ev.clientX - this.offsetLeft;
      // 获取鼠标点击的点与上边框的距离
      var disY = ev.clientY - this.offsetTop;
      // setCapture可以将鼠标事件锁定在指定的元素上，当元素捕获了鼠标事件后，该事件只能作用在当前元素上
      if (wrapper.setCapture) {
        wrapper.setCapture();
      }
      document.onmousemove = function (e) {
        // Event 对象代表事件的状态，比如事件在其中发生的元素、键盘按键的状态、鼠标的位置、鼠标按钮的状态。
        var ev = e || window.event;
        // 移动的点与左边框的距离
        var oLeft = ev.clientX - disX;
        // 移动点与上边框的距离
        var oTop = ev.clientY - disY;
        // 判断距离是否为负数
        if (oLeft < 0) {
          oLeft = 0;
        } else if (oLeft >= maxW) {
          oLeft = maxW;
        }
        if (oTop < 0) {
          oTop = 0;
        } else if (oTop >= maxH) {
          oTop = maxH;
        }
        // 赋值
        wrapper.style.left = oLeft + 'px';
        wrapper.style.top = oTop + 'px';
      }
      document.onmouseup = function (e) {
        var ev = e || window.event;
        endX = ev.clientX
        endY = ev.clientY
        // checkJump(beginX, beginY, endX, endY);
        document.onmousemove = document.onmouseup = null;
        //释放全局捕获 releaseCapture();
        if (wrapper.releaseCapture) {
          wrapper.releaseCapture();
        }
      }
      return false;//阻止默认事件或冒泡
    }
  }
  // 移动端拖拽
  function phoneTouch() {
    if (isMove === false) return;
    // 监听触摸开始事件
    wrapper.addEventListener('touchstart', function (e) {
      var ev = e || window.event;
      // 获取手指触摸的第一个点
      var touch = ev.targetTouches[0];
      // 触摸点与屏幕左边框的的距离
      oL = touch.clientX - wrapper.offsetLeft;
      // 触摸点与屏幕上边框的的距离
      oT = touch.clientY - wrapper.offsetTop;
      // 取消默认的滑动事件
      document.addEventListener("touchmove", defaultEvent, false);
    })
    // 监听触摸过程事件
    wrapper.addEventListener('touchmove', function (e) {
      var ev = e || window.event;
      // 获取手指触摸的第一个点
      var touch = ev.targetTouches[0];
      // 获取手指移动的点与左边的距离
      var oLeft = touch.clientX - oL;
      // 获取手指移动的点与上边缘的距离
      var oTop = touch.clientY - oT;
      // 判断距离是否为负数和是否超过最大移动范围
      if (oLeft < 0) {
        oLeft = 0;
      } else if (oLeft >= maxW) {
        oLeft = maxW;
      }
      if (oTop < 0) {
        oTop = 0;
      } else if (oTop >= maxH) {
        oTop = maxH;
      }
      // 把距离赋值给移动的元素
      wrapper.style.left = oLeft + 'px';
      wrapper.style.top = oTop + 'px';
    })
    // 监听触摸结束
    wrapper.addEventListener('touchend', function () {
      document.removeEventListener("touchmove", defaultEvent);
    })
  }
  // 取消默认的事件
  function defaultEvent(e) {
    e.preventDefault();
  }
  ```
## 以上的小栗子是既可以在pc端也可以在移动端拖拽的
在这个例子中，遇到的比较突出的问题有以下几点：
## 1.移动端与pc端拖拽API的不一样
### pc端监听的是鼠标事件
(1)onmousedown：鼠标按下事件
(2)onmousemove：鼠标移动事件
(3)onmouseup：鼠标抬起事件
### 移动端监听的是touch事件
(1)touchstart：手指按下事件
(2)touchmove：手指移动事件
(3)touchend：手指抬起事件
## 2.pc端鼠标移动过快出现卡顿现象
刚开始完成这个例子的时候，pc端上鼠标移动过快会出现元素跟不上，出现卡顿现象，这是因为一开始我将元素的onmousemove()和onmouseup()事件绑定在了移动的元素上，造成单反鼠标移出移动元素时出现卡顿现象，解决的办法是将onmousemove()事件和onmouseup()事件绑定在父元素(即要移动的区域的元素上)，本栗子是绑定在document上。
## 3.移动的元素超出移动范围后出现滑动条
解决的办法是给定移动的最小范围和最大范围，然后进行判断一下哦。
## 4.小知识点
(1)Touch对象代表一个触点，可以通过event.touches[0]获取，每个触点包含位置，大小，形状，压力大小，和目标 element属性。
(2)targetTouches
也是一个TouchList对象，包含了如下触点的 Touch 对象：touchstart从当前事件的目标element上触发
(3)touches:手指触摸到屏幕上引起的当前所有触摸点的集合
(4)targetTouches:手指触摸到绑定事件的节点上的触摸点的集合;
(5)changedTouches:触摸事件时改变触摸点的集合;
(6)scrollHeight:获取对象的滚动高度。
(7)scrollLeft:设置或获取位于对象左边界和窗口中目前可见内容的最左端之间的距离
(8)scrollTop:设置或获取位于对象最顶端和窗口中可见内容的最顶端之间的距离
(9)scrollWidth:获取对象的滚动宽度
(10)offsetHeight:获取对象相对于版面或由父坐标 offsetParent 属性指定的父坐标的高度
(11)offsetLeft:获取对象相对于版面或由 offsetParent 属性指定的父坐标的计算左侧位置
(12)offsetTop:获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置
(13)event.clientX 相对文档的水平座标
(14)event.clientY 相对文档的垂直座标
(15)event.offsetX 相对容器的水平坐标
(16)event.offsetY 相对容器的垂直坐标
()17document.documentElement.scrollTop 垂直方向滚动的值
(18)event.clientX+document.documentElement.scrollTop 相对文档的水平座标+垂直方向滚动的量
![](http://pbzngw3z7.bkt.clouddn.com/client.gif)
(图片来自简书上某作者流沙麒麟客 
)
