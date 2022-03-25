# 2. 빌링키로 결제 요청하기

[빌링키를 발급](undefined.md)받으셨다면, 발급받은 빌링키로 결제를 요청할 수 있습니다. 스케쥴러 등을 통해 결제를 요청하시고자 하는 시점에 아래 API를 수행하면 되며, 미리 결제를 예약하고 싶으시다면 [결제 예약하기](undefined-2.md)를 참조하세요.

## **발급받은 빌링키로 결제하기 REST API**

결제를 원하는 시점에, 원하는 금액으로 결제를 승인요청 할 수 있습니다.&#x20;

{% swagger baseUrl="https://api.bootpay.co.kr" path="/subscribe/billing.json" method="post" summary="" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="body" name="billing_key" type="string" %}
부트페이를 통해 발급받은 빌링키
{% endswagger-parameter %}

{% swagger-parameter in="body" name="item_name" type="string" %}
결제할 상품명
{% endswagger-parameter %}

{% swagger-parameter in="body" name="order_id" type="string" %}
개발사에서 관리하는 고유 거래 번호
{% endswagger-parameter %}

{% swagger-parameter in="body" name="price" type="integer" %}
결제할 금액
{% endswagger-parameter %}

{% swagger-parameter in="body" name="quota" type="integer" %}
할부 개월수  

\


결제할 금액이 5만원 이상부터 설정 가능 

\


무이자 여부는 해당 PG사 공지사항을 참조해주세요
{% endswagger-parameter %}

{% swagger-parameter in="body" name="items" type="array" %}
아래의 Object 형태를 Array 형태로 보냅니다 

\


구매시 통계를 위한 아이템 정보입니다. 

\


(string) 

`item_name`

 \- 아이템 명

\


(integer) 

`qty`

 \-  구매할 상품 수 

\


(string) 

`unique`

 \- 개발사에서 관리하는 아이템 고유 유니크 값

\


(integer) 

`price`

 \- 상품 하나당 금액 

\


(string) 

`cat1`

 \- 카테고리 1

\


(string) 

`cat2`

 \- 카테고리 2

\


(string) 

`cat3`

 \- 카테고리 3
{% endswagger-parameter %}

{% swagger-parameter in="body" name="feedback_url" type="string" %}
결제 상태 변경시 (완료/취소) 통지받을 URL
{% endswagger-parameter %}

{% swagger-parameter in="body" name="feedback_content_type" type="string" %}
feedback_url로 받을 content-type 설정 

\




`json`

 \- json으로 리턴

\




`urlencoded`

 \- urlencoded로 리턴 

\


(기본: 

`urlencoded`

)
{% endswagger-parameter %}

{% swagger-parameter in="body" name="user_info" type="object" %}
payapp은 구매자명, 전화번호를 필수로 입력해야 합니다.

\


(string) 

`phone`

 \- 구매자 전화번호 

\


(string) 

`username`

 \- 구매자명 

\


(string) 

`email`

 \- 구매자 이메일
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```javascript
{
  "status": 200,
  "code": 0,
  "message": "",
  "data": {
    "receipt_id": "5b0f88dce13f332c6948d6ee",
    "price": 3000,
    "card_no": "9430810000004569",
    "card_code": "CCHN",
    "card_name": "하나카드",
    "card_quota": "00",
    "params": null,
    "item_name": "테스트 정기결제",
    "order_id": "1527744732",
    "url": null,
    "payment_name": "카드정기결제",
    "pg_name": "KCP",
    "pg": "kcp",
    "method": "card_rebill",
    "method_name": "카드정기결제",
    "requested_at": "2018-05-31 14:32:12",
    "purchased_at": "2018-05-31 14:32:12",
    "status": 1
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Data  | 타입      | 설명                                                                                                                                                                                                                                                                                                                                                                                                    |
| -------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| receipt\_id    | string  | <p>부트페이에서 발행한 영수증 고유값<br><strong>* 취소/결제확인 용도로 사용되는 값으로 데이터베이스에 저장해야합니다.</strong></p>                                                                                                                                                                                                                                                                                                                 |
| price          | integer | 결제 승인된 금액                                                                                                                                                                                                                                                                                                                                                                                             |
| card\_no       | string  | 승인된 카드 번호                                                                                                                                                                                                                                                                                                                                                                                             |
| card\_code     | string  | 카드사 코드                                                                                                                                                                                                                                                                                                                                                                                                |
| card\_name     | string  | 결제된 카드사 명                                                                                                                                                                                                                                                                                                                                                                                             |
| card\_quota    | string  | 할부 기간                                                                                                                                                                                                                                                                                                                                                                                                 |
| params         | string  | 결제시 요청했던 parameters                                                                                                                                                                                                                                                                                                                                                                                   |
| item\_name     | string  | 판매되었던 아이템 이름                                                                                                                                                                                                                                                                                                                                                                                          |
| order\_id      | string  | 결제 요청시 보냈던 주문 번호                                                                                                                                                                                                                                                                                                                                                                                      |
| pg\_name       | string  | 결제된 PG사명                                                                                                                                                                                                                                                                                                                                                                                              |
| pg             | string  | 결제된 PG사의 코드 값                                                                                                                                                                                                                                                                                                                                                                                         |
| method         | string  | 결제 수단 코드 값                                                                                                                                                                                                                                                                                                                                                                                            |
| method\_name   | string  | 결제수단 명                                                                                                                                                                                                                                                                                                                                                                                                |
| requested\_at  | string  | 결제가 요청된 시간 ( +09:00 기준 )                                                                                                                                                                                                                                                                                                                                                                              |
| purchased\_at  | string  | 결제된 시각 ( +09:00 기준 )                                                                                                                                                                                                                                                                                                                                                                                  |
| status         | integer | <p></p><p>결제 상태</p><ul><li>0 - 결제 대기 상태입니다. 승인이 나기 전의 상태입니다.</li><li>1 - 결제 완료된 상태입니다.</li><li>2 - 결제승인 전 상태입니다. <br><code>transactionConfirm()</code> 함수를 호출하셔서 결제를 승인해야합니다.</li><li>3 - 결제승인 중 상태입니다. PG사에서 transaction 처리중입니다.</li><li>20 - 결제가 취소된 상태입니다.</li><li>-20 - 결제취소가 실패한 상태입니다.</li><li>-30 - 결제취소가 진행중인 상태입니다.</li><li>-1 - 오류로 인해 결제가 실패한 상태입니다.</li><li>-2 - 결제승인이 실패하였습니다.</li></ul> |

## 언어별 예제&#x20;

{% tabs %}
{% tab title="CURL" %}
```perl
curl -H "Content-Type: application/json" \
-H "Authorization: d6941c650061e3eaddd3f4718ab63e0983c1f6a0a0a01370c1b1ffa90ddd0b51" \
-d '{"billing_key": "[[ billing_key ]]", "price": 3000, "item_name": "정기결제 테스트아이템", "order_id": "[[ order_id ]]", "items": [{"item_name": "정기 결제 아이템", "qty": 1, "unique": "아이템 유니크값", "price": 3000, "cat1": "카테고리 1", "cat2": "카테고리 2", "cat3": "카테고리 3"}]}' \
-X POST https://api.bootpay.co.kr/subscribe/billing.json
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
 * 결제링크 생성하기 관련 예제입니다. 
 */
