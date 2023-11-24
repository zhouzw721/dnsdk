---
description: 当前页面将引导您成功将SDK接入到游戏中，请仔细阅读
---

# 💡 接入指南

## 接入流程

获取SDK=>能力选择=>SDK接入=>自测=>正式交付

## 获取SDK

<table data-header-hidden><thead><tr><th>平台</th><th width="130">类型</th><th width="85">版本</th><th width="117">更新日期</th><th width="140">获取链接</th><th>备注</th></tr></thead><tbody><tr><td><strong>平台</strong></td><td><strong>类型</strong></td><td><strong>版本</strong></td><td><strong>更新日期</strong></td><td><strong>获取链接</strong></td><td><strong>备注</strong></td></tr><tr><td>App游戏<br></td><td>Unity<br></td><td>v2.1.9</td><td>2023.10.19</td><td><a href="#/app-you-xi/unity-jie-ru/sdk-xia-zai.md">SDK下载</a></td><td>安装unity插件，在插件内可以导出Android和ios工程以及获取文档</td></tr><tr><td></td><td>Creator</td><td>v1.0.0</td><td>2023.11.08</td><td><a href="#/app-you-xi/creator-jie-ru/sdk-xia-zai.md">SDK下载</a></td><td>下载Creator插件，解压后放入工程assets目录下随资源一起构建</td></tr><tr><td></td><td>Android</td><td>-</td><td>-</td><td>-</td><td></td></tr><tr><td></td><td>iOS</td><td>-</td><td>-</td><td>-</td><td></td></tr><tr><td>小游戏</td><td>Unity小游戏</td><td>v2.2.3</td><td>2023.10.31</td><td><a href="#/xiao-you-xi/unity-jie-ru/sdk-xia-zai.md">SDK下载</a></td><td>-</td></tr><tr><td></td><td>H5小游戏</td><td>v5.13.1</td><td>2023.10.31</td><td><a href="#/xiao-you-xi/h5-jie-ru/sdk-xia-zai.md">SDK下载</a></td><td></td></tr></tbody></table>

## 能力选择

### **App游戏**

<table data-header-hidden><thead><tr><th></th><th width="140"></th><th></th><th width="252"></th><th></th></tr></thead><tbody><tr><td><strong>功能模块</strong></td><td><strong>子功能</strong></td><td><strong>接入要求</strong></td><td><strong>说明</strong></td><td><strong>文档附录</strong></td></tr><tr><td>闪屏背景图</td><td><br></td><td>安卓必接</td><td>修改res/drawble/bg_splash_vigame.png</td><td><br></td></tr><tr><td>广告<br></td><td><br></td><td>必接</td><td>支持多家聚合广告，激励视频/全屏视频/开屏（非模板）/信息流（模板）/原生banner（非模板）广告</td><td><br></td></tr><tr><td>统计<br></td><td>关卡统计<br></td><td>必接<br></td><td>用于分析关卡数据<br></td><td><br></td></tr><tr><td>自定义事件</td><td>必接</td><td>用于分析游戏行为</td><td><br></td><td></td></tr><tr><td>支付<br></td><td><br></td><td>IAP必接</td><td>安卓支持支付宝、微信支付、联运渠道支付，iOS支持Apple pay支付；</td><td><br></td></tr><tr><td>签名验证</td><td><br></td><td>必接</td><td>用于防止他人二次打包</td><td><br></td></tr><tr><td>商店联运<br></td><td>退出弹框</td><td>必接</td><td>商店审核要求</td><td><br></td></tr><tr><td>反馈邮箱</td><td>安卓必接</td><td>商店审核要求</td><td><br></td><td></td></tr><tr><td>更多精彩</td><td>Oppo联运必接</td><td>商店审核要求</td><td><br></td><td></td></tr><tr><td>增值功能</td><td>兑换码</td><td>选接</td><td>有内购产品建议接入</td><td><br></td></tr><tr><td><br></td><td>在线参数</td><td>选接</td><td>用于在线控制游戏功能</td><td><br></td></tr><tr><td><br></td><td>三方账号登录</td><td>选接<br></td><td>用于绑定微信、华为、oppo、vivo、小米等账号</td><td><br></td></tr></tbody></table>

### **小游戏**

<table data-header-hidden><thead><tr><th></th><th width="127"></th><th width="172"></th><th width="202"></th><th></th></tr></thead><tbody><tr><td>功能模块</td><td>子功能</td><td>接入要求</td><td>说明</td><td>文档附录</td></tr><tr><td>初始化</td><td><br></td><td>必接</td><td><br></td><td><br></td></tr><tr><td>广告<br></td><td><br></td><td>必接</td><td>支持banner、插屏、激励视频等小游戏广告类型</td><td><br></td></tr><tr><td>统计</td><td>自定义事件</td><td>必接</td><td>用于分析游戏行为</td><td><br></td></tr><tr><td>关卡统计</td><td>必接</td><td>用于分析关卡数据</td><td><br></td><td></td></tr><tr><td>转化行为统计</td><td>必接</td><td>用于投放侧计算激活</td><td><br></td><td></td></tr><tr><td>支付<br></td><td><br></td><td>IAP必接</td><td>安卓支持支付宝、微信支付、联运渠道支付，iOS支持Apple pay支付；</td><td><br></td></tr><tr><td>增值功能</td><td>分享</td><td>选接</td><td>在线获取分享内容并调用小游戏分享接口</td><td><br></td></tr><tr><td><br></td><td>在线参数</td><td>选接</td><td>用于在线控制游戏功能</td><td><br></td></tr></tbody></table>

## SDK接入

* 您可以参考接入指引文档，导入SDK并集成到您的项目中

<table data-header-hidden><thead><tr><th width="196"></th><th></th></tr></thead><tbody><tr><td><strong>类型</strong></td><td><strong>文档</strong></td></tr><tr><td>Unity</td><td><a href="#/app-you-xi/unity-jie-ru/jie-ru-wen-dang.md">Unity接入</a></td></tr><tr><td>Creator</td><td><a href="#/app-you-xi/creator-jie-ru/">Creator接入</a></td></tr><tr><td>Unity微信小游戏</td><td><a href="#/xiao-you-xi/unity-jie-ru/jie-ru-wen-dang-wei-xin-xiao-you-xi.md">接入文档-微信小游戏</a></td></tr><tr><td>Unity抖音小游戏</td><td><a href="#/xiao-you-xi/unity-jie-ru/jie-ru-wen-dang-dou-yin-xiao-you-xi.md">接入文档-抖音小游戏</a></td></tr></tbody></table>

## 自测

{% hint style="info" %}
接入广告和支付时，可先使用demo的预置参数进行简单测试。简单测试后，需要更换为动能的正式参数进一步测试。正式参数由动能运营提供：

* 使用广告能力时，运营提供正式的广告位名称
* 使用支付能力时，运营提供礼包ID
{% endhint %}

**App游戏自测**

[App接入后自测](app-you-xi/app-you-xi-zi-ce.md)

**小游戏自测**

[小游戏自测](xiao-you-xi/xiao-you-xi-zi-ce.md)

## 正式交付

**交付前验收**

- App游戏提供测试apk给测试同学  
- 小游戏提供体验版/开发版二维码给测试同学

**正式交付**

按如下要求交付出包工程，后续由动能进行出包和上架。

* 安卓：AndroidStudio工程
* iOS：xcode工程+证书文件
* 小游戏无需交付工程，自行发版即可
