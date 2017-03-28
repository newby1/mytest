# 游戏sdk接入文档

------
<table>
    <tr>
        <th>版号</th>
        <th>修改时间</th>
        <th>内容</th>
    </tr>
    <tr>
        <td rowspan="4">1.0</td>
        <td rowspan="4">2017-03-22</td>
        <td>
        1.支持用户登录 
        </td>
    </tr>
    <tr>
        <td>
        2.支持用户信息获取 
        </td>
    </tr>
    <tr>
        <td>
        3.支持用户退出 
        </td>
    </tr>
    <tr>
        <td>
        4.支持用户支付（支付宝，微信） 
        </td>
    </tr>
</table>

-----
[TOC]
## 1、接入流程
### 1.1 申请APPID APPKEY APPSECRET
联系相关运营人员获得APPID APPKEY APPSECRET
### 1.2 导入资源包

1.在游戏应用的build.gradle里面加入
```
repositories {
    flatDir {
        dirs 'libs' //this way we can find the .aar file in libs folder
    }
}

dependencies {
    ...
    compile(name: 'game360-uc', ext: 'aar')
    compile(name: 'game360-libs', ext: 'aar')
    compile 'com.android.support:appcompat-v7:22.2.1'
    compile 'com.android.support:support-v4:22.2.1'
}
```
2.将"game360-uc.aar"和"game360-libs.aar"两个aar文件复制到游戏应用的libs下

### 1.3配置应用工程的AndroidManifest.xml
```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.SEND_SMS" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
<uses-permission android:name="android.permission.READ_LOGS" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.NFC" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
<uses-permission android:name="android.permission.CHANGE_CONFIGURATION" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
<uses-permission android:name="android.permission.READ_SETTINGS" />
<uses-permission android:name="uac.permission.READ_WRITE_RTKT" />
<uses-permission android:name="uac.permission.READ_WRITE_USERINFO" />
<uses-feature android:name="android.hardware.camera.autofocus" />
<uses-permission android:name="com.android.launcher.permission.READ_SETTINGS" />
```
----
## 2、接口说明
### 2.1 初始化sdk
#### 2.1.1 代码示例
```
public class App extends Application{
    @Override
    public void onCreate(){
        super.onCreate();
        //需要添加这段代码
        com.jiagu.sdk.QucSdk.install(this, this);
    }
}
```
这段代码`com.jiagu.sdk.QucSdk.install(this, this);`需要在主程序中添加。

如果之前没有App这个类，手动创建后在AndroidManifest.xml里面Application里面需要申明下
```
<application
    ...
    ...
    android:name=".App" 
    ...>
</application> 
```
在需要调用的地方初始化sdk
```
import com.qihoo360.game360.SeiYaSdk;

public class MainActivity extends Activity{
    @Override
    protected void onCreate(Bundle saveInstanceState){
        super.onCreate(saveInstanceState); 
        ....
        
        //示例代码 
        //appid,appsecret,channelid,pkey,gkey都先联系运营获得
        Bundle bundle = new Bundle();
        bundle.putString("appid", "xxxxxx");
        bundle.putString("appsecret", "xxxxxx");
        bundle.putString("channelid", "360pay");
        final SeiYaSdk sdk =  SeiYaSdk.getInstance(bundle);
    }
}
```
#### 2.1.2 功能描述
整个sdk是个单例，通过`getInstance()`来获取实例，首次调用必须把app相关信息传递过来。
#### 2.1.3 参数、回调说明
bundle参数说明

|参数|类型|必须|参数说明|
|---|---|---|---|
|appid|String|Y|平台申请得到|
|appsecret|String|Y|平台申请得到|
|channelid|String|Y|渠道id，平台申请得到|

### 2.2 登录接口

#### 2.2.1 代码示例
```
sdk.login(MainActivity.this, new SeiYaSdk.Callback() {
    @Override
    public void onSuccess(Bundle bundle) {
        //成功回调
    }
    @Override
    public void onFail(Bundle bundle) {
        //失败回调
    }
});
```
#### 2.2.2 功能说明
sdk实例化后，可以按需调`login`方法。
#### 2.2.3 参数、回调说明 <span id="callback"></span>
调用方法传参说明

