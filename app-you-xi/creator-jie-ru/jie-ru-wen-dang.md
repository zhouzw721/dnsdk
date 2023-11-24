---
description: 当前页面用于完成Cocos Creator游戏发布Android版本的接入
---

# 接入文档

## 一、接入流程

### 1、SDK介绍

动能广告SDK提供广告、付费等能力给客户端集成。

附安卓模板工程：

下载链接：https://gui.vigame.cn/CP/Creator/CreatorDemoAndroid/CreatorDemoAndroid.zip

附Creator插件：

下载链接：https://gui.vigame.cn/CP/Creator/WbPlugin/Wb.zip

附Demo apk包：

下载链接：https://gui.vigame.cn/CP/Creator/CreatorDemo/CreatorDemo.apk

### 2、SDK接入流程

#### **2.1 准备**

模板工程、Wb插件、引擎导出的assets等资源、App Icon。

#### **2.2 接入流程**

##### **2.2.1 在项目中assets下添加Wb插件**

将文档附件中的Wb插件下载并解压至项目的assets目录下

<figure><img src="../../.gitbook/assets/Creator/1280X1280.png" alt=""><figcaption></figcaption></figure>

```typescript
//Wb插件导入代码示例
//以Scripts目录下的test.ts为例
import Wb from "../Wb/Define";

//打开广告
Wb.Ad.openAd("banner", null);
```

##### **2.2.2 替换assets资源**

将Cocos Creator引擎导出的资源替换至模板工程内（将模版工程中对应文件删除），如下图：

引擎导出的资源目录：

<figure><img src="../../.gitbook/assets/Creator/1280X1280 (1).png" alt=""><figcaption></figcaption></figure>

模板工程资源存放目录：

<figure><img src="../../.gitbook/assets/Creator/1280X1280 (2).png" alt=""><figcaption></figcaption></figure>

##### **2.2.3 替换libs下.so库**

根据项目本身需要的abi支持导出对应的.so文件并替换至模板工程libs目录下：

<figure><img src="../../.gitbook/assets/Creator/1280X1280 (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Creator/1280X1280 (4).png" alt=""><figcaption></figcaption></figure>

##### **2.2.4  修改游戏icon和名称**

<figure><img src="../../.gitbook/assets/Creator/1280X1280 (5).png" alt=""><figcaption></figcaption></figure>

## 二、常用接口

### 1.基础能力

#### 1.1 调用退出（必接）

```typescript
// 该市场是否支持退出
isSupportExit(): boolean;
// 退出接口
openExitGame(): void;

//示例
if (Wb.Pay.isSupportExit())
{
    Wb.Pay.openExitGame();
    return;
} else {
    //无退出界面，由游戏方自己实现
}
```

#### 1.2 获取应用基础参数

```typescript
let pname = Wb.Core.getPackageName();//获取包名
let androidid = Wb.Core.getAndroidId();//获取AndroidID或iOS的IDFV
let prjid = Wb.Core.getPrjid();//获取项目ID
let imsi = Wb.Core.getImsi();//获取imsi
let imei = Wb.Core.getImei();//获取imei
let lsn = Wb.Core.getLsn();//获取lsn
let appid = Wb.Core.getAppid();//获取appid
let channel = Wb.Core.getChannel();//获取渠道名
let vername = Wb.Core.getVerName();//获取app版本
let result = Wb.Core.getAuditSwitch();//获取审核开关，返回值：0-未审核 1-审核中
let state = Wb.Core.getNetState();//获取网络状态(0-无网络 1-手机网络 2-wifi网络 3-以太网络 4-蓝牙网络)
let result = Wb.Core.getCustomSwitch(inputValue);//获取审核开关，返回值：0-未审核 1-审核中（inputValue：自定义键值）
```

### 2.广告能力

#### 2.1 打开普通广告（插屏/视频/横幅/开屏）

```typescript
/// <param name="adName">广告位</param>
/// <param name="callback">回调</param>
openAd(adName: string, callback: Function = null): void;
```

示例：

