# 결제 취소하기

결제취소 요청시 `price`를 지정하지 않을 경우 전액 취소 처리됩니다.

결제취소 요청시 `price`를 지정하면 부분취소가 가능합니다.\
`(지원가능 PG사: inicis, kcp, danal, payletter, nicepay, kakao, payco)`

{% hint style="info" %}
**부분취소 중복 요청 막기**

개발사의 실수로 인해서 부분취소를 중복 요청하는 불상사가 생길 수 있습니. 이를 방지하고자 부트페이는 동일한 receipt\_id에 대해 10초간 부분취소 중복방지 기능이 있습니다. 활성화 후 사용하시길 추천합니다.
{% endhint %}

## **결제 취소하기 REST API**

{% swagger baseUrl=" https://api.bootpay.co.kr" path="/cancel.json" method="post" summary="" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="header" name="Access Token" type="string" required="false" %}
**부트페이에서 발급받은토큰 값**
{% endswagger-parameter %}

{% swagger-parameter in="body" name="receipt_id" type="string" required="false" %}
부트페이에서 발급받은 거래 영수증 id
{% endswagger-parameter %}

{% swagger-parameter in="body" name="cancel_id" type="string" required="false" %}
부분 취소 중복을 막기 위한 값

\\

가맹점에서 지정하는 cancel\_id를 보내면, 실수로 중복 요청한 경우 취소가 되지 않도록 방지처리
{% endswagger-parameter %}

{% swagger-parameter in="body" name="price" type="string" required="false" %}
부분 취소할 취소금액, 금액을 적지 않으면 전책 취소 처리 됨

\\

\* 1,000원보다 더 큰 값이어야 함

\\

\* 일부 PG사만 가능

\\

(inicis, kcp, danal, payletter, nicepay, kakao, payco)
{% endswagger-parameter %}

{% swagger-parameter in="body" name="name" type="string" required="false" %}
취소자 이름
{% endswagger-parameter %}

{% swagger-parameter in="body" name="reason" type="string" required="false" %}
취소 사유
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```javascript
{
  "status": 200,
  "code": 0,
  "message": "",
  "data": {
    "receipt_id": "5df6e8034f74b4002177e0bb",
    "request_cancel_price": 3000,
    "remain_price": 0,
    "remain_tax_free": 0,
    "cancelled_price": 3000,
    "cancelled_tax_free": 1500,
    "revoked_at": "2019-12-16 11:12:28",
    "tid": "48586080"
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Data          | 타입      | 설명                         |
| ---------------------- | ------- | -------------------------- |
| receipt\_id            | string  | 부트페이에서 발급한 고유 영수증 키        |
| request\_cancel\_price | integer | 요청한 취소 금액                  |
| remain\_price          | integer | 취소후 남은 금액 ( 부분 취소시 남은 금액 ) |
| remain\_tax\_free      | integer | 남은 면세 금액                   |
| cancelled\_price       | integer | 총 취소된 금액                   |
| cancelled\_tax\_free   | integer | 총 취소된 면세 금액                |
| revoked\_at            | string  | 취소된 시각 ( +09:00 기준 )       |
| tid                    | string  | PG에서 사용하는 거래 ID            |

## 언어별 예제

{% tabs %}
{% tab title="CURL" %}
```perl
curl -H "Content-Type: application/json" \
-H "Authorization: d6941c650061e3eaddd3f4718ab63e0983c1f6a0a0a01370c1b1ffa90ddd0b51" \
-d '{"receipt_id": "[[ receipt_id ]]", "price": "취소금액", "name": "취소자명", "reason": "취소사유"}' \
-X POST https://api.bootpay.co.kr/cancel.json
```
{% endtab %}

{% tab title="PHP" %}
### 설치하기

[Composer](http://getcomposer.org)을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
composer require bootpay/backend-php
```

### 사용 예제

```php
<?php 
/*
 * 취소 테스트 예제 입니다.
 */ 
require_once '../vendor/autoload.php';
use Bootpay\BackendPhp\BootpayApi; 


$bootpay = BootpayApi::setConfig(
    '5b8f6a4d396fa665fdc2b5ea',
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
);

$response = $bootpay->requestAccessToken();

$receiptId = '613f101f0d681b0023e6e53f';

if ($response->status === 200) { 
    // $result = $bootpay->cancel($receiptId);
    $response = $bootpay->cancel(
        '613f101f0d681b0023e6e53f', 
        null, 
        'API 관리자',
        'API에 의한 요청',
        time(),
        null,
        [
            'account' => '66569112432134',
            'accountholder' => '홍길동',
            'bankcode' => BootpayApi::BankCode['국민은행']
        ]
    );
    var_dump($response);
}
```
{% endtab %}

