---
title: web端拍照与录像
date: 2019-07-23 09:28:59
tags:
  -js
---
  又是记录挖坑填坑的一天~
  <!-- more -->
  最近公司在一个宣誓墙的项目，主要是web端外接摄像头进行拍照与录像，这个项目反反复复遇到很多问题，其实之前也有过类似的项目，但是还不理解各种api的用法，经过这个项目大大加深了对视频api的理解。
  # API介绍
  ## MediaDevices.getUserMedia()
    MediaDevices.getUserMedia() 会提示用户给予使用媒体输入的许可，媒体输入会产生一个MediaStream里面包含了请求的媒体类型的轨道。此流可以包含一个视频轨道（来自硬件或者虚拟视频源，比如相机、视频采集设备和屏幕共享服务等等）、一个音频轨道（同样来自硬件或虚拟音频源，比如麦克风、A/D转换器等等），也可能是其它轨道类型。
        
  它返回一个 Promise 对象，成功后会resolve回调一个 MediaStream 对象。若用户拒绝了使用权限，或者需要的媒体源不可用，promise会reject回调一个  PermissionDeniedError 或者 NotFoundError 。
  ### 通常你可以使用 navigator.mediaDevices 来获取 MediaDevices ，例如：
  ```js
  navigator.mediaDevices.getUserMedia(constraints)
  .then(function(stream) {
    /* 使用这个stream stream */
  })
  .catch(function(err) {
    /* 处理error */
  });
  ```
  ### 注意!!!
  navigator.getUserMedia()此API已更名为 MediaDevices.getUserMedia()。 请使用那个版本进行替代！ 这个已废弃的API版本仅为了向后兼容而存在。
  该特性已经从 Web 标准中删除，虽然一些浏览器目前仍然支持它，但也许会在未来的某个时间停止支持，请尽量不要使用该特性。
  ## MediaRecorder
  MediaRecorder 是 MediaStream Recording API 提供的用来进行媒体轻松录制的接口, 他需要通过调用 MediaRecorder() 构造方法进行实例化.
  MediaRecorder.MediaRecorder()创建一个新的MediaRecorder对象,对指定的MediaStream 对象进行录制,支持的配置项包括设置容器的MIME 类型 (例如"video/webm" 或者 "video/mp4")和音频及视频的码率或者二者同用一个码率
  ## 获取摄像头
  ```js
  var opt = {
      audio: false,
      video: {
      width: tw,
      height: th
      }
  };
   function cameraInit() {
    navigator.mediaDevices.getUserMedia(opt)
        .then(function(mediaStream) {
          // 
            var video = document.createElement('video')
            var chunks = [], startTime = 0;
            video.srcObject = mediaStream;
            video.onloadedmetadata = function(e) {
                video.play();
            };
            $('.container').append(video)
            $('.loading2').fadeOut()
        })
        .catch(function(err) {
            console.log(err.name + ": " + err.message);
        });
  }
  ```
# 获取不到摄像头
  遇到如下错误
  ![获取不到摄像头](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/webCamera/web1.png)

  ## 错误原因
  1. 没有接入摄像头
  2. 使用了http服务端打开

## 解决方法
  1. 检查电脑是否接入了摄像头，如是win10系统，可以在搜索框中搜索'相机'，看是否有画面，如没有，检查驱动是否是最新，可以使用鲁大师或360安全卫士检查
  ![](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/webCamera/web2.png)
  ![](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/webCamera/web3.png)
  2. 新版Chrome可以通过本地地址访问摄像头，比如localhost/video.html。但是不能通过IP地址访问，比如192.168.1.99/video.html。而其它低版本的chrome工作都是正常的。这是因为，http协议的网站的安全原因，不能访问本地的摄像头想要访问摄像头，麦克风等必须使用https

