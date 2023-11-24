---
description: 适于使用unity引擎开发的小游戏参考
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

# 接入文档-微信小游戏

### 对接流程

{% hint style="danger" %}
SDK接入完毕后请发送二维码到对接群，测试验收通过后再提审发布！！！
{% endhint %}

### 接入准备

#### 通用域名加白

接入前请检查下述域名是否已加进微信域名配置白名单，配置项有request合法域名、socket合法域名、uploadFile合法域名、downloadFile合法域名等（根据具体使用到那些域名项进行配置，也可以通配）。

{% code lineNumbers="true" %}
```C#
https://yyx.vigame.cn
https://c.vigame.cn
https://p.vigame.cn 
https://u.vigame.cn 
https://x.vigame.cn
```
{% endcode %}

### 初始化（必接）

将下载的`dnsdk.js`拷贝到导出小游戏工程`game.js`同级目录下，`game.js`文件中添加如下代码

<pre class="language-javascript" data-line-numbers><code class="lang-javascript">import './weapp-adapter';
import unityNamespace from './unity-namespace';
import './webgl.wasm.framework.unityweb';
import './unity-sdk/index.js';
import checkVersion, { canUseCoverview } from './check-version';
import 'texture-config.js';
import { launchEventType, scaleMode } from './plugin-config';
<strong>import './dnsdk.js'; // 添加动能小游戏sdk
</strong></code></pre>

**初始化示例**

{% code lineNumbers="true" fullWidth="false" %}
```csharp
/// <summary>
/// 微信小游戏sdk初始化
/// </summary>
/// <param name="appid"></param>
/// <param name="appKey"></param>
/// <param name="offerId">米大师侧申请的应用ID</param>
/// <param name="cloudEnv">云函数的环境ID</param>
/// <param name="payRate">根据小游戏平台配置支付时设置的支付比例（1：1、1：10、1：100）确认传1、10、100</param>
/// <param name="callBack">sdk初始化回调bool：是否初始化成功；string：openId；string：userCode</param>
/// <param name="gravityEngineToken">引力引擎token</param>
public void MiniGameInit(string appid, string appKey, string offerId, string cloudEnv, Wb.MiniPayRate payRate = Wb.MiniPayRate.Shi, Action<bool, string, string> callBack = null, string gravityEngineToken = null)
```
{% endcode %}

示例

{% code lineNumbers="true" %}
```csharp
Wb.CoreManager.Instance.MiniGameInit("H5动能内部appid", "H5动能内部appKey", "米大师侧申请的应用ID", "云环境ID", Wb.MiniPayRate.Shi,
    (ret, openId, userCode) =>
    {
        if (ret)
        {
            Debug.Log("DNSDK:  MiniGameInit 初始化成功 openId：" + openId + ", userCode：" + userCode);
        }
        else
        {
            Debug.Log("DNSDK:  MiniGameInit SDK初始化失败");
            WX.ShowModal(new ShowModalOption()
            {
                title = "提示",
                content = "SDK初始化失败，请重启！",
                success = (res) =>
                {
                    WX.RestartMiniProgram();
                }
            });
        }
    });
```
{% endcode %}

### 统计（必接）

根据统计埋点文件使用合适接口上报游戏中产生的事件。

{% hint style="danger" %}
**转化行为`active`和`active_register`事件必接！！！**
{% endhint %}

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

<table data-header-hidden data-full-width="false"><thead><tr><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><td><strong>事件名称</strong></td><td><strong>事件ID</strong></td><td><strong>触发时机</strong></td><td><strong>附加参数</strong></td></tr><tr><td>游戏loading页面</td><td>app_loading_show<br></td><td>游戏引擎初始化后就触发上报</td><td>无</td></tr><tr><td>游戏内首页</td><td>app_home_show</td><td>主页展示时</td><td>无</td></tr></tbody></table>

使用示例