```typescript
//打开banner广告
Wb.Ad.openAd("banner", null);

//打开插屏、视频
Wb.Ad.openAd("inter/video", function (result: boolean, adParam: Map<string, string>) {
    //打开广告回调
});
```

#### 2.2 打开原生广告

```typescript
/// <param name="adName">广告位</param>
/// <param name="width">显示区域宽</param>
/// <param name="height">显示区域高</param>
/// <param name="x">以屏幕的左上角为原点的x</param>
/// <param name="y">以屏幕的左上角为原点的y</param>
/// <param name="callback">回调</param>
openYsAd(adName: string, width: number, height: number, x: number, y: number, callback: Function): void;
```

示例：

```TypeScript
//坐标系转换部分可供参考或忽略
let size = this.testbtn.node.getContentSize();//panel的坐标转为屏幕坐标
let width = size.width;
let height = size.height;
//屏幕分辨率
let canvasSize = cc.view.getCanvasSize();
//scene的设计分辨率
let desiginSize = cc.view.getDesignResolutionSize();
//Fit width适配方案，以width作为缩放基准
let sW = (canvasSize.width / desiginSize.width);
//计算panel在屏幕中的尺寸
width = sW * width;
height = sW * height;
//转为世界坐标    
let worldPos =  Wb.Pos.localConvertWorldPointAR(this.testbtn.node);
let outPos = cc.v2();
//设置scene的camera
this.camera.getWorldToScreenPoint(worldPos, outPos);
//x,y即屏幕坐标，creator的坐标原点在坐下，Android的在左上，需要做个转换。x轴不变，y轴画布高减去y对应比例的值即为屏幕y坐标。（宽高都以sW为比例计算）
let x = (outPos.x * sW) - (width / 2);
let y = canvasSize.height - (outPos.y * sW) - (height / 2);

Wb.Ad.openYsAd(this.posName, width, height, x, y, function (result: boolean) {
    //打开回调
});
```

#### 2.3 查看广告是否就绪

```TypeScript
/// <summary>
/// 广告是否准备好
/// </summary>
/// <param name="adName">广告位</param>
isAdReady(adName: string): boolean;
```

#### 2.4 关闭广告

```TypeScript
/// <summary>
/// 关闭广告
/// </summary>
/// <param name="adName">广告位</param>
closeAd(adName: string): void;
```

#### 2.5 获取视频限制次数

```TypeScript
// 获取视频限制次数
getVideoLimitOpenNum(): number;
```

#### 2.6 某关卡是否出现广告

```TypeScript
/// <summary>
/// 关卡是否可以打开该广告
/// </summary>
/// <param name="adName">广告位</param>
/// <param name="level">关卡</param>
isAdBeOpenInLevel(adName: string, level: number): boolean;
```

### 3.统计能力

#### 3.1 自定义统计

```TypeScript
/// <summary>
/// 单独事件上报
/// </summary>
/// <param name="eventId">事件id，可根据统计文档填写</param>
tJCustomEvent(eventId: string): void;

/// <summary>
/// 携带标签上报
/// </summary>
/// <param name="eventId">事件id，可根据统计文档填写</param>
/// <param name="label">label内容</param>
tJCustomEventLabel(eventId: string, label: string): void;

/// <summary>
/// 多属性上报
/// </summary>
/// <param name="eventId">事件id，可根据统计文档填写</param>
/// <param name="attributes">attributes</param>
tJCustomEventHashMap(eventId: string, attributes: string): void;
```

> app_loading_show和app_home_show需要必接，以便于分析启动漏斗转化

| **事件名称**    | **事件ID**       | **触发时机**               | **附加参数** |
| --------------- | ---------------- | -------------------------- | ------------ |
| 游戏loading页面 | app_loading_show | 游戏引擎初始化后就触发上报 | 无           |
| 游戏内首页      | app_home_show    | 主页展示时                 | 无           |

#### 3.2 关卡统计（必接）

```TypeScript
/// <summary>
/// 关卡开始
/// </summary>
/// <param name="level">关卡id</param>
startLevel(level: string): void;

/// <summary>
/// 关卡胜利
/// </summary>
/// <param name="level">关卡id</param>
finishLevel(level: string, score: string): void;

/// <summary>
/// 关卡失败
/// </summary>
/// <param name="level">关卡id</param>
failLevel(level: string, score: string): void;
```

