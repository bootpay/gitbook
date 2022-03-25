# Unity

구글의 정책변화로 구글 앱스토어에서도 PG결제가 가능하며, Unity로 PG 연동하실 때 이 페이지를 참조하시면 됩니다.

Unity를 통해 배포된 android, ios 환경에서 PG결제가 가능하도록 [bootpay.unitypackage](https://github.com/bootpay/bootpay-unitypackage/raw/main/bootpay.unitypackage)를 제공합니다.

## Bootpay 설치하기

현재 유니티 에셋스토어에는 `bootpay` 로 배포되어있습니다만 관리되지 않으므로,[ ](https://docs.bootpay.co.kr/unity/bootpay.unitypackage)[bootpay.unitypackage](https://github.com/bootpay/bootpay-unitypackage/raw/main/bootpay.unitypackage)를 이곳에서 직접 다운로드 받도록 합니다.

![](<../../.gitbook/assets/스크린샷 2021-12-07 오전 11.26.55 (1).png>)

다운 받은 파일을 프로젝트에 드래그하여 인스톨 하면, 라이브러리와 \*\*샘플(Assets/Scenes/ButtonScript.cs)\*\*가 보입니다. BootpayViewObject (Assets/Plugins/BootpayViewObject.cs)는 수정하실 필요 없으시며, 결제연동은 **샘플 Script**를 참조하시면 되겠습니다.

## 결제창 띄우는 Unity 코드

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine; 
using System; 

public class ButtonScript : MonoBehaviour
{
    public BootpayViewObject bootpayViewObject;
    private bool iWantPay = true; //confirm 단계에서 bootpayViewObject.TransactionConfirm 을 수행해야 최종 결제승인이 됩니다. (재고검사나 내부 로직 수행 후 결제승인)

 

    void Start() 
    { 
        //object init 
        bootpayViewObject =
            (new GameObject("BootpayViewObject")).AddComponent<BootpayViewObject>();
        bootpayViewObject.gameObject.name = "BootpayViewObject";
        bootpayViewObject.name = "BootpayViewObject";

        bootpayViewObject.Init(
            confirm: (data) => {
                Debug.Log(string.Format("CallOnConfirm[{0}]", data));

                if (iWantPay) // 재고가 있거나 결제하기를 원할 경우
                    bootpayViewObject.TransactionConfirm(data); //반드시 수행해야 결제가 최종 승인됨
                else // 재고가 없거나, 결제를 승인하면 안될 경우
                    bootpayViewObject.RemovePaymentWindow();
            },
            done: (data) => {
                Debug.Log(string.Format("CallOnDone[{0}]", data));
            },
            cancel: (data) => {
                Debug.Log(string.Format("CallOnCancel[{0}]", data));       
            },
            ready: (data) => {
                Debug.Log(string.Format("CallOnReady[{0}]", data));
            },
            close: (data) => {
                Debug.Log(string.Format("CallOnClose[{0}]", data));
                bootpayViewObject.Dismiss();
            },
            error: (data) => {
                Debug.Log(string.Format("CallOnError[{0}]", data));
                //Destroy(bootpayViewObject);
            }
        );
        Debug.Log("object start");
    }

    void Update()
    {
        if (Input.GetKey(KeyCode.Escape))
        {
            //Destroy(bootpayViewObject);  //더 이상 결제를 하지 않을때에만 Destory 수행
            return;
        }
    }

    public void goBootpayRequest() {

        BootpayUser user = new BootpayUser(); // 구매자 정보
        user.username = "홍길동";
        user.email = "testUser@email.com";
        user.phone = "01012345678";
        user.area = "서울";

        BootpayExtra extra = new BootpayExtra();
 
         
        List<string> methods = new List<string>();
        methods.Add("easy_card");
        methods.Add("easy_bank");
        methods.Add("card");
        methods.Add("phone");
        methods.Add("bank");
        methods.Add("vbank");


       

        List<BootpayItem> items = new List<BootpayItem>(); //구매하고자 하는 아이템 정보, 배열 형태로 사용
        BootpayItem item1 = new BootpayItem();
        item1.item_name = "미키 마우스";
        item1.qty = 1;
        item1.unique = "ITEM_CODE_MOUSE";
        item1.price = 1000;

        BootpayItem item2 = new BootpayItem();
        item2.item_name = "키보드";
        item2.qty = 1;
        item2.unique = "ITEM_CODE_KEYBOARD";
        item2.price = 1000;
        item2.cat1 = "패션";
        item2.cat2 = "여성상의";
        item2.cat3 = "블라우스";

        items.Add(item1);
        items.Add(item2);


        BootpayPayload payload = new BootpayPayload(); // 결제 정보
        payload.price = 1000;
        payload.name = "블링블링's 마스카라";
        payload.order_id = "1234_1234_1243"; 


#if UNITY_ANDROID
        payload.application_id = "5b8f6a4d396fa665fdc2b5e8"; //이곳에 android_application_id 를 넣어주세요
#elif UNITY_IPHONE
        payload.application_id = "5b8f6a4d396fa665fdc2b5e9"; //이곳에 ios_application_id 를 넣어주세요
#endif

        //model 할당 
        payload.user_info = user;
        payload.extra = extra;
        payload.items = items;
        payload.methods = methods;


        //pay start 
        bootpayViewObject.Request(payload);
    } 
}
```

## 프로젝트 빌드하기

{% tabs %}
{% tab title="Android" %}
1. File > Build Setting > Android 에서 빌드하여 Android Project로 export 합니다.
2. export한 폴더를 Android Studio와 같은 IDE로 Open 합니다
3. [build.gradle](https://github.com/bootpay/unity-android-example/blob/main/unityLibrary/build.gradle) (android.unityLibrary)에 dependencies를 추가합니다.\
   ![](<../../.gitbook/assets/스크린샷 2022-03-02 오전 9.12.53 (3).png>)
4.  unityLibrary 프로젝트의 [AndroidManifest.xml](https://github.com/bootpay/unity-android-example/blob/main/unityLibrary/src/main/AndroidManifest.xml) 파일을 수정합니다.\\

    ```
    //1. useCleartextTraffic 추가 
      <activity android:name="com.unity3d.player.UnityPlayerActivity" android:theme="@style/UnityThemeSelector" android:screenOrientation="fullSensor" android:launchMode="singleTask" android:configChanges="mcc|mnc|locale|touchscreen|keyboard|keyboardHidden|navigation|orientation|screenLayout|uiMode|screenSize|smallestScreenSize|fontScale|layoutDirection|density" android:resizeableActivity="false" android:hardwareAccelerated="false"
            android:usesCleartextTraffic="true">
      
    //2. uses-permission 추가 
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
      
    //3. queries 추가, 내용은 AndroidManifest.xml 상단 링크 참조 
    ```

    \
    ![](<../../.gitbook/assets/스크린샷 2022-03-02 오전 9.14.52 (2) (1).png>)
5. [gradle.properties](https://github.com/bootpay/unity-android-example/blob/main/gradle.properties) (Project Properties) 파일에 AndroidX 지원 설정을 추가합니다.\
   ![](<../../.gitbook/assets/스크린샷 2022-03-02 오전 10.01.43.png>)
6. 필요시 unity 버전과 android gradle plugin 버전 [호환](https://docs.unity3d.com/Manual/android-gradle-overview.html)을 확인하여 [gradle 버전](https://github.com/bootpay/unity-android-example/blob/main/build.gradle)을 수정합니다.\
   ![](<../../.gitbook/assets/스크린샷 2021-12-07 오후 3.42.49.png>)
7. gradle sync 후 빌드 및 실행
{% endtab %}

{% tab title="iOS " %}
1. File > Build Setting > iOS 에서 빌드하여 Xcode Project로 export 합니다.
2. export한 ios폴더의 Unity-iPhone.xcodeproj 파일을 열어 xcode를 실행합니다.
3. Targets > Unity-iPhone > Signing & Capabilities 에서 개발자 프로파일, bundle identifier를 설정합니다\
   \
   ![](<../../.gitbook/assets/스크린샷 2021-12-07 오전 9.13.03 (1).png>)![](<../../.gitbook/assets/스크린샷 2022-02-17 오전 11.36.37.png>)
4. Targets > Unity-iPhone > Build Settings에서 Development Team 설정합니다 (build clean)\
   ![](<../../.gitbook/assets/스크린샷 2021-12-07 오전 9.13.16.png>)
5. Targets > UnityFramework > General > Frameworks and Libraries에 WebKit.framework를 추가합니다.\
   ![](<../../.gitbook/assets/스크린샷 2021-12-07 오후 12.01.01.png>)
6. 빌드 후 실행합니다.
{% endtab %}
{% endtabs %}

## 결제진행 이벤트

{% hint style="info" %}
결제 진행 상태에 따라 LifeCycle 함수가 실행됩니다. 각 함수에 대한 상세 설명은 아래를 참고하세요.
{% endhint %}

{% tabs %}
{% tab title="error 함수" %}
결제 진행 중 오류가 발생된 경우 호출되는 함수입니다. 진행중 에러가 발생되는 경우는 다음과 같습니다.

1. **부트페이 관리자에서 활성화 하지 않은 PG, 결제수단을 사용하고자 할 때**
2. **PG에서 보내온 결제 정보를 부트페이 관리자에 잘못 입력하거나 입력하지 않은 경우**
3. **결제 진행 도중 한도초과, 카드정지, 휴대폰소액결제 막힘, 계좌이체 불가 등의 사유로 결제가 안되는 경우**
4. **PG에서 리턴된 값이 다른 Client에 의해 변조된 경우**

에러가 난 경우 해당 함수를 통해 관련 에러 메세지를 사용자에게 보여줄 수 있습니다.

data 포맷은 아래와 같습니다.

```
{
  action: "BootpayError",
  message: "카드사 거절",
  receipt_id: "5fffab350c20b903e88a2cff"
}
```
{% endtab %}

{% tab title="cancel 함수" %}
결제 진행 중 사용자가 PG 결제창에서 취소 혹은 닫기 버튼을 눌러 나온 경우 입니다. \*\*\*\*

data 포맷은 아래와 같습니다.

```
{
  action: "BootpayCancel",
  message: "사용자가 결제를 취소하였습니다.",
  receipt_id: "5fffab350c20b903e88a2cff"
}
```
{% endtab %}

{% tab title="ready 함수" %}
가상계좌 발급이 완료되면 호출되는 함수입니다. 가상계좌는 다른 결제와 다르게 입금할 계좌 번호 발급 이후 입금 후에 Feedback URL을 통해 통지가 됩니다. 발급된 가상계좌 정보를 ready 함수를 통해 확인하실 수 있습니다.

data 포맷은 아래와 같습니다.

```
{
  account: "T0309260001169"
  accounthodler: "한국사이버결제"
  action: "BootpayBankReady"
  bankcode: "BK03"
  bankname: "기업은행"
  expiredate: "2021-01-17 00:00:00"
  item_name: "테스트 아이템"
  method: "vbank"
  method_name: "가상계좌"
  order_id: "1610591554856"
  params: null
  payment_group: "vbank"
  payment_group_name: "가상계좌"
  payment_name: "가상계좌"
  pg: "kcp"
  pg_name: "KCP"
  price: 3000
  purchased_at: null
  ready_url: "https://dev-app.bootpay.co.kr/bank/7o044QyX7p"
  receipt_id: "5fffad430c20b903e88a2d17"
  requested_at: "2021-01-14 11:32:35"
  status: 2
  tax_free: 0
  url: "https://d-cdn.bootapi.com"
  username: "홍길동"
}
```
{% endtab %}

{% tab title="confirm 함수" %}
결제 승인이 되기 전 호출되는 함수입니다. 승인 이전 관련 로직을 서버 혹은 클라이언트에서 수행 후 결제를 승인해도 될 경우`BootPay.transactionConfirm(data); 또는 return true;`

코드를 실행해주시면 PG에서 결제 승인이 진행이 됩니다.

**\* 페이앱, 페이레터 PG는 이 함수가 실행되지 않고 바로 결제가 승인되는 PG 입니다. 참고해주시기 바랍니다.**

data 포맷은 아래와 같습니다.

```
{
  receipt_id: "5fffc0460c20b903e88a2d2c",
  action: "BootpayConfirm"
}
```
{% endtab %}

{% tab title="done 함수" %}
PG에서 거래 승인 이후에 호출 되는 함수입니다. 결제 완료 후 다음 결제 결과를 호출 할 수 있는 함수 입니다.

이 함수가 호출 된 후 반드시 REST API를 통해 [결제검증](https://docs.bootpay.co.kr/rest/verify)을 수행해야합니다. data 포맷은 아래와 같습니다.

{% hint style="warning" %}
결제가 완료되더라도 클라이언트 상태에 따라서 브라우저가 종료되거나 reload 되는 등의 이슈가 있어서 done 이벤트를 못받을 수도 있습니다. 가장 확실한 처리 방법은 [webhook 통지시](../../webhook/server.md)에도 [결제검증](../../server/verify.md)을 하시어 아이템 지급이나 상품 발송 등의 비즈니스 로직을 수행하시면 더욱 완성도 있는 서비스 개발이 되시겠습니다.
{% endhint %}

```
{
  action: "BootpayDone"
  card_code: "CCKM",
  card_name: "KB국민카드",
  card_no: "0000120000000014",
  card_quota: "00",
  item_name: "테스트 아이템",
  method: "card",
  method_name: "카드결제",
  order_id: "1610596422328",
  payment_group: "card",
  payment_group_name: "신용카드",
  payment_name: "카드결제",
  pg: "kcp",
  pg_name: "KCP",
  price: 100,
  purchased_at: "2021-01-14 12:54:53",
  receipt_id: "5fffc0460c20b903e88a2d2c",
  receipt_url: "https://app.bootpay.co.kr/bill/UFMvZzJqSWNDNU9ERWh1YmUycU9hdnBkV29DVlJqdzUxRzZyNXRXbkNVZW81%0AQT09LS1XYlNJN1VoMDI4Q1hRdDh1LS10MEtZVmE4c1dyWHNHTXpZTVVLUk1R%0APT0%3D%0A",
  requested_at: "2021-01-14 12:53:42",
  status: 1,
  tax_free: 0,
  url: "https://d-cdn.bootapi.com"
}
```
{% endtab %}
{% endtabs %}

##

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
