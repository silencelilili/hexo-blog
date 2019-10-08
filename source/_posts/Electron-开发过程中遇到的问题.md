---
title: Electron 开发过程中遇到的问题
date: 2019-09-29 18:51:07
tags: "Electron"
categories: "Electron"
---

##### 1. 私有云平台私有自签发证书不安全的问题
在Electron应用的main.js文件配置以下代码
// 忽略证书相关错误 在ready前使用

```
app.commandLine.appendSwitch('ignore-certificate-errors')
```


> 因为自签名的CA不会被浏览认可，因此需要把Chrome的忽略证书相关错误命令开启，但不用担心你的数据不会被加密，只要你的证书配置正确，浏览器都会为你加密传输的


##### 2. Electron渲染进程，出现‘require is not defined’的问题
> 问题描述：electron@5.x以上版本，在渲染进程中会报require is not defined 的错误
错误提示：

```
index.html:20 Uncaught ReferenceError: require is not defined
    at index.html:20
```



> 解决方法：开启BrowserWindow的nodeIntegration: true。

```
new BrowserWindow({
    width: 1000,
    height: 600,
    webPreferences: {
        nodeIntegration: true // 是否完整支持node, electron@5.0默认为false
    }
})
```


> 因为最新的electron@5.0系列中，这个nodeIntegration参数，默认改成false了。而在以前版本的electron中，这个nodeIntegration参数，默认为true。

##### 3. Electron启动出现短暂的白屏
> 解决方法：创建窗体时先隐藏show: false，初始化后再显示 mainWindow.show()

```
mainWindow = new BrowserWindow({
    height: 600,
    width: 960,
    frame: false,
    minWidth: 710,
    minHeight: 500,
    offscreen: true,
    webPreferences: {webSecurity: false},
    resizable: true,
    skipTaskbar: false,
    flashFrame: true,
    show: false // 先隐藏
  })
  mainWindow.openDevTools()
  mainWindow.loadURL(winURL)
  mainWindow.on('ready-to-show', function () {
    mainWindow.show() // 初始化后再显示
  })
```


##### 4. Electron 如何禁用本地缓存

```
const app = electron.app
//...
app.commandLine.appendSwitch("--disable-http-cache");
//...
app.on('ready', createWindow)
```

> 这句关键代码app.commandLine.appendSwitch("--disable-http-cache")，需要放置在ready事件之前。
