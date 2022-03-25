# 결제 검증하기

## 결제 검증을 해야하는 이유&#x20;

결제는 일반적으로 클라이언트에서 요청되기 때문에 위변조가 가능하기에, 정상적인 결제건인지 서버간 통신으로 검증을 하는 작업이 필수적으로 필요합니다. 서버간 통신시 [IP 보안 정책](token.md) 설정하길 추천드립니다.

{% hint style="info" %}
**웹 프락시 스니핑에 의한 변조 가능성도 있지 않나요?**

부트페이는 HTTPS 통신을 하기 때문에 중간에 패킷 감청을 통한 변조는 매우 어렵습니다. 하지만 최근 웹 애드온 중에 프락시 형태로 동작하는 불법 프로그램이 있습니다.  이 애드온은 웹브라우저에서 작동하기 때문에 HTTPS의 패킷감청과 상관없이 웹브라우저에서 직접 값을 변조할 수 있습니다.  이에 특정 요청하는 변수 값 (ex. 결제금액, 결제 아이템 등)의 변조를 어렵게 하기 위해 부트페이로 요청하는 값들을 암호화하여 보내고 있습니다. **( 부트페이 JS SDK 버전 2.0.0 이상 )**
{% endhint %}

결제 검증을 해야하는 시점은 클라이언트 기준 `done 함수`가 호출(결제 완료 시점) 되었을 때 또는 ajax 혹은 form submit으로 페이지를 이동하여 직접 구축하신 서버에서 결제 검증을 수행하시면 됩니다.&#x20;

## **결제 검증하기 예제**

{% swagger baseUrl=" https://api.bootpay.co.kr" path="/receipt/:receipt_id" method="get" summary="" %}
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
use Bootpay\BackendPhp\BootpayApi; 

$receiptId = '612c31000199430036b5165d'; //부트페이 영수증 번호 

$bootpay = BootpayApi::setConfig(
    '5b8f6a4d396fa665fdc2b5ea', //발급받은 rest application id
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=' //발급받은 prviate_key
);

$response = $bootpay->requestAccessToken();

// Token이 발행되면 그 이후에 verify 처리 한다.
if ($response->status === 200) {
    $result = $bootpay->verify($receiptId);
    var_dump($result);
    // 원래 주문했던 금액이 일치하는가?
    // 그리고 결제 상태가 완료 상태인가?
    if ($result->data->price === price && $result->data->status === 1) {
        // TODO: 이곳이 상품 지급 혹은 결제 완료 처리를 하는 로직으로 사용하면 됩니다.
    }
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
  response = api.verify(receipt_id)
  print  response.data.to_json
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
async function verify() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    const token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let result
        try {
            result = await Bootpay.verify('612df0250d681b001de61de6')
        } catch (e) {
            return console.log(e)
        }
        console.log(result)
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
print(bootpay.verify(receipt_id)) # 결제검증하기
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
import kr.co.bootpay.model.response.ResDefault;

void getAccessToken() {
    Bootpay bootpay = new Bootpay("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");

    try {
        bootpay.getAccessToken(); //토큰 가져오기 
        ResDefault<VerificationData> res = bootpay.verify("6100e8e7019943003850f9b0"); //결제 검증하기 
        System.out.println(res.toJson());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```
{% endtab %}

{% tab title="Go" %}
## 설치하기&#x20;

[Github](https://github.com/bootpay/backend-go)을 통해 설치할 수 있습니다&#x20;

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
    GetVerify(api) 
}

func GetVerify(api *bootpay.Api) {
    receiptId := "610c96352386840036db8bef"
    fmt.Println("--------------- GetVerify() Start ---------------")
    verify, err := api.Verify(receiptId)

    fmt.Println(verify)
    fmt.Println(verify.Data.PaymentData)
    fmt.Println(verify.Data.PaymentData["o_id"])
    if err != nil {
	fmt.Println("get token error: " + err.Error())
    }
    fmt.Println("--------------- GetVerify() End ---------------")
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
var res = await api.Verify("612df0250d681b001de61de6");

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



| Response Data        | 타입      | 설명                                                                                                                                                                                                                                                                                                                                                                                                        |
| -------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| receipt\_id          | string  | 부트페이에서 발급하는 고유 영수증 ID                                                                                                                                                                                                                                                                                                                                                                                     |
| order\_id            | string  | 부트페이로 부터 결제 요청시 보냈던 주문번호                                                                                                                                                                                                                                                                                                                                                                                  |
| name                 | string  | 판매된 대표 상품명                                                                                                                                                                                                                                                                                                                                                                                                |
| price                | integer | 결제된 금액                                                                                                                                                                                                                                                                                                                                                                                                    |
| tax\_free            | integer | 결제된 면세 금액                                                                                                                                                                                                                                                                                                                                                                                                 |
| remain\_tax\_free    | integer | 남은 면세 금액                                                                                                                                                                                                                                                                                                                                                                                                  |
| remain\_price        | integer | 총 금액 - 취소 금액 ( 부분 취소후 남은 금액 )                                                                                                                                                                                                                                                                                                                                                                             |
| cancelled\_price     | integer | 총 취소된 금액                                                                                                                                                                                                                                                                                                                                                                                                  |
| cancelled\_tax\_free | integer | 총 취소된 면세 금액                                                                                                                                                                                                                                                                                                                                                                                               |
| unit                 | string  | 판매된 결제 단위 ( ex. krw - 한국 원, usd - 미국 달러 )                                                                                                                                                                                                                                                                                                                                                                 |
| pg                   | string  | 결제된 PG의 Alias ( ex. danal, inicis, kcp )                                                                                                                                                                                                                                                                                                                                                                  |
| method               | string  | 결제된 수단 Alias ( ex. card, vbank, bank, phone )                                                                                                                                                                                                                                                                                                                                                             |
| pg\_name             | string  | 결제된 PG사의 명칭                                                                                                                                                                                                                                                                                                                                                                                               |
| method\_name         | string  | 결제된 수단의 명칭                                                                                                                                                                                                                                                                                                                                                                                                |
| payment\_data        | object  | <p>PG사에서 보내온 결제 raw 데이터</p><p><a href="../code/response-data.md">* 각 결제수단별 payment_data 리턴값 보기</a></p>                                                                                                                                                                                                                                                                                                    |
| requested\_at        | string  | 결제가 처음 요청된 시각 ( 한국 기준시 +09:00 )                                                                                                                                                                                                                                                                                                                                                                           |
| purchased\_at        | string  | 결제 승인이 된 시각 ( 한국 기준시 +09:00 )                                                                                                                                                                                                                                                                                                                                                                             |
| revoked\_at          | string  | 결제가 취소된 시각 ( 한국 기준시 +09:00 )                                                                                                                                                                                                                                                                                                                                                                              |
| status               | integer | <p></p><p><strong>결제 상태</strong></p><ul><li>0 - 결제 대기 상태입니다. 승인이 나기 전의 상태입니다.</li><li>1 - 결제 완료된 상태입니다.</li><li>2 - 결제승인 전 상태입니다. <br>transactionConfirm() 함수를 호출하셔서 결제를 승인해야합니다.</li><li>3 - 결제승인 중 상태입니다. PG사에서 transaction 처리중입니다.</li><li>20 - 결제가 취소된 상태입니다.</li><li>-20 - 결제취소가 실패한 상태입니다.</li><li>-30 - 결제취소가 진행중인 상태입니다.</li><li>-1 - 오류로 인해 결제가 실패한 상태입니다.</li><li>-2 - 결제승인이 실패하였습니다.</li></ul> |

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
