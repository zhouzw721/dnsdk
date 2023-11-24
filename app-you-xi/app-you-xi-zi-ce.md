---
description: 当前页面引导您完成App游戏的自测，以确保SDK接入正确无误
---

# App游戏自测

## Android

### 一、隐私政策和用户协议（必接）

在手机上点击用户协议或隐私政策后，出现如下弹框：

<div align="left">

<figure><img src="../.gitbook/assets/image (12).png" alt="" width="150"><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (11).png" alt="" width="167"><figcaption></figcaption></figure>

</div>

### 二、统计上报

**1、初始化事件（必接）**

| **事件名称**    | **事件ID**           | **触发时机**      | **附加参数** |
| ----------- | ------------------ | ------------- | -------- |
| 游戏loading页面 | app\_loading\_show | 游戏引擎初始化后就触发上报 | 无        |
| 游戏内首页       | app\_home\_show    | 主页展示时         | 无        |

触发事件，过滤tj-dnstatistics，有save: {事件名}即可，如下图：

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

**2、关卡事件（必接）**

触发关卡开始、关卡胜利、关卡失败，过滤tj-dnstatistics，有save: um\_plus\_game\_level即可，如下图：![](<../.gitbook/assets/image (15).png>)

### 三、广告能力（必接）

打开对应类型广告时能展示正确的广告样式即可，广告样式详情见下方截图：



<div>

<figure><img src="../.gitbook/assets/image (16).png" alt="" width="193"><figcaption><p>开屏广告样式</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (17).png" alt="" width="155"><figcaption><p>激励视频广告样式</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (18).png" alt="" width="196"><figcaption><p>banner广告样式</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (19).png" alt="" width="154"><figcaption><p>普通插屏广告样式</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption><p>嵌入式广告样式</p></figcaption></figure>

</div>

信息流广告样式，出现以下类似的广告样式即可：

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

### 四、oppo、vivo反馈邮箱

oppo、vivo渠道需要在设置界面或者外接一个界面显示一个反馈邮箱（点击不用跳转）

<img src="../.gitbook/assets/email1.png" alt="img_v3_025c_28090b2f-4ae9-4e03-8298-240614ec398g" style="zoom:65%;" /><img src="../.gitbook/assets/email2.png" alt="img_v3_025c_8d11e370-a9e8-47c0-ac55-50a519508a0g" style="zoom:50%;" />

### 五、oppo更多精彩

oppo渠道需要再游戏结算界面或者其他二级子界面展示更多精彩的按钮，点击后能正常跳转oppo超休游戏中心

<img src="../.gitbook/assets/more.png" alt="img_v3_025c_08579fd9-be9d-4b10-88ad-a94fec807e2g" style="zoom:67%;" />

## IOS

### 一、隐私政策和用户协议（必接）

在手机上点击用户协议或隐私政策后，出现如下弹框：



<div>

<figure><img src="../.gitbook/assets/image (23).png" alt="" width="164"><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (22).png" alt="" width="164"><figcaption></figcaption></figure>

</div>

### 二、统计上报

**1、初始化事件（必接）**

| **事件名称**    | **事件ID**           | **触发时机**      | **附加参数** |
| ----------- | ------------------ | ------------- | -------- |
| 游戏loading页面 | app\_loading\_show | 游戏引擎初始化后就触发上报 | 无        |
| 游戏内首页       | app\_home\_show    | 主页展示时         | 无        |

触发事件，过滤tj-kafka，有event\_name : {事件名}即可，如下图：

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

**2、关卡事件（必接）**

触发关卡开始、关卡胜利、关卡失败，过滤tj-dnstatistics，有event\_name : um\_plus\_game\_level即可，如下图

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

### 三、广告能力（必接）

打开对应类型广告时能展示正确的广告样式即可，广告样式详情见下方截图：

<table><thead><tr><th width="188">广告样式</th><th width="185.33333333333331">截图</th></tr></thead><tbody><tr><td>banner</td><td><img src="../.gitbook/assets/image (26).png" alt="" data-size="original"></td></tr><tr><td>激励视频</td><td><img src="../.gitbook/assets/image (27).png" alt="" data-size="original"></td></tr><tr><td>插屏视频</td><td><img src="../.gitbook/assets/image (28).png" alt="" data-size="original"></td></tr><tr><td>插屏</td><td><img src="../.gitbook/assets/image (29).png" alt="" data-size="original"></td></tr><tr><td>msg</td><td><img src="../.gitbook/assets/image (30).png" alt="" data-size="original"></td></tr><tr><td>开屏</td><td><img src="../.gitbook/assets/image (31).png" alt="" data-size="original"></td></tr></tbody></table>
