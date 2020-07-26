## 美信拓扑IM uniapp版

[美信拓扑](https://www.maximtop.com/)，一键启用多云架构的即时通讯云服务

美信拓扑IM为美信拓扑云服务的DemoApp，方便 App 开发者体验和使用 IM SDK，可以在官网[下载页面](https://www.maximtop.com/downloads/)选择试用所有客户端。

本工程为Uniapp工程，实际上是从[微信小程序](https://github.com/maxim-top/maxim-miniprogram)转换而来，如有问题也可以参照小程序版本。

感谢 [DCloud.io](https://dcloud.io) 和 [zhangdaren](https://github.com/zhangdaren/miniprogram-to-uniapp)，转换过程非常顺畅。

此工程共有四个源码目录：

1. im 存放美信拓扑IM SDK，当前最新版本为 floo-2.0.0.uniapp.js
2. pages 为 UI 源码目录；
3. utils 为使用的工具类源码；
4. third 为第三方源码；

## 开发准备 

1. 运行命令安装依赖包

> npm install

2. 美信拓扑 AppID

打开文件 App.vue, 修改变量 appid，将 "welovemaxim" 改为你的应用AppID，此 AppID 为在[美信拓扑后台](https://console.maximtop.com/)创建应用后获取。

3. 如果开发小程序，还需修改对应小程序平台的 AppID

如微信小程序，可以打开文件 manifest.json，修改其中的 AppID 为你的小程序在微信后台的 appid。

修改以上信息后，可以直接通过HBuilder（Uniapp IDE）发版了，好好玩吧。

了解更多信息可以阅读[在线文档](https://www.maximtop.com/docs/)，或者在本仓库提问 :)