require_once '../vendor/autoload.php';
use Bootpay\BackendPhp\BootpayApi; 

$bootpay = BootpayApi::setConfig(
    '5b8f6a4d396fa665fdc2b5ea',
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
);

$response = $bootpay->requestAccessToken();

if ($response->status === 200) {

   $response = $bootpay->subscribeCardBilling(
    '613af2600199430027b5cb83',
    time(),
    '정기결제 테스트 아이템',
    1000
    );

   var_dump($response); 
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
  response = api.subscribe_billing(
      billing_key:    billing_key,
      item_name:      '테스트 결제',
      price:          1000,
      tax_free:       1000,
      order_id:       '1234'
    )
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
async function subscribeBilling() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    let token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let response
        try {
            response = await Bootpay.requestSubscribeBillingPayment({
                billingKey: '612deb53019943001fb52312',
                itemName: '테스트',
                price: 1000,
                orderId: (new Date()).getTime(),
                feedbackUrl: 'https://www.yourdomain.com/callback',
                feedbackContentType: 'json'
            })
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
bootpay.get_access_token() # 토큰 얻어오기

billing_key = '612deb53019943001fb52312'
result = bootpay.subscribe_billing(
    billing_key,
    '정기 결제 테스트 아이템',
    3000,
    '12345',
    [],
    {'username': 'test'}
)
print(result)
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

public static void requestSubscribe() {
    SubscribePayload payload = new SubscribePayload();
    payload.billingKey = "6100e8c80d681b001dd4e0d7";
    payload.itemName = "아이템01";
    payload.price = 1000;
    payload.orderId = "" + (System.currentTimeMillis() / 1000);

    try {
        ResDefault<SubscribeBillingData> res = bootpay.requestSubscribe(payload);
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
    RequestSubscribe(api) 
}

func RequestSubscribe(api *bootpay.Api) {
    fmt.Println("--------------- RequestSubscribe() Start ---------------")
    payload := bootpay.SubscribePayload{
	BillingKey: "6100e8c80d681b001dd4e0d7",
	ItemName: "테스트아이템",
	Price: 1000,
	OrderId: fmt.Sprintf("%+8d", (time.Now().UnixNano() / int64(time.Millisecond))),
    }
    res, err := api.RequestSubscribe(payload)
    fmt.Println(res)
    if err != nil {
	fmt.Println("get token error: " + err.Error())
    }
    fmt.Println("--------------- RequestSubscribe() End ---------------")
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

Subscribe subscribe = new Subscribe();
subscribe.itemName = "정기결제 테스트 아이템";
subscribe.orderId = "" + DateTimeOffset.UtcNow.ToUnixTimeMilliseconds();
subscribe.pg = "nicepay"; 

subscribe.cardNo = "5570**********1074"; //실제 테스트시에는 *** 마스크처리가 아닌 숫자여야 함
subscribe.cardPw = "**"; //실제 테스트시에는 *** 마스크처리가 아닌 숫자여야 함
subscribe.expireYear = "**"; //실제 테스트시에는 *** 마스크처리가 아닌 숫자여야 함
subscribe.expireMonth = "**"; //실제 테스트시에는 *** 마스크처리가 아닌 숫자여야 함
subscribe.identifyNumber = ""; //주민등록번호 또는 사업자 등록번호 (- 없이 입력)

BootpayApi api = new BootpayApi(Constants.application_id, Constants.private_key);
await api.GetAccessToken();
var res = await api.getBillingKey(subscribe);

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