{% code lineNumbers="true" %}
```csharp
//示例
Wb.TjManager.Instance. TJCustomEvent("app_loading_show")；
Wb.TjManager.Instance. TJCustomEvent("app_home_show")；
```
{% endcode %}

#### 2. 转化行为上报（必接）

{% hint style="warning" %}
**注意：转化行为上报使用`TjSendTrackingEvent`接口！！！**\
**SDK会本地缓存埋点记录，再上报会进行拦截**
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

| **事件id**             | **事件定义** | **data额外携带数据**                                          | **建议上报时机**               |
| -------------------- | -------- | ------------------------------------------------------- | ------------------------ |
| online               | 在线时长     | duration--时长（秒）                                         | 用户切到后台或者杀掉进程时            |
| setting\_page\_click | 设置页点击    | button\_click：1用户反馈，2游戏圈，3微信客服                          | 设置页内点击按钮时（用户反馈，游戏圈、微信客服） |
| self\_button\_click  | 大厅内点击    | button:1-点击购物车，2-点击跳转合成界面，3-点击任务栏，4-点击能量弹窗（各游戏需要自己定义数值） | <p>大厅界面点击按钮时<br></p>     |

#### 5.分享/邀请统计上报

|                     | **事件id**      | **事件定义**                                                 | **data额外携带数据**                  | **建议上报时机**                  |
| ------------------- | ------------- | -------------------------------------------------------- | ------------------------------- | --------------------------- |
| 分享                  | help\_share   | 分享小程序                                                    |                                 | 分享小程序到会话时                   |
| help\_button\_click | 点击求助好友按钮      | bonus\_status:1领取成功，2领取失败 help\_times\_left: 剩余领取次数1,2,3 | 点击求助好友按钮时                       |                             |
| 邀请                  | invite\_click | 点击邀请好友入口                                                 | invite\_number:邀请好友的数量 0,1,2... | 玩家在主页点击邀请好友入口时上报 （首次弹出时上报0） |
| invite\_click\_plus | 点击加号邀请好友      | /                                                        | 玩家在邀请好友界面点击加号时                  |                             |
| bonus\_click        | 点击领取奖励        | bonus\_status:1领取成功，2领取失败                                | 点击领取奖励时                         |                             |

#### 6.设置关卡和活动进度

{% hint style="warning" %}
**`sdk`上报`stay_time`事件时会携带下面设置的数据**
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

{% hint style="warning" %}
**调用下面接口设置后，所有事件上报都会携带用户id**
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

#### 1. 打开广告

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

### 支付

#### 1.支付接口

{% hint style="info" %}
**注：**\
**1： 虚拟支付 1.0版本**\
**2： 虚拟支付 2.0版本**\
**3: SDK默认为1：10旧接口升级后参数要赋值给新接口`PayParam`对应字段！！！**\
**`custom`字段为透传自定义数据字段，该字段会包含在订单信息中，有透传需求请使用该字段！！！**
{% endhint %}

