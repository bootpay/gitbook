# Android

Native 방식으로 안드로이드 앱을 만들때 이 페이지를 참조하시면 됩니다.

금융감독원에서는 온라인 신용카드 결제시 안심클릭 또는 ISP 안전결제를 권고하며, 이에 따라 카드사의 앱카드 앱이 [Intent Call 방식](https://developer.android.com/training/basics/intents?hl=ko)으로 호출됩니다. 그러므로 Android에서 외부앱 호출 및 통신을 위한 처리를 해주셔야 하는데, 이 라이브러를 사용하시면 쉽게 해결됩니다. ​

PG 결제창은 기본적으로 Javascript로 연동됩니다. 부트페이 Android SDK는 내부적으로 Webview 방식으로 구현하였으며, 사용방법은 아래와 같습니다.

## Bootpay 설치하기

{% tabs %}
{% tab title="Android" %}
bootpay 모듈은 [jipack](https://jitpack.io)을 통해 배포되었습니다. [bootpay github](https://github.com/bootpay/android)은 [이곳](https://github.com/bootpay/android)을 참조하세요.

#### Gradle을 통한 설치

```groovy
//build.gradle (project)
buildscript {
    repositories {
        ...
        jcenter()
    }
    dependencies {
        ...
        classpath 'com.github.dcendents:android-maven-gradle-plugin:2.1' // 비공식 해결 방법, gradle build error 가 발생시에만 추가
 
    }
}

allprojects {
    repositories {
        ...
        jcenter()
        maven {
            url "https://jitpack.io"
        }
    }
}
```

```groovy
//build.gradle (module)

android {
    compileSdkVersion 30 //Android 11 지원을 위한 30 이상 버전을 추천 
    buildToolsVersion "30.0.3"

    defaultConfig {
        ...
        minSdkVersion 16
        targetSdkVersion 30 //Android 11 지원을 위한 30 이상 버전을 추천 
    }
    }

dependencies {
    ...
    implementation 'com.github.bootpay:android:+' //최신 버전 추천
}
```

#### AndroidManifest.xml 수정

```markup
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```

{% hint style="info" %}
실행시 Cleartext HTTP traffic to Your.Domain not permitted 와 같은 에러가 나온 경우

Android [네트워크 보안 구성](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted)에 따르면 안드로이드 9(API28)부터 cleartext traffic을 기본적으로를 비활성화한다고 합니다. 따라서 API 28 이후에서 Http에 접근하려면 cleartext traffic을 활성화 시켜야 합니다.

```markup
<application
        android:label="@string/app_name"
        ...
        android:usesCleartextTraffic="true">
```
{% endhint %}
{% endtab %}
{% endtabs %}

## 결제창 띄우는 예제 코드

```java
////https://github.com/bootpay/android/blob/main/app/src/main/java/kr/co/bootpay/android/WebAppActivity.java

package kr.co.bootpay.android;

import android.content.Context;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.EditText;
import android.widget.Spinner;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import kr.co.bootpay.core.Bootpay;
import kr.co.bootpay.core.BootpayAnalytics;
import kr.co.bootpay.core.events.BootpayEventListener;
import kr.co.bootpay.core.models.BootExtra;
import kr.co.bootpay.core.models.BootItem;
import kr.co.bootpay.core.models.BootUser;
import kr.co.bootpay.core.models.Payload;

public class NativeActivity extends AppCompatActivity {
    private String application_id = "5b8f6a4d396fa665fdc2b5e8";
    Context context;

    Spinner spinner_pg;
    Spinner spinner_method;
    EditText edit_price;
    EditText edit_non_tax;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_native);

        this.context = this;
        spinner_pg = findViewById(R.id.spinner_pg);
        spinner_method = findViewById(R.id.spinner_method);
        edit_price = findViewById(R.id.edit_price);
        edit_non_tax = findViewById(R.id.edit_non_tax);

        bootpayInit();
    }

    void bootpayInit() {
        BootpayAnalytics.init(this, application_id);
    }

    public void goRequest(View v) {

        BootUser user = new BootUser().setPhone("010-1234-5678"); // 구매자 정보
        BootExtra extra = new BootExtra().setQuotas(new int[] {0,2,3}).setPopup(1).setQuickPopup(1);  // 일시불, 2개월, 3개월 할부 허용, 할부는 최대 12개월까지 사용됨 (5만원 이상 구매시 할부허용 범위)
        Double price = 1000d;
        try {
            price = Double.parseDouble(edit_price.getText().toString());
        } catch (Exception e){}


        String pg = BootpayValueHelper.pgToString(spinner_pg.getSelectedItem().toString());
        String method = BootpayValueHelper.methodToString(spinner_method.getSelectedItem().toString());


        List<BootItem> items = new ArrayList<>();
        BootItem item1 = new BootItem().setItemName("마우's 스").setUnique("ITEM_CODE_MOUSE").setQty(1).setPrice(500d);
        BootItem item2 = new BootItem().setItemName("키보드").setUnique("ITEM_KEYBOARD_MOUSE").setQty(1).setPrice(500d);
        items.add(item1);
        items.add(item2);

        Payload payload = new Payload();
        payload.setApplicationId(application_id)
                .setName("맥\"북프로's 임다")
                .setPg(pg)
                .setOrderId("1234")
                .setMethod(method)
                .setPrice(price)
                .setUser(user)
                .setExtra(extra)
                .setItems(items);
        Map<String, Object> map = new HashMap<>();
        map.put("1", "abcdef");
        map.put("2", "abcdef55");
        map.put("3", 1234);
        payload.setParams(map);

        Bootpay.init(getSupportFragmentManager(), getApplicationContext())
                .setPayload(payload)
                .setEventListener(new BootpayEventListener() {
                    @Override
                    public void onCancel(String data) {
                        Log.d("cancel", data);
                    }

                    @Override
                    public void onError(String data) {
                        Log.d("error", data);
                    }

                    @Override
                    public void onClose(String data) {
                        Log.d("close", data);
                        Bootpay.removePaymentWindow();
                    }

                    @Override
                    public void onReady(String data) {
                        Log.d("ready", data);
                    }

                    @Override
                    public boolean onConfirm(String data) {
                        Log.d("confirm", data);
//                        Bootpay.transactionConfirm(data); //재고가 있어서 결제를 진행하려 할때 true (방법 1)
                        return true; //재고가 있어서 결제를 진행하려 할때 true (방법 2)
//                        return false; //결제를 진행하지 않을때 false
                    }

                    @Override
                    public void onDone(String data) {
                        Log.d("done", data);
                    }
                }).requestPayment();
    }
}
```

## 결제진행 이벤트

{% hint style="info" %}
결제 진행 상태에 따라 LifeCycle 함수가 실행됩니다. 각 함수에 대한 상세 설명은 아래를 참고하세요.
{% endhint %}

{% tabs %}
{% tab title="onError 함수" %}
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

{% tab title="onCancel 함수" %}
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

{% tab title="onReady 함수" %}
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

{% tab title="onConfirm 함수" %}
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

{% tab title="onDone 함수" %}
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

{% hint style="warning" %}
**결제 테스트 시 주의사항**

1. 결제 테스트시에는 소액(1,000원)으로 진행해주세요.
2. 가상계좌는 결제테스트하지 말아주세요.
3. 휴대폰 결제는 이월될 경우 취소되지 않습니다.

테스트로 결제를 하시더라도 실제 결제가 될 수 있습니다. 자동취소가 누락되어 취소되지 않을 수 있으니 테스트 결제는 소액으로 진행해주세요.

가상계좌의 경우 계좌간 계좌이체 방식이기 때문에 이체간 비용이 발생하기에 PG사에서 정책적으로 가상계좌는 테스트 시 결제취소 기능을 제공하지 않습니다. 가상계좌를 테스트 하기 위해서는 PG사 가맹 후 발급받은 코드로 진행하시길 추천합니다.
{% endhint %}

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