{% tab title="Ruby" %}
### 설치하기

Gemfile을 통해 설치 (Github 주소)

```concurnas
gem 'backend-ruby'
```

위 라인 추가 후 `bundle install` 실행

### 사용 예제

```ruby
require 'bootpay-rest-client'

bootpay = Bootpay::ServerApi.new(
    '[[ application_id ]]',
    '[[ private_key ]]'
)
api.request_access_token
if api.request_access_token.success?
    response = @api.cancel_payment(
      receipt_id:      "612df0250d681b001de61de6",
      cancel_username:        'test',
      cancel_message:         'test',
      cancel_price:     1000, # 부분취소 요청시
      refund: { # 가상계좌 환불 요청시, 단 CMS 특약이 되어있어야만 환불요청이 가능하다.
        account: '675601010124', # 환불계좌
        accountholder: '홍길동', # 환불계좌주
        bankcode: Bootpay::BANKCODE["국민은행"] # 은행코드
      }
    )
    puts  response.data.to_json
end
```
{% endtab %}

{% tab title="Node.js" %}
### NPM 통해 설치하기

```c
npm install bootpay-backend-nodejs
```

### 사용예제

```javascript
async function cancel() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    let token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let response
        try { 
            response = await Bootpay.cancel({
                receiptId: '612df0250d681b001de61de6',
                price: 1000,
                name: '취소자명',
                reason: '취소합니다'                
            })
        } catch (e) {
            console.log(e)
            return
        }
        console.log(response)
    }
}
```
{% endtab %}

{% tab title="Python" %}
### 설치하기

