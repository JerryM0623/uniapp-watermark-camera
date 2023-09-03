# uniapp-watermark-camera

## 这是什么?

这个仓库的作用主要是为了技术调研，在公司项目开发中需要使用一个水印相机的需求，对于这个需求目前的个人开发生涯中并没有遇见过。所以为了更好的工作，进行了一定时间的技术调研。这个仓库主要针对的是使用**uniapp编写微信小程序**的情况下通过原生内置的`camera` 组件实现图片的拍摄以及水印的添加最终将其保存到系统相册整个过程。代码仅作调研使用，可以在工作开发过程中借鉴，但是请勿直接复制粘贴上线生产环境。

## 如何运行?

运行方式如下：

1. `fork` 或者`clone` 本仓库，然后使用`git`工具将仓库克隆到电脑本地

   ```shell
   git clone git@github.com:JerryM0623/uniapp-watermark-camera.git
   ```

   

2. 如果你需要的是稳定的版本，请直接查看`main` 分支代码即可。
   如果您追求更新的代码，请将分支切换到`develop` 分支，然后再查看代码。

   ```shell
   git checkout -b develop origin/develop
   ```

   

3. 注意，运行这个仓库需要使用【微信开发者工具】以及【HBuilderX】两种开发工具，请确保您的设备上安装了这两个开发工具。下面提供了官方下载页面，如若需要请自行跳转下载安装

   > 微信开发者工具：[微信开发者工具下载地址与更新日志 | 微信开放文档 ](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)
   > HBuilderX：[HBuilderX-下载 ](https://www.dcloud.io/hbuilderx.html)

4. 在 HBuilderX的顶部菜单【运行-运行到小程序模拟器-微信开发者工具】系统便会自动进行编译及以来的下载，并在微信开发者工具中运行小程序。
   **注：由于内置相机组件的特殊性，建议在真机上进行预览。**