|参数|类型|必须 |参数说明| 
|---|---|---|---|
|activity|Activity|Y|当前activity对象|
|callback|SeiYaSdk.Callback|Y|SeiYaSdk.Callback为接口,方法必须实现。登录成功或者失败的回调|

callback onSuccess 回调参数说明。

|参数|类型|参数说明| 
|---|---|---|
|uid|String|用户id|
|username|String|用户名称|
|avatar|String|用户头像|
|biz_token|String|业务token，用户业务核验，会失效|

callback onFail 回调参数说明

|参数 |类型|参数说明| 
|---|---|---|
|errno|int|错误码。负值为网络错误；600以下一般为http状态返回|
|errmsg|String|错误信息描述|
|data|Object|相关数据|
#### 2.2.4 常见错误信息
|errno|errmsg|data|
|--|--|--|
|408|connect timeout||
|600|network error||
|1000|need login||
|1007|biz_token is invalid||

### 2.3 获取用户信息
#### 2.3.1 代码示例
```
sdk.getUserInfo(MainActivity.this, new SeiYaSdk.Callback() {
    @Override
    public void onSuccess(Bundle bundle) {
        //成功回调
    }
    @Override
    public void onFail(Bundle bundle) {
        //失败回调
    }
});
```
#### 2.3.2 功能说明
sdk实例化后，可以按需调`getUserInfo`方法。
#### 2.3.3 参数、回调说明
同[2.2.3](#callback)

### 2.4 退出
#### 2.4.1 代码示例
```
sdk.logout(MainActivity.this, new SeiYaSdk.Callback() {
    @Override
    public void onSuccess(Bundle bundle) {
        //成功回调
    }
    @Override
    public void onFail(Bundle bundle) {
        //失败回调
    }
});
```
#### 2.4.2 功能说明
sdk实例化后，可以按需调`logout`方法。
#### 2.4.3 参数、回调说明
传参说明同[2.2.3](#callback).
回调参数为空`Bundle`;

### 2.5 支付
#### 2.5.1 代码示例
```
//支付
Bundle bundle = new Bundle();
bundle.putString("cost", "6");
bundle.putString("goods_id", "10001");
bundle.putString("goods_name", "6个元宝");
bundle.putString("goods_count", "1");
bundle.putString("role_id", "10000");
bundle.putString("role_name", "星矢");
sdk.pay(MainActivity.this, new SeiYaSdk.Callback(){
    @Override
    public void onSuccess(Bundle bundle){
        //成功回调
    }
    @Override
    public void onFail(Bundle bundle){
        //失败回调
    }
}, bundle);
```
#### 2.5.2 功能说明
sdk实例化后，可以按需调`pay`和`payResult`方法。
#### 2.5.3 参数、回调说明
调用方法传参说明
|参数|类型|必须 |参数说明| 
|---|---|---|---|
|activity|Activity|Y|当前activity对象|
|callback|SeiYaSdk.Callback|Y|SeiYaSdk.Callback为接口,方法必须实现。登录成功或者失败的回调|

bundle里需要的信息
|参数|类型|必须 |参数说明| 
|---|---|---|---|
|cost|String|Y|支付的金额，单位元|
|goods_id|String|Y|商品id|
|goods_name|String|Y|商品显示名称|
|goods_count|String|Y|商品数量|
|role_id|String|Y|游戏中角色id|
|role_name|String|Y|游戏中角色名称|

callback onSuccess 回调参数说明。
|参数 |类型|参数说明| 
|---|---|---|
|pay_state|int|状态码|
|result_msg|String|状态信息|
|order_id|String|订单号|
callback onFail 回调参数说明
|参数 |类型|参数说明| 
|---|---|---|
|pay_state|int|状态码|
|result_msg|String|状态信息|
|order_id|String|订单号|
#### 2.5.4 状态码信息
onSuccess状态码
|pay_state|result_msg|
|--|--|
|100|支付成功|
onFail 状态码
|pay_state|result_msg|
|--|--|
|200|支付失败|
|300|用户取消|
|400|直接退出|
|500|处理中|
|600|状态位置|

#### 2.5.5 发货
参考服务端文档

****************
## 3、其他