#### 3.3 示例代码

```TypeScript
import Wb from "./Wb/Define"

export default class SampleTj{

    //进入游戏loading页面
    public static LoadingShow()
    {
        Wb.Tj.tJCustomEvent("app_loading_show");        
    }

    //进入游戏主界面
    public static HomeShow()
    {
        Wb.Tj.tJCustomEvent("app_home_show");        
    }

    //广告位曝光
    /// <param name="ad_pos">广告位名称</param>
    public static VideoReady(ad_pos:any){
        var ps =
        {
            "ad_pos": ad_pos,
        };
        Wb.Tj.tJCustomEventHashMap("ad_video_ready", JSON.stringify(ps));
    }

    //进入关卡统计
    /// <param name="level">当前关卡ID</param>
    /// <param name="type">进入关卡的方式</param>   play，video（直接进入，看视频进入）
    /// <param name="RainbowJolt">是否使用彩虹</param>
    /// <param name="ThunderJolt">是否使用闪电</param>
    public static LevelStarted(level:number, type:any, RainbowJolt:boolean, ThunderJolt:boolean)
    {
        var ps =
        {
            "level": level,
            "type": type,
            "RainbowJolt": RainbowJolt?"1":"0",
            "ThunderJolt":ThunderJolt?"1":"0"
        };
        Wb.Tj.tJCustomEventHashMap("self_levelstarted", JSON.stringify(ps));
    }

    //结束关卡统计
    /// <param name="level">当前关卡ID</param>
    /// <param name="pass">是否通关</param>
    /// <param name="score">结束时的分数</param>
    /// <param name="star">结束时的星星数</param>
    /// <param name="bubble">剩余泡泡数</param>
    public static LevelCompleted(level:number, pass:boolean, score:number, star:number,bubble:number)
    {
        var ps =
        {
            "level": level,
            "pass": pass?"1":"0",
            "score": score,
            "star":star,
            "bubble":bubble
        };
        Wb.Tj.tJCustomEventHashMap("self_levelcompleted", JSON.stringify(ps));
    }

    //签到统计
    /// <param name="day">当前签到天数</param>
    /// <param name="type">签到方式</param>   collect，doublecollect（单倍领取，双倍领取）
    public static Sign(day:number, type:any)
    {
        var ps =
        {
            "day": day,
            "type": type
        };
        Wb.Tj.tJCustomEventHashMap("self_sign", JSON.stringify(ps));
    }

    //卡片收集统计
    /// <param name="name">卡片名字</param>   Tools，IceCream，Flowers.......
    /// <param name="number">数量</param>
    public static Card(name:any, number:number)
    {
        var ps =
        {
            "name": name,
            "number": number
        };
        Wb.Tj.tJCustomEventHashMap("self_card", JSON.stringify(ps));
    }

    //购买成功--新手特惠包
    public static Beginner(){
        Wb.Tj.tJCustomEvent("self_Beginner");  
    }

    //购买成功--初级射手包
    public static Shooter(){
        Wb.Tj.tJCustomEvent("self_Shooter");  
    }

    //购买成功--超级冲关包
    public static Super(){
        Wb.Tj.tJCustomEvent("self_Super");  
    }

    //购买成功--高手超值包
    public static Mega(){
        Wb.Tj.tJCustomEvent("self_Mega");  
    }

    //购买成功--巨星划算包
    public static Giant(){
        Wb.Tj.tJCustomEvent("self_Giant");  
    }

    //购买成功--传奇王者包
    public static Legend(){
        Wb.Tj.tJCustomEvent("self_Legend");  
    }    

    //购买成功--金币20
    public static coin20(){
        Wb.Tj.tJCustomEvent("self_coin20");  
    } 

    //购买成功--金币50
    public static coin50(){
        Wb.Tj.tJCustomEvent("self_coin50");  
    }    

    //购买成功--金币100
    public static coin100(){
        Wb.Tj.tJCustomEvent("self_coin100");  
    }   

    //购买成功--金币240
    public static coin240(){
        Wb.Tj.tJCustomEvent("self_coin240");  
    }       

    //购买成功--金币500
    public static coin500(){
        Wb.Tj.tJCustomEvent("self_coin500");  
    }  

    //购买成功--金币1100
    public static coin1100(){
        Wb.Tj.tJCustomEvent("self_coin1100");  
    }  

    //关卡统计
    //进入关卡
    /// <param name="level">关卡ID</param>
    public static StartLevel(level:number){
        Wb.Tj.startLevel(level.toString());   
    }

    //关卡成功
    /// <param name="level">关卡ID</param>
    /// <param name="score">分数</param>
    public static FinishLevel(level:number,score:number){
        Wb.Tj.finishLevel(level.toString(),score.toString());   
    }

    //关卡失败
    /// <param name="level">关卡ID</param>
    /// <param name="score">分数</param>
    public static FailLevel(level:number,score:number){
        Wb.Tj.failLevel(level.toString(),score.toString());   
    }

}
```