[Pypl](https://pypi.org)을 통해 설치 ([github](https://github.com/bootpay/backend-python) 코드 보기)

### 사용 예

```python
from bootpay import Bootpay

bootpay = Bootpay('5b8f6a4d396fa665fdc2b5ea', 'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=')
result = bootpay.get_access_token() # 토큰 얻어오기

receipt_id = '612df0250d681b001de61de6'
result = bootpay.cancel(
  receipt_id
)
```
{% endtab %}

{% tab title="Java" %}
### 설치하기

gradle을 통해 설치

{% code title="build.gradle" %}
```
repositories {
    maven { url 'https://jitpack.io' }
    mavenCentral()
}

dependencies {
    implementation 'com.github.bootpay:backend-java:+'
}
```
{% endcode %}

### 사용 예제

```java
import kr.co.bootpay.Bootpay;
import kr.co.bootpay.model.response.ResDefault;


bootpay = new Bootpay("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");
        
public static void receiptCancel() {
    Cancel cancel = new Cancel();
    cancel.receiptId = "6100e77a019943003650f4d5";
    cancel.name = "관리자";
    cancel.reason = "테스트 결제";
//        cancel.price = 1000.0; //부분취소 요청시
//        cancel.cancelId = "12342134"; //부분취소 요청시, 중복 부분취소 요청하는 실수를 방지하고자 할때 지정
//        RefundData refund = new RefundData(); // 가상계좌 환불 요청시, 단 CMS 특약이 되어있어야만 환불요청이 가능하다.
//        refund.account = "675601012341234"; //환불계좌
//        refund.accountholder = "홍길동"; //환불계좌주
//        refund.bankcode = BankCode.getCode("국민은행");//은행코드
//        cancel.refund = refund;

    try {
        ResDefault<Cancel> res = bootpay.receiptCancel(cancel);
        System.out.println(res.toJson());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```
{% endtab %}

{% tab title="Go" %}
### 설치하기 ([Github](https://github.com/bootpay/backend-go) 주소)

```javascript
go get github.com/bootpay/backend-go
```

### 사용 예제

```go
package main

import (
    "fmt"
    "github.com/bootpay/backend-go"
)

func main() {
    api := bootpay.Api{}.New("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=", nil, "")
    api.GetToken()
    ReceiptCancel(api) 
}

func ReceiptCancel(api *bootpay.Api) {
    receiptId := "610cc0cb7b5ba40044b04530"
    name := "관리자"
    reason := "테스트 결제 취소를 테스트"
    fmt.Println("--------------- ReceiptCancel() Start ---------------")
    cancel, err := api.ReceiptCancel(receiptId, 0, name, reason, bootpay.RefundData{})

    fmt.Println(cancel)
    if err != nil {
	fmt.Println("get token error: " + err.Error())
    }
    fmt.Println("--------------- ReceiptCancel() End ---------------")
}
```
{% endtab %}

{% tab title="ASP.NET" %}
#### 1. Visual Studio에서 추가하기

1\. 솔루션 탐색기(Solution Explorer) 열기\
2\. 만드신 솔루션 프로젝트 우클릭\
3\. Manage Nuget Packages 클릭\
4-1. '[Bootpay.framework](https://www.nuget.org/packages/Bootpay.framework)' (.net standard 2.0 이상)\
4-2. 또는 '[Bootpay.net](https://www.nuget.org/packages/Bootpay.net)' (.net core 3.1 이상)

### 2. 사용 예제

```javascript
BootpayApi api = new BootpayApi("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");
await api.GetAccessToken();

Cancel cancel = new Cancel();
cancel.receiptId = "6100e77a019943003650f4d5";
cancel.name = "관리자";
cancel.reason = "테스트 결제";

//cancel.price = 1000.0; //부분취소 요청시
//cancel.cancelId = "12342134"; //부분취소 요청시, 중복 부분취소 요청하는 실수를 방지하고자 할때 지정
//RefundData refund = new RefundData(); // 가상계좌 환불 요청시, 단 CMS 특약이 되어있어야만 환불요청이 가능하다.
//refund.account = "675601012341234"; //환불계좌
//refund.accountholder = "홍길동"; //환불계좌주
//refund.bankcode = BankCode.getCode("국민은행");//은행코드
//cancel.refund = refund;


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

## 결제취소가 안 될 경우

### **테스트 결제모드일 경우**

테스트 결제모드 일 경우 PG사의 스케쥴러의 의해 당일 취소처리 될 수 있습니다. 이럴 경우 부트페이 API를 통해 취소된 건이 아니기 때문에 부트페이 전산에 반영되지 않아 결제완료 상태로 남을 수 있으며, 취소 요청시 '\*\*기취소된 건입니다'\*\*와 같은 응답결과가 리턴됩니다. 취소된 건이니 다시 취소요청하실 필요는 없으십니다.

### **실결제 모드일 경우**

1. **기취소된 건 일 경우**\
   PG사 전산에서 관리자에 의해 수동 또는 강제취소 되었을 경우, Bootpay API를 통해 취소요청 된 건이 아니기 때문에 부트페이 전산과 결제상태가 동기화 되지 않습니다. 이때 먼저 PG사 전산에서 해당 결제건이 취소 상태가 아닌지 확인이 필요합니다.\\
2. **PG사 내부정책 - 정산예정 금액이 취소금액 보다 큰 경우**\
   결제취소 API 요청 시 PG사로부터 결제취소가 승인나지 않을 수 있습니다. 일반적으로 상계취소 처리 조건이 성립하지 않는 경우가 그러합니다. (PG사로부터 승인거절된 건인지는 [결제오류 확인하기](../advance/error-check.md)를 참조하세요)\\
3. PG사 내부정책 - 가맹점별 취소요청금액의 한도가 있을 경우\
   PG사에서 가맹점별 리스크 관리 정책으로 내부적으로 취소한도가 있을 수 있습니다. 이때 취소한도 금액보다 취소금액이 클 경우 PG사의 정책에 의해 취소요청이 반려될 수 있습니다. 이에 대한 문의는 이용하시는 PG사에 하시면 되겠습니다.

## PG사 전산에 부트페이 IP 등록하기

KCP를 이용하실 경우 부트페이 IP를 KCP 전산에 등록해야 결제취소가 가능하십니다.

[KCP전산](https://admin8.kcp.co.kr)에 로그인 하신 후 `상점정보관리 → 정보변경 → 결제서버 IP 설정`에서 부트페이 Outbound IP인 223.130.82.4 를 추가해주시면 되겠습니다.

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