支付接口

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 小游戏支付
/// </summary>
/// <param name="ver">小游戏支付版本，1:虚拟支付1.0版本; 2:虚拟支付2.0版本</param>
/// <param name="payParam">付参数</param>
/// <param name="callFun"></param>
public void MiniPay(int ver, PayParam payParam, OrderPayCallbackHandler callFun = null)
```
{% endcode %}

调用示例

<pre class="language-csharp" data-line-numbers><code class="lang-csharp">// payId 计费点id
// price 金额（单位分）
// payDesc 商品描述，使用#拼接
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

<strong>public void OrderPay()
</strong>{
    Debug.Log("OrderPay 被点击");
    ShowPreInputDialog("购买礼包", "钻石礼包", "2",(inputValue, inputValue2)=>
    {
        var str = "{\"offer_wave\":100}";
        str = str.Substring(1, str.Length - 2);
        Debug.Log("OrderPay version = " + inputValue2);
        var extArray = str.Split(",");
        var dic = new Dictionary&#x3C;string, object> ();
        foreach (var t in extArray)
        {
            var paramArray = t.Split(":");
            dic.Add(paramArray[0].Substring(1, paramArray[0].Length - 2), paramArray[1]);
        }

        var payParam = new Wb.PayParam(1, 100,
            inputValue +
            "#https://yyx.vigame.cn/MergeZoo/small.png#客服发送#https://yyx.vigame.cn/MergeZoo/big.png", "1001",
            "1", "1", 1, 1,"1", "1001", "custom_test", "1", "userid_1001")
        {
            extraParam = dic
        };

        Debug.Log("PayManager payParam json = " + payParam.ToJson());
<strong>        var ver = inputValue2 == "1" ? Wb.MiniPayVer.Ver1 : Wb.MiniPayVer.Ver2;
</strong>        Wb.PayManager.Instance.MiniPay(ver, payParam, (result, payId, userData, orderId) =>
        {
            switch (result)
            {
<strong>                case Wb.PayStatus.PaySuccess:
</strong>                    Debug.Log("PayManager 支付成功");
                    text.GetComponent&#x3C;Text>().text = "支付成功";
                    Debug.Log("PayManager OrderPay 充值到账上报");
                    Wb.TjManager.Instance.TjPayOnAccount(orderId);
                    break;
<strong>                case Wb.PayStatus.PayFail:
</strong>                    Debug.Log("PayManager 支付失败");
                    text.GetComponent&#x3C;Text>().text = "支付失败";
                    break;
<strong>                case Wb.PayStatus.PayCancel:
</strong>                    Debug.Log("PayManager 支付取消");
                    text.GetComponent&#x3C;Text>().text = "支付取消";
                    break;
<strong>                case Wb.PayStatus.PayNetError:
</strong>                    Debug.Log("PayManager 支付完成，遇到网络问题，请重新消耗订单");
                    text.GetComponent&#x3C;Text>().text = "遇到网络问题";
                    break;
                default:
                    throw new ArgumentOutOfRangeException(nameof(result), result, null);
            }
            Debug.Log("PayManager payId = " + payId);
            Debug.Log("PayManager orderId = " + orderId);
            Debug.Log("PayManager userData = " + userData);
        });
    });
}
</code></pre>

**自测示例**

虚拟支付2.0会进行验签请求，可通过日志查看

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

虚拟支付1.0不会进行验签请求，会直接拉起支付

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

#### 2.上报到账事件和消单

{% hint style="info" %}
**游戏发奖的同时调用该接口上报充值到账事件，同时sdk会进行消单处理，整个支付流程结束。**
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 充值到账后上报
/// </summary>
/// <param name="tradeId">订单id</param>
public void TjPayOnAccount(string tradeId) 
```
{% endcode %}

#### 3. 补单

> 适用场景：使用sdk支付

{% hint style="info" %}
**游戏在合适时机调用该接口设置补单回调，sdk会在游戏首次调用和onShow时，触发补单逻辑进行补单**
{% endhint %}

设置补单回调，触发sdk中的补单逻辑。

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 支付回调
/// </summary>
/// <param name="payStatus">支付状态</param>
/// <param name="payId">计费点id</param>
/// <param name="userData">自定义参数</param>
/// <param name="orderId">订单id</param>
public delegate void OrderPayCallbackHandler(PayStatus payStatus, int payId, string userData, string orderId);