### 4.支付能力（有支付必接）

#### 4.1 发起支付（必接）

```TypeScript
/**
 * 内购统计支付
 * @param isUse 是否使用计费点配置，false不使用、true使用
 * @param info 支付参数
 * @param func 回调
 */
orderPay6(isUse: boolean, info: Object, func: Function): void
```

##### 4.1.1 不使用计费点配置支付示例

```TypeScript
//支付参数
let obj = {
    "id":"test1",
    "price":"100",
    "payCode":"testcode",
    "payDesc":"珍贵宝箱",
    "giftId":"giftId",
    "giftType":"giftType",
    "giftNum":"1",
    "levelId":"levelId",
    "tokenType":"1",
    "userdata":"userdata",
    "custom":"custom",
    "extParams": {
        "value1":"自定义参数1",//自定义参数
        "value2":"自定义参数2" //自定义参数
    }
};
 Wb.Pay.orderPay6(false, obj, (ret, payId, userData, orderId)=>
    {
        console.log("支付(不用计费配置) ret = " + ret);
        if (ret == 1) {
            Wb.T.show("支付成功");
            console.log("PayManager 内购统计支付(不用计费配置) 成功 payId = " + payId + ", userData = " + userData + ", orderId = " + orderId);
            var rand = 1 + Math.round(Math.random() * 99);
            console.log("PayManager 内购统计支付(不用计费配置) pay_token_change 购买前代币数量 before_token = " + rand);
            console.log("PayManager 内购统计支付(不用计费配置) pay_token_change 购买后代币数量 before_token = " + (rand + 1000));
            var obj = {
                "appid":Wb.Core.getAppid(),
                "pid": Wb.Core.getPrjid(),
                "level_id": "1",
                "token_type": "1",
                "before_token": "" + rand,
                "after_token": "" + (rand + 1000),
                "add_reduce": "1000",
                "reason": "1001"
            };
            Wb.Tj.tJCustomEventHashMap("pay_token_change", JSON.stringify(obj));
            console.log("PayManager 内购统计支付(不用计费配置) pay_on_account 上报 orderId = " + orderId);
            Wb.Tj.tJPayOnAccount(orderId);
        } else if (ret == 0){
            Wb.T.show("支付失败");
        } else if (ret == 2){
            Wb.T.show("支付取消");
        } else {
            Wb.T.show("网络错误，需做补单处理");
        }
    });
```

##### 4.1.2 使用计费点配置支付示例

