# 본인인증

![](<../.gitbook/assets/스크린샷 2021-07-21 오전 11.29.50 (1).png>)

휴대폰 본인확인 서비스로 고객의 이름, 성별, 생년월일, 전화번호 등의 정보를 이통사로부터 얻어낼 수 있어 인증된 회원 서비스를 만들 수 있는 효과가 있습니다.&#x20;

## 1-1. Bootpay Client SDK 설치하기&#x20;

각 개발언어별로 정리된 [설치하기](auth.md#1-1.-bootpay-client-sdk)를 참조해주세요.

## 1-2. 본인인창 띄우는 예제 코드&#x20;

{% tabs %}
{% tab title="JavaScript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	price: 0, // 본인인증은 0으로 해야 한다.
	application_id: "[ WEB SDK용 Application ID ]",
	name: '본인인증', //결제창에서 보여질 이름
	pg: 'danal',
	method: 'auth', // 빌링키를 받기 위한 결제 수단
	show_agree_window: 0, // 부트페이 정보 동의 창 보이기 여부
	order_id: '고유order_id_1234', //고유 주문번호로, 생성하신 값을 보내주셔야 합니다.
	params: {callback1: '그대로 콜백받을 변수 1', callback2: '그대로 콜백받을 변수 2', customvar1234: '변수명도 마음대로'},
	user_info: {
		phone: '[ 인증 번호 고정할 전화번호 ]'
	},
	extra: {
		carrier: '[ SKT, KT, LGT ] 고정할 통신사명',
		age_limit: '[ 제한할 최소 나이 ex) 20 -> 20살 이상만 인증이 가능 ]'
	}
}).error(function (data) {
	// 본인인증 진행시 에러가 발생하면 수행됩니다.
	console.log(data);
}).cancel(function (data) {
	// 본인인증이 취소되면 수행됩니다.
	console.log(data);
}).done(function (data) {
	// 본인인증이 완료되면 관련된 값이 리턴된다.
	console.log(data);
});
```
{% endtab %}

{% tab title="Flutter" %}
```dart
import 'package:bootpay/bootpay.dart';

import 'package:bootpay/model/extra.dart';
import 'package:bootpay/model/item.dart';
import 'package:bootpay/model/payload.dart';
import 'package:bootpay/model/user.dart';
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  Payload payload = Payload();

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
 

    // _payload = Payload();
    // _payload.app
    payload.applicationId = '5b8f6a4d396fa665fdc2b5e7';
    payload.androidApplicationId = '5b8f6a4d396fa665fdc2b5e8';
    payload.iosApplicationId = '5b8f6a4d396fa665fdc2b5e9';

    payload.pg = 'danal';
    payload.method = 'auth'; 
    payload.name = '본인인증';
    payload.price = 0; //본인인증의 경우 가격 0
    payload.orderId = DateTime.now().millisecondsSinceEpoch.toString();
    payload.params = {
      "callbackParam1" : "value12",
      "callbackParam2" : "value34",
      "callbackParam3" : "value56",
      "callbackParam4" : "value78",
    };

    User user = User();
    user.username = "사용자 이름";
    user.email = "user1234@gmail.com";
    user.area = "서울";
    user.phone = "010-4033-4678";
    user.addr = '서울시 동작구 상도로 222';

    Extra extra = Extra();
    extra.appScheme = 'bootpayFlutterExample';    
    //extra.carrier = "SKT"; //본인인증 시 고정할 통신사명, SKT,KT,LGT 중 1개만 가능 
    //extra.ageLimit = 20; // 본인인증시 제한할 최소 나이 ex) 20 -> 20살 이상만 인증이 가능

    payload.user = user;
    payload.extra = extra;
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: Builder(builder: (BuildContext context) {
          return Container(
            child: Center(
              child: TextButton(
                onPressed: () => Bootpay().request(
                  context: context,
                  payload: payload,
                  isMaterialStyle: false,
                  showCloseButton: false,
                  closeButton: Icon(Icons.close, size: 35.0, color: Colors.black54),
                  onCancel: (String data) {
                    print('------- onCancel: $data');
                  },
                  onError: (String data) {
                    print('------- onCancel: $data');
                  },
                  onClose: () {
                    print('------- onClose');
                  },
                  onReady: (String data) {
                    print('------- onReady: $data');
                  },
                  onConfirm: (String data) {
                    print('------- onConfirm: $data');
                    return true;
                  },
                  onDone: (String data) {
                    print('------- onDone: $data');
                  },
                ),
                child: Text('부트페이 결제테스트'),
              )
            ),
          );
        }),
      ),
    );
  }
}
```
{% endtab %}

{% tab title="Android" %}
```