# 视频数据上传给后台只有0或者1kb
  前端在关闭数据流后马上上传给后台，此时后台返回的视频只有0kb/1kb,导致前端不能正确显示
  ## 产生原因
    在前端关闭数据流时，需要时间进行缓存（大概是这个原因），数据还没有缓存完就传给后台，导致数据流传不完整，后台保存出错
  ## 解决
    在关闭数据流后，设置定时器n秒后再上传

# 保存的视频或后台返回的视频在ios系统不能播放
  在做项目的过程中，前端将摄像头的流数据转换成Bold二进制数据发送给后台，后台返回视频路径。后端返回的视频在pc端和安卓移动端都可以播放，但是在ios移动端不能播放，在查了很多资料，并尝试了许多方法都不行，之后只能将视频进行转码。
  ## FFmpeg
  这里介绍一个开源插件ffmpeg
  FFmpeg是一个自由软件，可以运行音频和视频多种格式的录影、转换、流功能1，包含了libavcodec ─这是一个用于多个项目中音频和视频的解码器库，以及libavformat——一个音频与视频格式转换库。
  ### 安装
  在官网直接下载压缩包并解压就完成了安装
  [FFmpegWindow下载](https://console.qcloud.com/cos5/bucket/setting?type=filelist&bucketName=blog22-1258668722&path=%252FblogImg%252FwebCamera%252F&region=ap-guangzhou)
  ![](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/webCamera/web4.png)
  ### 使用
  ![](https://blog22-1258668722.cos.ap-guangzhou.myqcloud.com/blogImg/webCamera/web5.png)
  在解压后的bin目录下打开终端（shift + 右键）,然后运行命令
  语法 ffmpeg -i 原格式路径 转换后格式路径
  如下在本文件转换
  ```js
  ffmpeg -i sss.mp4 dd.mp4
  ```
  具体使用方法可以百度教程噢~
# 拍照
  拍照的原理是调用摄像头使其出现画面，利用canvas将某一帧的画面截图下来，做成拍照假象
  ```js
let takePhotoTimer = null //拍照倒计时
const video = document.getElementById('cameraView'); //video元素
const canvas = document.createElement('canvas'); //画布，用于截图
var takePhoto = document.getElementsByClassName('takePhoto')[0] //拍照父元素容器
var tw;  //容器宽
var th; //容器高
var scaleBy = 1; //画布比例
var videoScale = 1; //视频系数
const context = canvas.getContext('2d'); //画布上下文
  function countDown(){
  let num = 5;
  takePhotoTimer = setInterval(function(){
    num--;
    $('.text').text(num)
    if(num === 0){
      clearInterval(takePhotoTimer);
      video.style.opacity='0'
      $('.timeBox').fadeOut()
      // 播放拍照声音
      document.getElementById('music').play();
      // 截画面
      var cw = canvas.width = tw*videoScale; //画布宽
      var ch = canvas.height = th*videoScale; //画布高
      context.drawImage(video, 0, 0, cw, ch);
      // 截图地址
      photo64Src = canvas.toDataURL('image/png')
      $('.photo-img').attr('src',photo64Src).removeClass('hide')
      //  显示宣誓上墙
      $('.take-photo').addClass('hide')
      $('.swearWall').css('opacity',1)
      $('.swearWall').removeClass('hide')
      // 解除可以点击状态
      takeMore = true
      // console.log(prefixSrcQiniu)
      // 点击宣誓上墙
      swearWallType = 'photo'
    }
  },1000)
}
  ```
# 录像小栗子
[小栗子原文链接](https://blog.csdn.net/mrzhangdulin/article/details/84560146)
```html
<!DOCTYPE html>
<html>

<head>
    <title>video recoder</title>
    <script src="../../js/fileSaver.js"></script>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
</head>
<style>
    body{
        background-color:#EFEDEF;
    }
</style>
<body>
    <article style="border:1px solid white;width:400px;height:400px;margin:0 auto;background-color:white;">

        <section class="experiment" style="width:320px; height:240px;border:1px solid green; margin:50px auto;">
            <div id="videos-container" style="width:320px; height:240px;">
            </div>
        </section>
        <section class="experiment" style="text-align:center;border:none; margin-top:20px;">
            <button id="openCamera">打开摄像头</button>
            <button id="start-recording" disabled>开始录制</button>
            <button id="save-recording" disabled>保存</button>
            <!--<a href="javascript:void(0)" onclick="send()">发送</a>-->
        </section>
 </article>
 </body>
</html>
```
js
```js
function cameraInit(video) {
            video.style.opacity='1'
            navigator.mediaDevices.getUserMedia(opt)
                .then(function(mediaStream) {
                    var video = document.querySelector('video');
                    var chunks = [], startTime = 0;
                    video.srcObject = mediaStream;
                    video.onloadedmetadata = function(e) {
                        video.play();
                    };
                    $('.loading2').fadeOut()
                })
                .catch(function(err) {
                    console.log(err.name + ": " + err.message);
                });
        }
            var mediaStream;
            var recorderFile;
            var stopRecordCallback;
            var openBtn = document.getElementById("openCamera");
            var startBtn = document.getElementById("start-recording");
            var saveBtn = document.getElementById("save-recording");
            openBtn.onclick = function() {
                this.disabled = true;
                startBtn.disabled=false;
                openCamera();
            };
            startBtn.onclick = function() {
                this.disabled = true;
                startRecord();
            };
            saveBtn.onclick = function() {
                saver();

                // alert('Drop WebM file on Chrome or Firefox. Both can play entire file. VLC player or other players may not work.');
            };
            var mediaRecorder;
            var videosContainer = document.getElementById('videos-container');
            var vendorUrl = window.URL || window.webkitURL;
            // var takePhoto = document.getElementsByClassName('takePhoto')[0] //拍照父元素容器
            // var tw;  //容器宽
            // var th; //容器高
            var opt = {
                audio: false,
                video: true
            };
            function openCamera(){
                var len = videosContainer.childNodes.length;
                for(var i=0;i<len;i++){
                    videosContainer.removeChild(videosContainer.childNodes[i]);
                }

                var video = document.createElement('video');

                var videoWidth = 320;
                var videoHeight = 240;

                video.controls = false;
                video.muted = true;
                video.width = videoWidth;
                video.height = videoHeight;
                video.style.opacity='1'
                MediaUtils.getUserMedia(true, false, function (err, stream) {
                    if (err) {
                        throw err;
                    } else {
                        // 通过 MediaRecorder 记录获取到的媒体流
                        mediaRecorder = new MediaRecorder(stream);
                        mediaStream = stream;
                        var chunks = [], startTime = 0;
                        video.srcObject = stream;
                        video.play();
                        videosContainer.appendChild(video);
                        mediaRecorder.ondataavailable = function(e) {
                            mediaRecorder.blobs.push(e.data);
                            chunks.push(e.data);
                        };
                        mediaRecorder.blobs = [];

                        mediaRecorder.onstop = function (e) {
                            recorderFile = new Blob(chunks, { 'type' : mediaRecorder.mimeType });
                            chunks = [];
                            if (null != stopRecordCallback) {
                                stopRecordCallback();
                            }
                        };
                }
            });
            }

            // 停止录制
            function stopRecord(callback) {
                stopRecordCallback = callback;
                // 终止录制器
                mediaRecorder.stop();
                console.log(mediaRecorder)
                // 关闭媒体流
                MediaUtils.closeStream(mediaStream);
            }

            var MediaUtils = {
                /**
                * 获取用户媒体设备(处理兼容的问题)
                * @param videoEnable {boolean} - 是否启用摄像头
                * @param audioEnable {boolean} - 是否启用麦克风
                * @param callback {Function} - 处理回调
                */
                getUserMedia: function (videoEnable, audioEnable, callback) {
                    navigator.getUserMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia
                    || navigator.msGetUserMedia || window.getUserMedia;
                    var constraints = {video: videoEnable, audio: audioEnable};
                    if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                        navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
                        callback(false, stream);
                    })['catch'](function(err) {
                        callback(err);
                    });
                    } else if (navigator.getUserMedia) {
                        navigator.getUserMedia(constraints, function (stream) {
                        callback(false, stream);
                    }, function (err) {
                        callback(err);
                    });
                } else {
                    callback(new Error('Not support userMedia'));
                }
            },

                /**
                * 关闭媒体流
                * @param stream {MediaStream} - 需要关闭的流
                */
                closeStream: function (stream) {
                    if (typeof stream.stop === 'function') {
                        stream.stop();
                    }
                    else {
                        let trackList = [stream.getAudioTracks(), stream.getVideoTracks()];
                        for (let i = 0; i < trackList.length; i++) {
                            let tracks = trackList[i];
                            if (tracks && tracks.length > 0) {
                                for (let j = 0; j < tracks.length; j++) {
                                    let track = tracks[j];
                                    if (typeof track.stop === 'function') {
                                        track.stop();
                                    }
                                }
                            }
                        }
                    }
                }
            }; 

function startRecord() {
    mediaRecorder.start();
    // console.log(mediaRecorder.start())
    setTimeout(function(){
        // 结束
        stopRecord(function() {
            console.log("录制成功!");
            // saver()
            openBtn.disabled=false;
            saveBtn.disabled=false;
            //send();
        });
    }, 5000);
}

function saver(){
    var file = new File([recorderFile], 'msr-' + (new Date).toISOString().replace(/:|\./g, '-') + '.mp4', {
        type: 'video/mp4'
    });
    console.log(file)
    saveAs(file);
}
function send(){
    var file = new File([recorderFile], 'msr-' + (new Date).toISOString().replace(/:|\./g, '-') + '.mp4', {
        type: 'video/mp4'
    });
    var data = new FormData();
    data.append("username", "test");
    data.append("userfile", file);

    var req = new XMLHttpRequest();
    req.open("POST", "com.spinsoft.bip.frame.utils.image.saveMp4.biz.ext");
    req.send(data);
}
```
栗子中用到[fileSaver.js](https://github.com/eligrey/FileSaver.js/)
```js
(function (global, factory) {
  if (typeof define === "function" && define.amd) {
    define([], factory);
  } else if (typeof exports !== "undefined") {
    factory();
  } else {
    var mod = {
      exports: {}
    };
    factory();
    global.FileSaver = mod.exports;
  }
})(this, function () {
  "use strict";

  /*
  * FileSaver.js
  * A saveAs() FileSaver implementation.
  *
  * By Eli Grey, http://eligrey.com
  *
  * License : https://github.com/eligrey/FileSaver.js/blob/master/LICENSE.md (MIT)
  * source  : http://purl.eligrey.com/github/FileSaver.js
  */
  // The one and only way of getting global scope in all environments
  // https://stackoverflow.com/q/3277182/1008999
  var _global = typeof window === 'object' && window.window === window ? window : typeof self === 'object' && self.self === self ? self : typeof global === 'object' && global.global === global ? global : void 0;

  function bom(blob, opts) {
    if (typeof opts === 'undefined') opts = {
      autoBom: false
    };else if (typeof opts !== 'object') {
      console.warn('Deprecated: Expected third argument to be a object');
      opts = {
        autoBom: !opts
      };
    } // prepend BOM for UTF-8 XML and text/* types (including HTML)
    // note: your browser will automatically convert UTF-16 U+FEFF to EF BB BF

    if (opts.autoBom && /^\s*(?:text\/\S*|application\/xml|\S*\/\S*\+xml)\s*;.*charset\s*=\s*utf-8/i.test(blob.type)) {
      return new Blob([String.fromCharCode(0xFEFF), blob], {
        type: blob.type
      });
    }

    return blob;
  }

  function download(url, name, opts) {
    var xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.responseType = 'blob';

    xhr.onload = function () {
      saveAs(xhr.response, name, opts);
    };

    xhr.onerror = function () {
      console.error('could not download file');
    };

    xhr.send();
  }

  function corsEnabled(url) {
    var xhr = new XMLHttpRequest(); // use sync to avoid popup blocker

    xhr.open('HEAD', url, false);

    try {
      xhr.send();
    } catch (e) {}

    return xhr.status >= 200 && xhr.status <= 299;
  } // `a.click()` doesn't work for all browsers (#465)


  function click(node) {
    try {
      node.dispatchEvent(new MouseEvent('click'));
    } catch (e) {
      var evt = document.createEvent('MouseEvents');
      evt.initMouseEvent('click', true, true, window, 0, 0, 0, 80, 20, false, false, false, false, 0, null);
      node.dispatchEvent(evt);
    }
  }

  var saveAs = _global.saveAs || ( // probably in some web worker
  typeof window !== 'object' || window !== _global ? function saveAs() {}
  /* noop */
  // Use download attribute first if possible (#193 Lumia mobile)
  : 'download' in HTMLAnchorElement.prototype ? function saveAs(blob, name, opts) {
    var URL = _global.URL || _global.webkitURL;
    var a = document.createElement('a');
    name = name || blob.name || 'download';
    a.download = name;
    a.rel = 'noopener'; // tabnabbing
    // TODO: detect chrome extensions & packaged apps
    // a.target = '_blank'

    if (typeof blob === 'string') {
      // Support regular links
      a.href = blob;

      if (a.origin !== location.origin) {
        corsEnabled(a.href) ? download(blob, name, opts) : click(a, a.target = '_blank');
      } else {
        click(a);
      }
    } else {
      // Support blobs
      a.href = URL.createObjectURL(blob);
      setTimeout(function () {
        URL.revokeObjectURL(a.href);
      }, 4E4); // 40s

      setTimeout(function () {
        click(a);
      }, 0);
    }
  } // Use msSaveOrOpenBlob as a second approach
  : 'msSaveOrOpenBlob' in navigator ? function saveAs(blob, name, opts) {
    name = name || blob.name || 'download';

    if (typeof blob === 'string') {
      if (corsEnabled(blob)) {
        download(blob, name, opts);
      } else {
        var a = document.createElement('a');
        a.href = blob;
        a.target = '_blank';
        setTimeout(function () {
          click(a);
        });
      }
    } else {
      navigator.msSaveOrOpenBlob(bom(blob, opts), name);
    }
  } // Fallback to using FileReader and a popup
  : function saveAs(blob, name, opts, popup) {
    // Open a popup immediately do go around popup blocker
    // Mostly only available on user interaction and the fileReader is async so...
    popup = popup || open('', '_blank');

    if (popup) {
      popup.document.title = popup.document.body.innerText = 'downloading...';
    }

    if (typeof blob === 'string') return download(blob, name, opts);
    var force = blob.type === 'application/octet-stream';

    var isSafari = /constructor/i.test(_global.HTMLElement) || _global.safari;

    var isChromeIOS = /CriOS\/[\d]+/.test(navigator.userAgent);

    if ((isChromeIOS || force && isSafari) && typeof FileReader !== 'undefined') {
      // Safari doesn't allow downloading of blob URLs
      var reader = new FileReader();

      reader.onloadend = function () {
        var url = reader.result;
        url = isChromeIOS ? url : url.replace(/^data:[^;]*;/, 'data:attachment/file;');
        if (popup) popup.location.href = url;else location = url;
        popup = null; // reverse-tabnabbing #460
      };

      reader.readAsDataURL(blob);
    } else {
      var URL = _global.URL || _global.webkitURL;
      var url = URL.createObjectURL(blob);
      if (popup) popup.location = url;else location.href = url;
      popup = null; // reverse-tabnabbing #460

      setTimeout(function () {
        URL.revokeObjectURL(url);
      }, 4E4); // 40s
    }
  });
  _global.saveAs = saveAs.saveAs = saveAs;

  if (typeof module !== 'undefined') {
    module.exports = saveAs;
  }
});
```
# 参考资料
[MediaDevices.getUserMedia()](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia)
[MediaRecorder](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaRecorder)
