---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 接入文档-抖音小游戏

### 宿主支持现状

> native方案只支持Android，但是性能更好。WebGL可以支持iOS和Android双端，性能会低一些。建议可以Android上native方案，iOS上WebGL方案。

| **方案类型**   | **支持平台**      | **支持宿主**                  |
| ---------- | ------------- | ------------------------- |
| **原生方案**   | Android       | 抖音、抖音极速版、头条、头条极速版、火山视频    |
| **Wasm方案** | Android / IOS | 仅支持抖音，抖音极速版 (android/ios) |

### 对接流程

{% hint style="info" %}
<mark style="color:red;">**SDK接入完毕后请发送二维码到对接群，测试验收通过后再提审发布！！！**</mark>
{% endhint %}

### 环境搭建

根据接入文档[StarkSDK\_Unity文档](https://bytedance.feishu.cn/docx/doxcnTom4J47auHMnkjGYMBaNnZ) 安装抖音小游戏插件。[Uid获取方法](https://bytedance.feishu.cn/docs/doccnxQA717G3W9Ovm2RsW1VcAg)

### SDK接入

{% hint style="info" %}
<mark style="color:red;">**`Unity`**</mark><mark style="color:red;">**工程中添加**</mark><mark style="color:red;">**`DY_MINI_GAME`**</mark><mark style="color:red;">**宏定义，将**</mark><mark style="color:red;">**`DN_MINI_PLUGIN_UNITY.unitypackage`**</mark><mark style="color:red;">**导入工程。**</mark>
{% endhint %}

**工程配置**

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

### 接入准备

**域名加白**

使用下列域名

```
https://c.vigame.cn
https://p.vigame.cn
https://u.vigame.cn
https://r.vigame.cn
https://x.vigame.cn
```

**配置抖音登录参数（抖音资质审核通过后才可以获取正式的openid）以及支付回调参数**

> 联系运营同学配置

### SDK初始化（必接）

{% hint style="danger" %}
<mark style="color:red;">**初始化接口要在游戏执行的第一个脚本就调用初始化，以免无法监听到抖音**</mark><mark style="color:red;">**`Stark SDK`**</mark><mark style="color:red;">**本身的初始化回调！！！**</mark>
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 抖音小游戏sdk初始化
/// </summary>
/// <param name="appid">动能appid</param>
/// <param name="appKey">动能appkey</param>
/// <param name="callBack">初始化回调bool：是否初始化成功；string：openId；string：userCode</param>
/// <param name="payRate">根据小游戏平台配置支付时设置的支付比例（1：1、1：10、1：100）确认传1、10、100</param>
/// <param name="isRelax">是否为超休游戏，true切换抖音账号不清空本地缓存，false切换抖音账号清空本地缓存</param>
public void DouYinInit(string appid, string appKey, Action<bool, string, string> callBack, MiniPayRate payRate = MiniPayRate.Shi,  bool isRelax = false)
```
{% endcode %}

示例

{% code lineNumbers="true" %}
```csharp
Wb.CoreManager.Instance.DouYinInit("动能appid", "动能appkey", (ret, openId, userCode) =>
{
    if (ret)
    {
        Debug.Log("DNSDK: 初始化成功 openId: " + openId + ", userCode: " + userCode);
    }
    else
    {
        Debug.Log("DNSDK: 初始化失败 openId: " + openId + ", userCode: " + userCode);
    }
},  Wb.MiniPayRate.Shi);
```
{% endcode %}

### 统计（必接）

根据统计埋点文件使用合适接口上报游戏中产生的事件。

#### 1. 自定义事件（必接）

接口说明

{% code lineNumbers="true" %}
```csharp
public void TJCustomEvent(string eventId)
public void TJCustomEvent(string eventId, string label)
public void TJCustomEvent(string eventId, Dictionary<string, string> attributes)
```
{% endcode %}

事件说明

| **事件名称**    | **事件ID**                    | **触发时机**      | **附加参数** |
| ----------- | --------------------------- | ------------- | -------- |
| 游戏loading页面 | <p>app_loading_show<br></p> | 游戏引擎初始化后就触发上报 | 无        |
| 游戏内首页       | app\_home\_show             | 主页展示时         | 无        |

使用示例

{% code lineNumbers="true" %}
```csharp
//示例
Wb.TjManager.Instance. TJCustomEvent("app_loading_show")；
Wb.TjManager.Instance. TJCustomEvent("app_home_show")；
```
{% endcode %}

#### 2. 转化行为上报（必接）

{% hint style="info" %}
<mark style="color:red;">**注意：转化行为上报使用**</mark><mark style="color:red;">**`TjSendTrackingEvent`**</mark><mark style="color:red;">**接口！！！**</mark>

<mark style="color:red;">**转化行为**</mark><mark style="color:red;">**`active`**</mark><mark style="color:red;">**、**</mark><mark style="color:red;">**`active_register`**</mark><mark style="color:red;">**和**</mark><mark style="color:red;">**`next_day_open`**</mark><mark style="color:red;">**事件必接，事件类型为**</mark><mark style="color:red;">**`tracking`**</mark><mark style="color:red;">**！！！**</mark>

<mark style="color:red;">**SDK会本地缓存埋点记录，再上报会进行拦截。**</mark>
{% endhint %}

接口说明

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 转化行为上报
/// </summary>
/// <param name="eventId">事件id</param>
/// <param name="val">额外参数json字符串</param>
public void TjSendTrackingEvent(string eventId, string val = "")
```
{% endcode %}

| **事件id**                  | **事件定义**      | **data额外携带数据** | **建议上报时机**                           |
| ------------------------- | ------------- | -------------- | ------------------------------------ |
| active(自实现上报必接)           | 激活            | <p><br></p>    | <p>首次进入主页触发<br></p>                  |
| active\_register(自实现上报必接) | <p>注册<br></p> | <p><br></p>    | 完成角色创建后触发。如果没有角色创建过程，可跟active事件同时触发。 |
| next\_day\_open (自实现上报必接) | 次留            | <p><br></p>    | 用户激活后次日（第二个自然日）在互联网环境下打开小游戏时触发       |

使用示例

{% code lineNumbers="true" %}
```csharp
Wb.TjManager.Instance.TjSendTrackingEvent("active")；
Wb.TjManager.Instance.TjSendTrackingEvent("active_register")；
Wb.TjManager.Instance.TjSendTrackingEvent("next_day_open")；
```
{% endcode %}

#### 3.关卡统计上报（必接）

接口说明

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 关卡开始
/// </summary>
/// <param name="level">关卡id</param>
public void StartLevel(string level)

/// <summary>
/// 关卡获胜
/// </summary>
/// <param name="level">关卡id</param>
/// <param name="score">得分</param>
public void FinishLevel(string level, string score)

/// <summary>
/// 关卡失败
/// </summary>
/// <param name="level">关卡id</param>
/// <param name="score">得分</param>
public void FailLevel(string level, string score)
```
{% endcode %}

事件说明

| **事件id** | **事件定义** | **data额外携带数据**        | **建议上报时机** |
| -------- | -------- | --------------------- | ---------- |
| begin    | 开始       | levelid:关卡ID          | 进入关卡       |
| complete | 胜利       | levelid:关卡IDscore:得分  | 关卡胜利       |
| fail     | 失败       | levelid:关卡IDscore: 得分 | 关卡失败       |

使用示例

{% code lineNumbers="true" %}
```csharp
Wb.TjManager.Instance.StartLevel("3-1");
Wb.TjManager.Instance.FinishLevel("3-1"， "100");
Wb.TjManager.Instance.FailLevel("3-1"， "100");
```
{% endcode %}

#### 4.用户行为统计上报

| **事件id**             | **事件定义**         | **data额外携带数据**                                          | **建议上报时机**               |
| -------------------- | ---------------- | ------------------------------------------------------- | ------------------------ |
| online               | 在线时长             | duration--时长（秒）                                         | 用户切到后台或者杀掉进程时            |
| setting\_page\_click | 设置页点击            | button\_click：1用户反馈，2游戏圈，3微信客服                          | 设置页内点击按钮时（用户反馈，游戏圈、微信客服） |
| self\_button\_click  | <p>大厅内点击<br></p> | button:1-点击购物车，2-点击跳转合成界面，3-点击任务栏，4-点击能量弹窗（各游戏需要自己定义数值） | <p>大厅界面点击按钮时<br></p>     |

#### 5.分享/邀请统计上报

|                     | **事件id**      | **事件定义**                                                 | **data额外携带数据**                  | **建议上报时机**                  |
| ------------------- | ------------- | -------------------------------------------------------- | ------------------------------- | --------------------------- |
| 分享                  | help\_share   | 分享小程序                                                    |                                 | 分享小程序到会话时                   |
| help\_button\_click | 点击求助好友按钮      | bonus\_status:1领取成功，2领取失败 help\_times\_left: 剩余领取次数1,2,3 | 点击求助好友按钮时                       |                             |
| 邀请                  | invite\_click | 点击邀请好友入口                                                 | invite\_number:邀请好友的数量 0,1,2... | 玩家在主页点击邀请好友入口时上报 （首次弹出时上报0） |
| invite\_click\_plus | 点击加号邀请好友      | /                                                        | 玩家在邀请好友界面点击加号时                  |                             |
| bonus\_click        | 点击领取奖励        | bonus\_status:1领取成功，2领取失败                                | 点击领取奖励时                         |                             |

#### 6.设置关卡和活动进度

{% hint style="info" %}
<mark style="color:red;">**`sdk`**</mark><mark style="color:red;">**上报**</mark><mark style="color:red;">**`stay_time`**</mark><mark style="color:red;">**事件时会携带下面设置的数据**</mark>
{% endhint %}

接口说明

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 设置上报level_id参数
/// </summary>
/// <param name="levelId"></param>
public void SetTjLevelId(string levelId) 

/// <summary>
/// 设置上报活动进度参数
/// </summary>
/// <param name="json">活动json格式的字符串</param>
public void SetTjActivityProgress(string json) 
```
{% endcode %}

使用示例

{% code lineNumbers="true" %}
```csharp
Wb.TjManager.Instance.SetTjLevelId("10");

Wb.TjManager.Instance.SetTjActivityProgress("{\"activity_id\":10,\"activity_progress\":15,\"activity_bonus_id\":15,\"begain_time\":100,\"valid_time\":120}");
```
{% endcode %}

#### 7.设置用户标识

{% hint style="info" %}
<mark style="color:red;">**调用下面接口设置后，所有事件上报都会携带用户id**</mark>
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 设置用户id
/// </summary>
/// <param name="userId">用户id</param>
public void SetLoginId(string userId)
```
{% endcode %}

使用示例

{% code lineNumbers="true" %}
```csharp
Wb.CoreManager.Instance.SetLoginId("123321");
```
{% endcode %}

### 广告（必接）

[抖音平台说明](https://developer.open-douyin.com/docs/resource/zh-CN/mini-game/operation1/advertise/norms#\_%E4%B8%80%E5%B9%BF%E5%91%8A%E8%A7%86%E9%A2%91%E6%92%AD%E6%94%BE-icon-%E4%BD%BF%E7%94%A8%E8%A7%84%E8%8C%83)

{% hint style="info" %}
**小游戏中广告按钮和文案在同一水平线时，位置需在文案左侧对齐、要用平台的按钮！！！（一定要按说明的方式处理，否则无法通过审核）**
{% endhint %}

#### 1. 打开广告

{% hint style="info" %}
**打开广告按钮可以加下屏蔽连点的操作，抖小从加载到展示有2\~3s的缓冲时间。可以在用户点击后弹出3\~4s的加载提示蒙版，3\~4s后自动关闭以及收到sdk回调时关闭。**
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 打开广告
/// </summary>
/// <param name="adName">广告位名称</param>
/// <param name="callBackFun">广告关闭回调，状态0成功、1失败、2加载失败</param>
/// <param name="adStartFun">广告播放状态回调，小游戏不要使用</param>
public void OpenAd(string adName, ADCallback callBackFun = null, AdStartCallback adStartFun = null)
```
{% endcode %}

示例

{% code lineNumbers="true" %}
```csharp
Wb.ADManager.Instance.OpenAd("home_mfzs", (ret, info) => {
    if (ret == ADResult.Success)
    {
        Debug.Log("Unity: 广告打开成功 OpenAdTest VideoAd info = " + info);
    }
    else
    {
        Debug.Log("Unity: 广告打开失败 OpenAdTest VideoAd info = " + info);
    }
});
```
{% endcode %}

#### 2. 广告是否缓存好

广告为实时加载方式（不缓存广告），调用接口直接返回`true`

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 广告是否缓存好
/// </summary>
/// <param name="adName">广告位名称</param>
public bool IsAdReady(string adName)
```
{% endcode %}

#### 3. 某关卡是否可以打开该广告

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 某关卡是否可以打开该广告
/// </summary>
/// <param name="adName">广告位名称</param>
/// <param name="level">关卡id</param>
public bool IsAdBeOpenInLevel(string adName, int level)
```
{% endcode %}

#### 4. 关闭广告

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 关闭广告
/// </summary>
/// <param name="adName">广告位名称</param>
public void CloseAd(string adName)
```
{% endcode %}

### 分享

分享策略名称由策划或运营提供。

#### 1. 主动分享

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 小游戏-主动分享接口
/// </summary>
/// <param name="shareName">分享策略名称</param>
/// <param name="userData">自定义数据使用'&'拼接</param>
public void ShareAppMessage(string shareName, string userData = "")

//调用示例
Wb.SocialManager.Instance.ShareAppMessage("分享策略名称", "t1=1&t2=2");
```
{% endcode %}

#### 2. 点击右上角分享

**点击`右上角"..."进行分享`触发**，**前提是要先主动调用设置分享监听`OnShareAppMessage`，触发右上角分享时才会调用下面的设置。**

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 小游戏-右上角分享接口
/// </summary>
/// <param name="shareName">分享策略名称</param>
/// <param name="userData">自定义数据使用'&'拼接</param>
public void OnShareAppMessage(string shareName, string userData = "")

//调用示例
Wb.SocialManager.Instance.OnShareAppMessage("分享策略名称", "t1=1&t2=2");
```
{% endcode %}

### 支付

> 逻辑可参考微信小游戏

#### 1. 支付

> 抖音小游戏没有支付版本限制，`ver`字段可默认使用`Wb.MiniPayVer.Ver1`

{% hint style="info" %}
<mark style="color:red;">**收到支付成功回调游戏发奖后，需主动调用**</mark><mark style="color:red;">**`TjPayOnAccount`**</mark><mark style="color:red;">**充值到账上报和消单。**</mark>
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 小游戏支付
/// </summary>
/// <param name="ver">小游戏支付版本，1:虚拟支付1.0版; 2:虚拟支付2.0版</param>
/// <param name="payParam">支付参数</param>
/// <param name="rate">根据小游戏平台配置支付时设置的支付比例（1：1、1：10、1：100）确认传1、10、100</param>
/// <param name="callFun"></param>
public void MiniPay(MiniPayVer ver, PayParam payParam, OrderPayCallbackHandler callFun = null)
```
{% endcode %}

**PayParam 字段说明**

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">payId 计费点id
price 金额（单位分）
payDesc 商品描述
payCode 支付code，没有填写计费点payId
giftId 礼包id，没有的话写计费点payId
giftType 礼包类型，没有填写空 <a data-footnote-ref href="#user-content-fn-1">非必传</a>
giftNum 礼包数量，一次购买了几份，最小值为1
giftPrice 礼包价值，没有可填写price金额值（单位分）
levelId 有用户等级的传用户等级，没有用户等级的游戏传关卡id
tokenType 代币类型（当礼包类型为代币礼包时，传参。否则传空串）<a data-footnote-ref href="#user-content-fn-2">非必传</a>
custom 用户自定义数据 ,最长128个字符(不会上传给平台) <a data-footnote-ref href="#user-content-fn-3">非必传</a>
userdata 用户自定义数据，由于支付平台的限制，最长16个字符 <a data-footnote-ref href="#user-content-fn-4">非必传</a>
payPush 礼包推送：1主动点击；0被动推送 <a data-footnote-ref href="#user-content-fn-5">非必传</a>
userId 户的账号ID，无账号游戏为空。最长128个字符 非必传
extParams 额外参数，传参后支付事件上报时携带该数据 非必传
</code></pre>

示例

{% code lineNumbers="true" %}
```csharp
// payId 计费点id
// price 金额（单位分）
// payDesc 商品描述
// payCode 支付code，没有填写计费点payId
// giftId 礼包id，没有的话写计费点payId
// giftType 礼包类型
// giftNum 礼包数量，一次购买了几份，最小值为1
// giftPrice 礼包价值，没有可填写price金额值（单位分）
// levelId 有用户等级的传用户等级，没有用户等级的游戏传关卡id
// tokenType 代币类型（当礼包类型为代币礼包时，传参。否则传空串）
// custom 用户自定义数据代替userdata（截取16个字符赋值userdata）,最长128个字符
// payPush 礼包推送：1主动点击；0被动推送
// userId 户的账号ID，无账号游戏为空。最长128个字符

var str = "{\"offer_wave\":100}";
str = str.Substring(1, str.Length - 2);
dic = new Dictionary<string, object> ();
foreach (var t in extArray)
{
    var paramArray = t.Split(':');
    dic.Add(paramArray[0].Substring(1, paramArray[0].Length - 2), paramArray[1]);
}

var payParam = new Wb.PayParam(1, 300, "钻石礼包#https://yyx.vigame.cn/MergeZoo/small.png#等待客服发送充值链接#https://yyx.vigame.cn/MergeZoo/big.png", "1001", "1", "1", 1, 300,"1", "1001", "custom_test", "1", "userid_1001")
{
    extraParam = dic //设置内购统计额外上报参数
};

Debug.Log("PayManager payParam json = " + payParam.ToJson());
var ver = Wb.MiniPayVer.Ver1;
Wb.PayManager.Instance.MiniPay(ver, payParam, (result, payId, userData, orderId) =>
{
    switch (result)
    {
        case Wb.PayStatus.PaySuccess:
            Debug.Log("PayManager 支付成功");
            text.GetComponent<Text>().text = "支付成功";
            Debug.Log("PayManager OrderPay 充值到账上报");
            Wb.TjManager.Instance.TjPayOnAccount(orderId);
            break;
        case Wb.PayStatus.PayFail:
            Debug.Log("PayManager 支付失败");
            text.GetComponent<Text>().text = "支付失败";
            break;
        case Wb.PayStatus.PayCancel:
            Debug.Log("PayManager 支付取消");
            text.GetComponent<Text>().text = "支付取消";
            break;
        case Wb.PayStatus.PayNetError:
            Debug.Log("PayManager 支付完成，遇到网络问题，请重新消耗订单");
            text.GetComponent<Text>().text = "遇到网络问题";
            break;
        default:
            throw new ArgumentOutOfRangeException(nameof(result), result, null);
    }
    Debug.Log("PayManager payId = " + payId);
    Debug.Log("PayManager orderId = " + orderId);
    Debug.Log("PayManager userData = " + userData);
});
```
{% endcode %}

#### 2. 补单

{% hint style="info" %}
**服务器发奖游戏可跳过设置补单回调，要在sdk初始化成功进入游戏后设置。调用该接口会触发`SDK`的补单流程，请根据游戏实际情况确认补单触发的时机。收到回调游戏发奖后，需主动调用`TjPayOnAccount`充值到账上报和消单。**
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 设置补单回调
/// 调用该接口会触发sdk补单流程
/// 根据实际情况确认调用时机
/// </summary>
/// <param name="callFun">支付回调</param>
public void SetInventoryCallBack(OrderPayCallbackHandler callFun)
```
{% endcode %}

示例

{% code lineNumbers="true" %}
```csharp

Wb.PayManager.Instance.SetInventoryCallBack((payStatus, payId, userData, orderId) =>
{
    switch (payStatus)
    {
        case Wb.PayStatus.PaySuccess:
            //支付成功"
            Debug.Log("SetInventoryCallBack DNSDK： PayManager SetInventoryCallBack 充值到账上报");
            Wb.TjManager.Instance.TjPayOnAccount(orderId);
            break;
        case Wb.PayStatus.PayFail:
            //支付失败
            break;
        case Wb.PayStatus.PayCancel:
            //支付取消
            break;
        case Wb.PayStatus.PayNetError:
            //网络错误
            break;
        default:
            //未知错误
    }
});
```
{% endcode %}

#### 3 代币变化上报（选接）

{% hint style="info" %}
**服务器发奖游戏可跳过**

**非服务端发奖游戏建议接入该事件上报，便于充值后奖励到账查询\~**
{% endhint %}

玩家游戏过程中虚拟货币发生变化时进行上报。内购上报统计事件为`pay_token_change`。

示例

{% code lineNumbers="true" %}
```csharp
 //以下参数仅供参考，以实际游戏为准
 Dictionary<string, string> attributes = new Dictionary<string, string>();
 attributes.Add("level_id", "有用户等级传用户等级，没有用户等级传关卡id");
 attributes.Add("token_type", "代币类型(1金币,2钻石,3体力)，没有填空");
 attributes.Add("before_token", "变化前数量");
 attributes.Add("change_token", "变化数量");
 attributes.Add("after_token", "变化后数量");
 attributes.Add("add_reduce", "增减标志(1增加, 2减少)");
 attributes.Add("reason", "变化原因，eg: 1购买道具A、2充值、3奖励");
 attributes.Add("scene", "场景(eg: 1游戏结束时、2游戏过程中、3商城)");
 
 Wb.TjManager.Instance.TJCustomEvent("pay_token_change", attributes);
```
{% endcode %}



[^1]: 

[^2]: 

[^3]: 

[^4]: 

[^5]: 