```TypeScript
//支付参数
//price、payCode、payDesc字段在使用计费点配置时可不传
let obj = {
    "id":"1001",
    "giftId":"giftId",
    "giftType":"giftType",
    "giftNum":"1",
    "levelId":"levelId",
    "tokenType":"1",
    "userdata":"userdata",
    "custom":"custom",
    "extParams": {
        "value1":"自定义参数1",//自定义参数
        "value2":"自定义参数2" //自定义参数
    }
好l};

Wb.Pay.orderPay6(true, obj, (ret, payId, userData, orderId)=>{
        console.log("支付ret = " + ret);
        if (ret == 1) {
            Wb.T.show("支付成功");
            console.log("PayManager 内购统计支付(用计费配置)成功 payId = " + payId + ", userData = " + userData + ", orderId = " + orderId);
            var rand = 1 + Math.round(Math.random() * 99);
            console.log("PayManager 内购统计支付(用计费配置) pay_token_change 购买前代币数量 before_token = " + rand);
            console.log("PayManager 内购统计支付(用计费配置) pay_token_change 购买后代币数量 before_token = " + (rand + 1000));
            var obj = {
                "appid":Wb.Core.getAppid(),
                "pid": Wb.Core.getPrjid(),
                "level_id": "1",
                "token_type": "1",
                "before_token": "" + rand,
                "after_token": "" + (rand + 1000),
                "add_reduce": "1000",
                "reason": "1001"
            };
            Wb.Tj.tJCustomEventHashMap("pay_token_change", JSON.stringify(obj));
            console.log("PayManager 内购统计支付(用计费配置) pay_on_account 上报 orderId = " + orderId);
            Wb.Tj.tJPayOnAccount(orderId);
        } else if (ret == 0) {
            Wb.T.show("支付失败");
        } else if (ret == 2){
            Wb.T.show("支付取消");
        } else {
            Wb.T.show("网络错误，需做补单处理");
        }
    });
```

#### 4.2 支付成功后上报事件（必接）

```TypeScript
/**
* 内购统计充值到账上报
* @param tradeId 订单id
*/
tJPayOnAccount(tradeId: string): void;
```

#### 4.3 设置补单回调（必接）

```TypeScript
// 设置补单监听回调
setInventoryCallBack(func: Function): void;

//示例
Wb.Pay.setInventoryCallBack((isSuc, payId, userData, orderId)=>{
    if (isSuc) {
        console.log("补单成功 payId = " + payId + ", userData = " + userData + ", orderId = " + orderId);
        var rand = 1 + Math.round(Math.random() * 99);
        console.log("pay_token_change 购买前代币数量 before_token = " + rand);
        console.log("pay_token_change 购买后代币数量 before_token = " + (rand + 1000));
        var obj = {
            "appid":Wb.Core.getAppid(),
            "pid": Wb.Core.getPrjid(),
            "level_id": "1",
            "token_type": "1",
            "before_token": "" + rand,
            "after_token": "" + (rand + 1000),
            "add_reduce": "1000",
            "reason": "1001"
        };
        Wb.Tj.tJCustomEventHashMap("pay_token_change", JSON.stringify(obj));
        console.log("PayManager setInventoryCallBack pay_on_account 上报 orderId = " + orderId);
        Wb.Tj.tJPayOnAccount(orderId);
    } else {
        console.log("PayManager setInventoryCallBack 补单失败 payId = " + payId + ", userData = " + userData + ", orderId = " + orderId);
    }
});
```

### 5.扩展能力

#### 5.1 反馈邮箱（推荐）

在设置界面显示邮箱信息，获取到数据就显示，没有就不显示。可以在ConfigVigame.xml中配置客服邮箱信息。

接口

```TypeScript
// 根据key获取ConfigVigame.xml中value
getConfigVigameValue(key: string): string;
```

配置示例

```C#
<Email>123321@gmail.com</Email>
```

示例

```CSS
//get email config from ConfigVigame.xml
let ret = Wb.Core.getConfigVigameValue("Email");
if (ret.length == 0)
{
   //not have the Email config
}
else
{
    // have the Email config show the config on Setting.

}
```

#### 5.2 客服中心

```TypeScript
/// <summary>
/// 打开客服中心
/// </summary>
/// <param name="title">标题</param>
/// <param name="loginId">用户id</param>  
openClientCenter(title: string, loginId: string);
```

#### 5.2 隐私政策和用户协议（必接）

```TypeScript
// 隐私政策
openPrivacyPolicy(): void;
// 用户协议
openUserAgreement(): void;

// 调用示例
Wb.Core.openPrivacyPolicy();
Wb.Core.openUserAgreement();
```