public void goRequest(View v) {

    BootUser user = new BootUser().setPhone("010-1234-5678"); // 구매자 정보
//        BootExtra extra = new BootExtra().setQuotas(new int[] {0,2,3}).setPopup(1).setQuickPopup(1);  // 일시불, 2개월, 3개월 할부 허용, 할부는 최대 12개월까지 사용됨 (5만원 이상 구매시 할부허용 범위)
    BootExtra extra = new BootExtra()
            .setQuotas(new int[] {0,2,3})
            .setPopup(0)
            .setQuickPopup(0);  // 일시불, 2개월, 3개월 할부 허용, 할부는 최대 12개월까지 사용됨 (5만원 이상 구매시 할부허용 범위)
//                .setCarrier("SKT") //본인인증 시 고정할 통신사명, SKT,KT,LGT 중 1개만 가능
//                .setAgeLimit(40); // 본인인증시 제한할 최소 나이 ex) 20 -> 20살 이상만 인증이 가능
    Double price = 0d; //본인인증의 경우 0 

    String pg = "danal"
    String method = "auth";

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

```
{% endtab %}

{% tab title="iOS" %}
```
import Bootpay

@objc func showBootpay() {
let payload = Payload()
payload.applicationId = "5b8f6a4d396fa665fdc2b5e9" //ios application id

        
payload.price = 0 // 본인인증 일 경우 가격 0
payload.orderId = String(NSTimeIntervalSince1970)
payload.pg = "danal"
payload.method = "auth"
payload.name = "테스트 아이템"
payload.extra = BootExtra() 

//        payload.extra?.carrier = "SKT" //본인인증 시 고정할 통신사명, SKT,KT,LGT 중 1개만 가능
//        payload.extra?.ageLimit = 40 // 본인인증시 제한할 최소 나이 ex) 20 -> 20살 이상만 인증이 가능
 



let user = BootUser()
user.username = "테스트 유저"
user.phone = "01012345678"
payload.userInfo = user
        
Bootpay.requestPayment(viewController: self, payload: payload)
    .onCancel { data in
        print("-- cancel: \(data)")
    }
    .onReady { data in
        print("-- ready: \(data)")
    }
    .onConfirm { data in
        print("-- confirm: \(data)")
        return true //재고가 있어서 결제를 최종 승인하려 할 경우
//                            return false //재고가 없어서 결제를 승인하지 않을때
    }
    .onDone { data in
        print("-- done: \(data)")
    }
    .onError { data in
        print("-- error: \(data)")
    }
    .onClose {
        print("-- close")
    }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
&#x20;결제 진행 상태에 따라 LifeCycle 함수가 실행됩니다. 각 함수에 대한 상세 설명은 아래를 참고하세요.
{% endhint %}

{% tabs %}
{% tab title="error 함수" %}
1. **부트페이 관리자에서 활성화 하지 않은 PG, 결제수단을 사용하고자 할 때**
2. **PG에서 보내온 결제 정보를 부트페이 관리자에 잘못 입력하거나 입력하지 않은 경우**
3. **결제 진행 도중 한도초과, 카드정지, 휴대폰소액결제 막힘, 계좌이체 불가 등의 사유로 결제가 안되는 경우**
4. **PG에서 리턴된 값이 다른 Client에 의해 변조된 경우**

에러가 난 경우 해당 함수를 통해 관련 에러 메세지를 사용자에게 보여줄 수 있습니다.

&#x20;data 포맷은 아래와 같습니다.

```
{
  action: "BootpayError",
  message: "카드사 거절",
  receipt_id: "5fffab350c20b903e88a2cff"
}
```
{% endtab %}

{% tab title="cancel 함수" %}
결제 진행 중 사용자가 PG 결제창에서 취소 혹은 닫기 버튼을 눌러 나온 경우 입니다. ****&#x20;

&#x20;data 포맷은 아래와 같습니다.

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

&#x20; data 포맷은 아래와 같습니다.

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
결제 승인이 되기 전 호출되는 함수입니다. 승인 이전 관련 로직을 서버 혹은 클라이언트에서 수행 후 결제를 승인해도 될 경우`BootPay.transactionConfirm(data);`

코드를 실행해주시면 PG에서 결제 승인이 진행이 됩니다.

**\* 페이앱, 페이레터 PG는 이 함수가 실행되지 않고 바로 결제가 승인되는 PG 입니다. 참고해주시기 바랍니다.**

&#x20;data 포맷은 아래와 같습니다.

```
{
  receipt_id: "5fffc0460c20b903e88a2d2c",
  action: "BootpayConfirm"
}
```
{% endtab %}

{% tab title="done 함수" %}
PG에서 거래 승인 이후에 호출 되는 함수입니다. 결제 완료 후 다음 결제 결과를 호출 할 수 있는 함수 입니다.

`done(function(data) { console.log(data.receipt_id) })`

다음과 같이 클라이언트에서 receipt\_id를 받을 수 있습니다. 이 함수가 호출 된 후 반드시 REST API를 통해 [결제검증](https://docs.bootpay.co.kr/rest/verify)을 수행해야합니다. data 포맷은 아래와 같습니다.

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

## 2-1. Bootpay Server SDK 설치하기&#x20;

## **2-2. 본인인증 REST API로 검증하기**&#x20;

본인인증이 완료되면 클라이언트 SDK의 done 커스텀 함수 내에서 receipt\_id를 받고 이 값을 가지고 REST API로 유효성 검사를 할 수 있습니다. 전화번호, 성명, 생년월일등의 민감한 데이터를 가져오기 때문에 반드시 서버에서 수행되어야 합니다.

{% swagger baseUrl=" https://api.bootpay.co.kr" path="/certificate/:receipt_id" method="get" summary="" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="header" name="Authorization" type="string" %}
 부트페이에서 발급받은 토큰 값  
{% endswagger-parameter %}

{% swagger-parameter in="query" name="receipt_id" type="string" %}
 부트페이에서 받은 거래 영수증 id 
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```javascript
{
  "status": 200,
  "code": 0,
  "message": "",
  "data": {
    "receipt_id": "5df6e67d4f74b4002a77e0eb",
    "order_id": "1576461949",
    "name": "테스트결제",
    "price": 3000,
    "tax_free": 1500,
    "remain_price": 3000,
    "remain_tax_free": 1500,
    "cancelled_price": 0,
    "cancelled_tax_free": 0,
    "receipt_url": "https://app.bootpay.co.kr/bill/ZVN3cXdRNTlEbTYzVi8xNmIyWCtFS0VrcWJncXkweFBoT200WXRPQkgzVC9u%0AZz09LS1KNU1MRW5JdGNMa3RMZDRLLS1lWkhFaTFkMmVnVkR0SmRTMUxUNXdR%0APT0=%0A",
    "unit": "krw",
    "pg": "payapp",
    "method": "card_rebill_rest",
    "pg_name": "페이앱",
    "method_name": "카드정기결제(REST)",
    "payment_data": {
      "card_name": "하나",
      "card_no": "94308100****4569",
      "card_quota": "00",
      "card_auth_no": "09286221",
      "receipt_id": "5df6e67d4f74b4002a77e0eb",
      "n": "테스트결제",
      "p": 3000,
      "tid": "48585753",
      "pg": "페이앱",
      "pm": "카드정기결제(REST)",
      "pg_a": "payapp",
      "pm_a": "card_rebill_rest",
      "o_id": "1576461949",
      "p_at": "2019-12-16 11:05:50",
      "s": 1,
      "g": 2
    },
    "requested_at": "2019-12-16 11:05:50",
    "purchased_at": "2019-12-16 11:05:50",
    "status": 1,
    "status_en": "complete",
    "status_ko": "결제완료"
  }
}
```
{% endswagger-response %}
{% endswagger %}



{% tabs %}
{% tab title="CURL" %}
```perl
curl -H "Content-Type: application/json" \
-H "Authorization: d6941c650061e3eaddd3f4718ab63e0983c1f6a0a0a01370c1b1ffa90ddd0b51" \
https://api.bootpay.co.kr/receipt/5afd6be8e13f33616f2876ac

```
{% endtab %}

{% tab title="PHP" %}
## 설치하기&#x20;

[Composer](http://getcomposer.org)을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
composer require bootpay/backend-php
```

## 사용 예제&#x20;

```php


<?php
/*
 * 결제 검증 관련 예제입니다.
 */
require_once '../vendor/autoload.php';
use Bootpay\BackendPhp\BootpayApi; 


$bootpay = BootpayApi::setConfig(
    '5b8f6a4d396fa665fdc2b5ea',
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
);

$response = $bootpay->requestAccessToken();

$receiptId = '[[ receipt_id ]]';

if ($response->status === 200) {
    $result = $bootpay->certificate($receiptId);
    var_dump($result);
}
```
{% endtab %}

{% tab title="Ruby" %}
## 설치하기

[Gemfile](https://rubygems.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
gem 'backend-ruby'
```

위 라인 추가 후 `bundle install` 실행&#x20;

## 사용 예제

```php
# 결제 검증하기 
receipt_id = '612df0250d681b001de61de6'

api = Bootpay::Api.new(
  application_id: '5b8f6a4d396fa665fdc2b5ea',
  private_key:    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=',
)

api.request_access_token

if api.request_access_token.success?
  receipt_id = '612e09260d681b0021e61ab9'
  response = @api.certificate(receipt_id)
  puts response.data.to_json
end
```
{% endtab %}

{% tab title="Node.js" %}
## NPM 통해 설치하기&#x20;

```c
npm install bootpay-backend-nodejs
```

## 사용예제&#x20;

```javascript
async function certificate() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    let token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let response
        try {            
            response = await Bootpay.certificate('612df0250d681b001de61de6')
        } catch (e) {
            return console.log(e)
        }
        console.log(response)
    }
}
```
{% endtab %}

{% tab title="Python" %}
## 설치하기

[Pypl](https://pypi.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-python) 코드 보기)

```javascript
pip install bootpay 
```

## 사용 예제

```python
from bootpay import Bootpay

bootpay = Bootpay('5b8f6a4d396fa665fdc2b5ea', 'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=')
result = bootpay.get_access_token() # 토큰 얻어오기

receipt_id = '612e09260d681b0021e61ab9' # 검증할 결제 영수증 ID
print(bootpay.certificate(receipt_id)) # 결제검증하기
```
{% endtab %}

{% tab title="Java" %}
## 설치하기

[Gradle](https://gradle.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-java) 코드 보기)

{% code title="build.gradle" %}
```javascript
repositories {
    maven { url 'https://jitpack.io' }
    mavenCentral()
}

dependencies {
    implementation 'com.github.bootpay:backend-java:+'
}
```
{% endcode %}

## 사용 예제

```java
import kr.co.bootpay.Bootpay;

bootpay = new Bootpay("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");

public static void certificate() {
    String receiptId = "593f8febe13f332431a8ddae";
    try {
        ResDefault<CertificateData> res = bootpay.certificate(receiptId);
        System.out.println(res.toJson());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```
{% endtab %}

{% tab title="Go" %}
## 설치하기 ([Github](https://github.com/bootpay/backend-go) 주소)

```javascript
go get github.com/bootpay/backend-go
```

## 사용 예제

```go
package main

import (
    "fmt"
    "github.com/bootpay/backend-go"
)

func main() {
    api := bootpay.Api{}.New("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=", nil, "")
    api.GetToken()
    Certificate(api) 
}

func Certificate(api *bootpay.Api) {    
    fmt.Println("--------------- GetToken() Start ---------------")
    token, err := api.GetToken()
    fmt.Println("token : " + token.Data.Token)
    if err != nil {
	fmt.Println("get token error: " + err.Error())
    }
    fmt.Println("--------------- GetToken() Start ---------------")
}
```
{% endtab %}

{% tab title="ASP.NET" %}
### 1. Visual Studio에서 추가하기

1\. 솔루션 탐색기(Solution Explorer) 열기 \
2\. 만드신 솔루션 프로젝트 우클릭 \
3\. Manage Nuget Packages 클릭 \
4-1.  '[Bootpay.framework](https://www.nuget.org/packages/Bootpay.framework)' (.net standard 2.0 이상)\
4-2.  또는 '[Bootpay.net](https://www.nuget.org/packages/Bootpay.net)' (.net core 3.1 이상)

## 2. 사용 예제&#x20;

```javascript
BootpayApi api = new BootpayApi("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");
await api.GetAccessToken();

string receiptId = "612df0250d681b001de61de6";
            
var res = await api.Certificate(receiptId);

string json = JsonConvert.SerializeObject(res,
    Newtonsoft.Json.Formatting.None,
    new JsonSerializerSettings
    {
        NullValueHandling = NullValueHandling.Ignore
    });


return Ok(json);
```
{% endtab %}
{% endtabs %}

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;

