---
title: 关于video标签的那些事
date: 2018-08-01 17:40:46
tags:
---
吃了个美美的下午茶。。。
芒果、桃子、枣子、哈密瓜、、、、、
嗝......
![](http://pbzngw3z7.bkt.clouddn.com/%E4%B8%8B%E5%8D%88.jpg)
<!-- more -->
由于我梦梦昨天询问了我一些关于video的问题，我决定对video进行一番研究。
# 知识点
## 标签的属性
<ul>
    <li>src ：视频的属性</li>
    <li>poster：poster属性用于指定一张图片，在当前视频数据无效时显示，可当作一张 预览图。视频数据无效可能是视频正在加载，可能是视频地址错误等等</li>
    <li>preload：属性有三个可选择的值：none、metadata、auto。如果不使用此属性，默认为auto</li>
    <li>autoplay：自动播放,当出现时，表示自动播放，去掉是表示不自动播放。</li>
    <li>loop：循环播放</li>
    <li>controls：浏览器自带的控制条</li>
    <li>width：视频宽度</li>
    <li>height：视频高度</li>
</ul>

## Media方法和属性
HTMLVideoElement和HTMLAudioElement 均继承自HTMLMediaElement
<ul>
    <li>Media.error; //null:正常</li>
    <li>Media.error.code; //1.用户终止 2.网络错误 3.解码错误 4.URL无效</li>
</ul>

### 网络状态
- Media.currentSrc; //返回当前资源的URL
- Media.src = value; //返回或设置当前资源的URL
- Media.canPlayType(type); //是否能播放某种格式的资源
- Media.networkState; //0.此元素未初始化 1.正常但没有使用网络 2.正在下载数据 3.没有找到资源
- Media.load(); //重新加载src指定的资源
- Media.buffered; //返回已缓冲区域，TimeRanges
- Media.preload; //none:不预载 metadata:预载资源信息 auto:

### 准备状态 
- Media.readyState;//1:HAVE_NOTHING 2:HAVE_METADATA 3.HAVE_CURRENT_DATA 4.HAVE_FUTURE_DATA 5.HAVE_ENOUGH_DATA
- Media.seeking; //是否正在seeking

### 回放状态

- Media.currentTime = value; //当前播放的位置，赋值可改变位置
- Media.startTime; //一般为0，如果为流媒体或者不从0开始的资源，则不为0
- Media.duration; //当前资源长度 流返回无限
- Media.paused; //是否暂停
- Media.defaultPlaybackRate = value;//默认的回放速度，可以设置
- Media.playbackRate = value;//当前播放速度，设置后马上改变
- Media.played; //返回已经播放的区域，TimeRanges，关于此对象见下文
- Media.seekable; //返回可以seek的区域 TimeRanges
- Media.ended; //是否结束
- Media.autoPlay; //是否自动播放
- Media.loop; //是否循环播放
- Media.play(); //播放
- Media.pause(); //暂停

### 视频控制
- Media.controls;//是否有默认控制条
- Media.volume = value; //音量
- Media.muted = value; //静音
TimeRanges(区域)对象
- TimeRanges.length; //区域段数
- TimeRanges.start(index) //第index段区域的开始位置
- TimeRanges.end(index) //第index段区域的结束位置

### 相关事件
- eventTester("loadstart"); //客户端开始请求数据
- eventTester("suspend"); //延迟下载
- eventTester("abort"); //客户端主动终止下载（不是因为错误引起）
- eventTester("loadstart"); //客户端开始请求数据
- eventTester("progress"); //客户端正在请求数据
- eventTester("suspend"); //延迟下载
- eventTester("abort"); //客户端主动终止下载（不是因为错误引起），
- eventTester("error"); //请求数据时遇到错误
- eventTester("stalled"); //网速失速
- eventTester("play"); //play()和autoplay开始播放时触发
- eventTester("pause"); //pause()触发
- eventTester("loadedmetadata"); //成功获取资源长度
- eventTester("loadeddata"); //
- eventTester("waiting"); //等待数据，并非错误
- eventTester("playing"); //开始回放
- eventTester("canplay"); //可以播放，但中途可能因为加载而暂停
- eventTester("canplaythrough"); //可以播放，歌曲全部加载完毕
- eventTester("seeking"); //寻找中
- eventTester("seeked"); //寻找完毕
- eventTester("timeupdate"); //播放时间改变
- eventTester("ended"); //播放结束
- eventTester("ratechange"); //播放速率改变
- eventTester("durationchange"); //资源长度改变
- eventTester("volumechange"); //音量改变

### 想要video能自动填充慢父div的大小，只要给video标签加上style="width= 100%; height=100%; object-fit: fill"即可。

 ### object-fit取值说明

object-fit主要适合于替换元素，比如：video、object、img、svg、和svg:video等。其默认值为fill。object-fill取值的说明如下：fill:此值为boject-fit的默认值，替换内容的大小被设置为填充元素的内容框，也就是说，元素的内容扩大到完全填充容器的外形尺寸，即使这打破其内在的宽高比。
 contain：替换元素内容大小保持长宽比例填充元素内容容器，其具体对象大小被解析为一个包含元素的宽度和高度。也就是说，如果你在替换元素上设置一个明确的高度和宽度，此值将导致内容大小，完全在固定的比例显示，但仍在元素尺寸内显示。
 cover：替换元素内容大小保持长宽比例填充元素内容容器，其具体对象大小被解析为覆盖整个元素的宽度和高度。也就是说，替换元素内容大小保持长宽比，但改变宽度和高度，以便完全覆盖内容元素。
 none：替换元素内容不调整大小以适应内部元素的容器，内容完全忽略设置在元素上的任何高度和权重，并且仍在元素尺寸内显示。
 scale-down：当内容大小设置了non或contain，将导致具体对象变得更小。
 ### 知识点参考<a href="https://blog.csdn.net/a460550542/article/details/52689915">原文链接</a>

 # 小栗子
 android下html5的视频播放一直是前端兼容的重灾区，各种体验差，被诟病已久。因此本人做了一个小小的栗子
 ```html
   <div class="wrapper">
    <div class="mask">
      我是遮罩层
    </div>
    <!-- <div class="padding"></div> -->
    <div class="video-container">
      <div class="video-box">
        <div class="videoBg">
          <video controls controlsList="nodownload" poster="../images/mv.png" class="video">
            <source src="../images/music.mp4" type="video/mp4">
          </video>
        </div>
        <video controls controlsList="nodownload">
          <source src="../images/music.mp4" type="video/mp4">
        </video>
        <video controls>
          <source src="../images/music.mp4" type="video/mp4">
        </video>
        <video controls>
          <source src="../images/music.mp4" type="video/mp4">
        </video>
        <video controls>
          <source src="../images/music.mp4" type="video/mp4">
        </video>
        <video controls>
          <source src="../images/music.mp4" type="video/mp4">
        </video>
        <video controls>
          <source src="../images/music.mp4" type="video/mp4">
        </video>
        <video controls>
          <source src="../images/music.mp4" type="video/mp4">
        </video>
      </div>
    </div>
  </div>
  ```
  css代码

  ```css
     .wrapper {
      position: relative;
      width: 100vw;
      height: 100vh;
    }

    .mask {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 1.6rem;
      text-align: center;
      line-height: 1.6rem;
      background-color: rgba(0, 0, 0, 0.8);
      font-size: 1rem/* 20/40 */
      ;
      color: #ffffff;
      z-index: 100;
    }

    .padding {
      position: relative;
      width: 100vw;
      height: 1.6rem;
      top: 0;
      left: 0;
      background-color: rgba(0, 0, 0, 0.8);
    }

    .video-container {
      position: relative;
      width: 100%;
      height: 100%;
      background-color: antiquewhite;
      overflow: hidden;
    }

    .video-box {
      position: relative;
      margin-top: 1.6rem;
      width: 100%;
      height: 100%;
      overflow: auto;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-wrap: wrap;
    }

    .videoBg {
      width: 9.52rem/* 400/40 */
      ;
      height: 5rem/* 300/40 */
      ;
      margin-top: 2rem;
      /* background-image: url('../images/mv.png') */ 
      background-position: center center;
      background-size: 100% 100%;
      background-repeat: no-repeat;
    }

    video {
      width: 9.52rem/* 400/40 */
      ;
      margin-top: 0.5rem;
    }

    /* 隐藏声音控件 */

    audio::-webkit-media-controls-mute-button,
    video::-webkit-media-controls-mute-button {
      display: none;
    }

    /* 显示时间 */

    audio::-webkit-media-controls-time-remaining-display,
    video::-webkit-media-controls-time-remaining-display {
      display: block !important;
    }

    audio::-webkit-media-controls-current-time-display,
    video::-webkit-media-controls-current-time-display {
      display: block !important
    }

    /* 修改背景颜色 */

    audio::-webkit-media-controls-panel,
    video::-webkit-media-controls-panel {
      background-color: rgba(255, 255, 255, 0.8);
    }
```
```js
 // 获取遮罩层的高度
    var maskHeight = document.getElementsByClassName('mask')[0].offsetHeight +16;
    // 获取video-box容器
    var videoContainer = document.getElementsByClassName('video-box');
    // 视频背景
    var videoBg = document.getElementsByClassName('videoBg');
    // 视频
    var video = document.getElementsByClassName('video');
    videoContainer[0].addEventListener("scroll", function (e) {
      // 获得滚动的像素数
      var videoScrollTop = videoContainer[0].scrollTop;
      videoBg
      if (videoScrollTop >= maskHeight) {
        videoBg[0].style.backgroundImage='url("../images/mv.png")';
        video[0].style.display = 'none';
        video[0].pause();
      } else {
        videoBg[0].style.backgroundImage="none";
        video[0].style.display = 'block';
      }
    })
```
# 遇到的问题
## 原生控件太丑
原生的控件实在太丑，除了自己写一套UI之外，我们还可以修改原生控件的样式
我们来看一下 video 的内部构造：
chrome 下，开发者工具 -> setting -> Preferences -> Elements -> 勾选 "Show user agent shadow DOM"
再回来看，已经可以看到 video 的内部结构了
### 隐藏全屏按钮 
```css
video::-webkit-media-controls-fullscreen-button{ display: none; }
```
### 隐藏下载按钮
1. controlsList
在 video 上加属性 controlsList="nodownload"，就可以隐藏下载按钮。
就这么简单？当然不是。。。这种方式需要 Chrome 58+ 才支持，不能用于生产环境
2. overflow: hidden
video::-webkit-media-controls-enclosure{ overflow: hidden; }
video::-webkit-media-controls-panel{ width: calc(100% + 30px); }
### video标签无法在手机上自动播放
移动端的浏览器为了避免浪费用户的网络流量，默认是不允许媒体文件自动播放的。
要想用代码解决这个问题是不现实的，所以只能退而求其次，引导用户手动触发视频的播放。
### video标签无法在手机上全屏播放
在ios端，可以加上webkit-playsinline="" playsinline="" x-webkit-airplay="true" airplay="allow"
```html
<video src="demo.mpr" webkit-playsinline="" playsinline="" x-webkit-airplay="true" airplay="allow" preload="auto"></video>
```
在安卓端，加上x5-video-player-fullscreen=”true”
### 控制横竖屏： x5-video-orientation
功能：声明播放器支持的方向
可选值： landscape 横屏, portraint竖屏
### 视频播放层级播放过高的问题
安卓手机上，不允许视频上层有东西。
对于播放后的视频，即使我们给父级加了overflow: hidden;也无法改变这一现象。
我的做发是拐个弯，在碰到遮挡层的时候，就将视频display=none;
### getBoundingClientRect()
getBoundingClientRect() 来获取页面元素的位置,但是兼容性差