/// <summary>
/// 设置补单回调
/// 调用该接口会触发sdk补单流程
/// 根据实际情况确认调用时机
/// </summary>
/// <param name="callFun">支付回调</param>
public void SetInventoryCallBack(OrderPayCallbackHandler callFun)
```
{% endcode %}

### 分享

分享策略名称由策划或运营提供。

#### 1.主动分享

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

### 游戏圈

[微信文档](https://developers.weixin.qq.com/minigame/dev/api/open-api/game-club/wx.createGameClubButton.html)

#### 1. 创建

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 创建游戏圈按钮
/// <param name="rect">按钮显示位置</param>
/// <param name="param">文本GameClubText、图片GameClubImage</param>
/// <param name="func">点击回调</param>
/// </summary>
public void GameClubButtonCreate(RectTransform trans, GameClubBase param, Action<string> func) 

//文本按钮示例
Wb.GameClubBase config = new Wb.GameClubText
{
    text = "游戏圈",
    style = new Wb.GameClubStyle
    {
        color = "#ffffff",
        fontSize = 16,
        backgroundColor = "#ff0000",
        lineHeight = 40
    }
};
if (text.TryGetComponent(out RectTransform rect))
{
    Wb.CoreManager.Instance.GameClubButtonCreate(rect, config, (res) =>
    {
        Debug.Log("GameClub res = " + res);
    });
    Wb.CoreManager.Instance.GameClubButtonShow();
}
else
{
    Debug.Log("GameClub RectTransform 获取失败");
}

//图片按钮示例
Wb.GameClubBase config = new Wb.GameClubImage
{
    image = "images/green.png",
    icon = "green"
};
if (image.TryGetComponent(out RectTransform rect))
{
    Wb.CoreManager.Instance.GameClubButtonCreate(rect, config, (res) =>
    {
        Debug.Log("GameClub res = " + res);
    });
    Wb.CoreManager.Instance.GameClubButtonShow();
}
else
{
    Debug.Log("GameClub RectTransform 获取失败");
}
```
{% endcode %}

#### 2. 销毁

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 销毁游戏圈按钮
 /// </summary>
public void GameClubButtonDestroy() 
```
{% endcode %}

#### 3. 隐藏

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 隐藏游戏圈按钮
/// </summary>
public void GameClubButtonHide() 
```
{% endcode %}

#### 4. 显示

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 显示游戏圈按钮
/// </summary>
public void GameClubButtonShow()
```
{% endcode %}

#### 5. 移除按钮的点击事件的监听函数

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 移除游戏圈按钮的点击事件的监听函数
/// </summary>
public void GameClubButtonOffTap() 
```
{% endcode %}

### 意见反馈

[微信文档](https://developers.weixin.qq.com/minigame/dev/api/open-api/feedback/wx.createFeedbackButton.html)

#### 1. 创建

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 创建意见反馈按钮
/// <param name="rect">按钮显示位置</param>
/// <param name="param">文本GameClubText、图片GameClubImage</param>
/// <param name="func">点击回调</param>
/// </summary>
public void FeedbackButtonCreate(RectTransform trans, GameClubBase param, Action<string> func) 

//文本示例
Wb.GameClubBase config = new Wb.GameClubText
{
    text = "问题反馈",
    style = new Wb.GameClubStyle
    {
        color = "#ffffff",
        fontSize = 16,
        backgroundColor = "#ff0000",
        lineHeight = 40
    }
};
if (text.TryGetComponent(out RectTransform rect))
{
    Wb.CoreManager.Instance.FeedbackButtonCreate(rect, config, (res) =>
    {
        Debug.Log("Feedback res = " + res);
    });
    Wb.CoreManager.Instance.FeedbackButtonShow();
}
else
{
    Debug.Log("Feedback RectTransform 获取失败");
}

//图片示例
Wb.GameClubBase config = new Wb.GameClubImage
{
    image = "images/dark.png",
};
if (image.TryGetComponent(out RectTransform rect))
{
    Wb.CoreManager.Instance.FeedbackButtonCreate(rect, config, (res) =>
    {
        Debug.Log("Feedback res = " + res);
    });
    Wb.CoreManager.Instance.FeedbackButtonShow();
}
else
{
    Debug.Log("Feedback RectTransform 获取失败");
}
```
{% endcode %}

#### 2. 销毁

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 销毁意见反馈按钮
/// </summary>
public void FeedbackButtonDestroy() 
```
{% endcode %}

#### 3. 隐藏

```C#
/// <summary>
/// 隐藏意见反馈按钮
/// </summary>
public void FeedbackButtonHide() 
```

#### 4. 显示

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 显示意见反馈按钮
/// </summary>
public void FeedbackButtonShow() 
```
{% endcode %}

#### 5. 移除按钮的点击事件的监听函数

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 移除意见反馈按钮的点击事件的监听函数
/// </summary>
public void FeedbackButtonOffTap() 
```
{% endcode %}

### 其他功能

#### 1.一次性订阅消息通知

需要自己接入微信的订阅功能，接入完毕后。可以使用下面的接口将通知时间（**服务器时间单位毫秒，通知时间 = 服务器时间+间隔多长时间收到通知**）告诉服务器。服务器根据设置的时间发送消息给玩家。

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 一次性订阅消息通知
/// </summary>
/// <param name="templateId">游戏后台配置的模板id</param>
/// <param name="noticeTime">发送通知的时间戳单位毫秒，为服务器时间</param>
public void SetSubscribe (string templateId, string noticeTime, ReportUserDataCallback callFun) 
```
{% endcode %}

示例：订阅1小时后收到微信通知

{% code lineNumbers="true" %}
```csharp
string timeStamp = "请求的服务器时间";
//订阅1小时后收到微信通知
long endTime = long.Parse(timeStamp) + 60 * 60 * 1000;
Wb.CoreManager.Instance.SetSubscribe("模板id", endTime.ToString(), (ret, info) => {
    if (ret)
    {
        // Debug.Log("Subscribe 订阅成功 info = " + info);
    }
    else
    {
        // Debug.Log("Subscribe 订阅失败 info = " + info);
    }
});
```
{% endcode %}

#### 2.获取黑名单

判断是否加入黑名单，根据请求返回的数据判断。`code`为200，返回的`info`中包含`forbidden`时即为加入了黑名单。

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 获取黑名单数据
/// </summary>
/// <param name="userId">用户唯一标识</param>
/// <param name="callFun">回调ret：true时需根据返回的info中是否有forbidden来判断是否被禁, info返回信息</param>
public void GetBlackConfig(string userId, ReportUserDataCallback callFun = null) 
```
{% endcode %}

返回值

{% code lineNumbers="true" %}
```json
{
  "code":200,              --返回结果码 200 请求成功 500服务器错误   
  "data":{
      "forbidden":{            --判断是否有值
          "leftTime":"-1",       --解禁剩余时间 秒 -1 永久封禁  其他为具体解封秒数
          "reason":"封禁",         --封禁原因
          "forbiddenTime":"-1",  --封禁截止时间  -1 永久封禁 其他 具体解封时间 yyyy-MM-dd HH:mm:ss
      }
  },
  "retMsg":"", --错误提示信息  
}
```
{% endcode %}

结果码

| 结果码 | 说明       |
| --- | -------- |
| 200 | 成功       |
| 401 | 签名不通过    |
| 403 | 必要请求参数为空 |
| 500 | 服务器出错    |

#### 3.客服中心

{% hint style="info" %}
**使用客服中心，需在后台进行配置**
{% endhint %}

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 打开客服中心
/// </summary>
/// <param name="title">标题</param>
/// <param name="loginId">用户id</param>
public void OpenClientCenter(string title, string loginId) 
```
{% endcode %}

#### 4. 获取用户信息

[微信公众平台用户信息相关接口调整公告](https://developers.weixin.qq.com/community/develop/doc/00028edbe3c58081e7cc834705b801)

{% code lineNumbers="true" %}
```csharp
/// <summary>
/// 获取用户微信信息
/// </summary>
/// <param name="type">登录类型1，默认微信</param>
/// <param name="callFun">通知回调</param>
public void GetUserInfo(Wb.LoginType type = Wb.LoginType.TYPE_WX, UserInfoCallback callFun = null)
```
{% endcode %}
