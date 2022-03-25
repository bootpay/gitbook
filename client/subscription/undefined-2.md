# 3. 빌링키로 결제 예약하기

\*\*\*\*[ \*\*\*\* 빌링키로 결제 요청하기 API](undefined-1.md)를 사용할 경우 원하는 시점에 요청해야 하기 때문에 스케쥴러 등을 이용해야 한다는 특징이 있다면, 결제 예약하기 API의 경우 미리 결제시점을 등록할 수 있다는 특징이 있습니다. 결제 시점이 정해진 경 이 API를 사용하셔도 되겠습니다.

## **빌링키로 결제 예약하기 REST API**

{% swagger baseUrl="https://api.bootpay.co.kr" path="/subscribe/billing/reserve " method="post" summary="" %}
{% swagger-description %}
요청 주소는 위와 같으며 payload 파라미터는 아래와 같습니다.
{% endswagger-description %}

{% swagger-parameter in="body" name="billing_key" type="string" required="false" %}
부트페이를 통해 발급받은 빌링키
{% endswagger-parameter %}

{% swagger-parameter in="body" name="item_name" type="string" required="false" %}
결제할 상품명
{% endswagger-parameter %}

{% swagger-parameter in="body" name="price" type="integer" required="false" %}
결제할 금액
{% endswagger-parameter %}

{% swagger-parameter in="body" name="order_id" type="string" required="false" %}
개발사에서 관리하는 고유거래 번호
{% endswagger-parameter %}

{% swagger-parameter in="body" name="scheduler_type" type="string" required="false" %}

{% endswagger-parameter %}

{% swagger-parameter in="body" name="execute_at" type="string" required="false" %}

{% endswagger-parameter %}

{% swagger-parameter in="body" name="feedback_url" type="string" required="false" %}
결제 상태 변경시 (완료/취소) 통지받을 URL
{% endswagger-parameter %}

{% swagger-parameter in="body" name="feedback_content_type" type="string" required="false" %}

{% endswagger-parameter %}

{% swagger-parameter in="body" name="items" type="string" required="false" %}

{% endswagger-parameter %}

{% swagger-parameter in="body" name="user_info" type="object" required="false" %}
payapp은 구매자명, 전화번호를 필수로 입력해야 합니다.

\\

(string)

`phone`

\- 구매자 전화번호

\\

(string)

`username`

\- 구매자명

\\

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
    "reserve_id": "5b339133e13f3307f5764da7",
    "billing_key": "5b025b33e13f33310ce560fb",
    "item_name": "정기결제 테스트 아이템",
    "price": 3000.0,
    "purchase_count": 0,
    "purchase_limit": 1,
    "execute_at": 1530106173,
    "status": 0
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Data   | 타입       | 설명                                                                                                                                                                      |
| --------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| reserve\_id     | string   | 예약 고유번호입니다. 예약 결제를 취소할 때 반드시 필요합니다.                                                                                                                                     |
| billing\_key    | string   | 부트페이를 통해 발급받은 빌링키                                                                                                                                                       |
| item\_name      | string   | 결제할 상품명                                                                                                                                                                 |
| price           | interger | 결제할 금액                                                                                                                                                                  |
| purchase\_count | integer  | period 결제일 때 결제를 몇번했는지에 대한 값                                                                                                                                            |
| purchase\_limit | integer  | period 결제할 때 최대 결제가능 횟수. 이 결제 횟수를 넘어서 결제를 할 수 없                                                                                                                         |
| execute\_at     | integer  | 다음 결제가 언제인지 나타냅니다. unixtime 숫자 값                                                                                                                                        |
| status          | integer  | <p>현재 예약 결제의 상태입니다.</p><ul><li>0 - 예약 결제 대기중</li><li>1 - 결제가 모두 완료됨</li><li>2 - 예약결제 실행도중 에러남</li><li>-1 - 예약이 되지 않음</li><li>3 - 결제예약을 했으나 결제를 하지 않고 사용자가 취소함</li></ul> |

## 언어별 예제

{% tabs %}
{% tab title="CURL" %}
```perl
curl -H "Content-Type: application/json" \
-H "Authorization: d6941c650061e3eaddd3f4718ab63e0983c1f6a0a0a01370c1b1ffa90ddd0b51" \
-d '{"billing_key": "[[ billing_key ]]", "price": 3000, "scheduler_type": "oneshot", "item_name": "정기결제 테스트아이템", "execute_at": "1626740831", "feedback_url": "[[ feedback_url ]]", "order_id": "[[ order_id ]]", "items": [{"item_name": "정기 결제 아이템", "qty": 1, "unique": "아이템 유니크값", "price": 3000, "cat1": "카테고리 1", "cat2": "카테고리 2", "cat3": "카테고리 3"}]}' \
-X POST https://api.bootpay.co.kr/subscribe/billing/reserve.json
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
    $response = $bootpay->subscribeCardBillingReserve(
        '613af2600199430027b5cb83',
        time(),
        '정기결제 테스트 아이템',
        1000,
        time() + 10
    ); 
    
    var_dump($response);
}
```
{% endtab %}

{% tab title="Ruby" %}
### 설치하기

[Gemfile](https://rubygems.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
gem 'backend-ruby'
```

위 라인 추가 후 `bundle install` 실행

### 사용 예제

```php
# 결제 검증하기 
receipt_id = '612df0250d681b001de61de6'

api = Bootpay::Api.new(
  application_id: '5b8f6a4d396fa665fdc2b5ea',
  private_key:    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=',
)

api.request_access_token

if api.request_access_token.success?
  response = @api.subscribe_reserve_billing(
      billing_key:    billing_key,
      item_name:      '테스트 결제',
      price:          1000,
      tax_free:       1000,
      order_id:       '1234',
      execute_at: (Time.now + 10.seconds).to_i # 10초 뒤 결제
    )
  puts response.data.to_json
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
async function subscribeBillingReserve() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    let token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let response
        try {
            response = await Bootpay.reserveSubscribeBilling({
                billingKey: '612deb53019943001fb52312',
                itemName: '테스트',
                price: 1000,
                orderId: (new Date()).getTime(),
                userInfo: {
                    username: '테스트',
                    phone: '01000000000'
                },
                feedbackUrl: 'https://dev-api.bootpay.co.kr/callback',
                feedbackContentType: 'json',
                schedulerType: 'oneshot',
                executeAt: ((new Date()).getTime() / 1000) + 5
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
### 설치하기

[Pypl](https://pypi.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-python) 코드 보기)

```javascript
pip install bootpay 
```

### 사용 예제

```python
from bootpay import Bootpay

bootpay = Bootpay('5b8f6a4d396fa665fdc2b5ea', 'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=')
bootpay.get_access_token() # 토큰 얻어오기

billing_key = '612deb53019943001fb52312'
result = bootpay.subscribe_billing_reserve(
    billing_key,
    '정기 결제 테스트 아이템',
    3000,
    '12345',
    time.time() + 10

)
print(result)
```
{% endtab %}

{% tab title="Java" %}
### 설치하기

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

### 사용 예제

```java
import kr.co.bootpay.Bootpay;
import kr.co.bootpay.model.response.ResDefault;


bootpay = new Bootpay("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");
        
public static void reserveSubscribe() {
    SubscribePayload payload = new SubscribePayload();

    payload.billingKey = "6100e77a0d681b002ad4e5d9";
    payload.itemName = "아이템01";
    payload.price = 1000;
    payload.orderId = "" + (System.currentTimeMillis() / 1000);
    payload.executeAt = (System.currentTimeMillis() / 1000) + 10000; // 결제 승인 시점

    try {
        ResDefault<SubscribeBillingReserveData> res = bootpay.reserveSubscribe(payload);
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

SubscribePayload payload = new SubscribePayload();
payload.billingKey = "615d00f0238684001f60241e";
payload.itemName = "아이템01";
payload.price = 1000;
payload.orderId = "" + DateTimeOffset.UtcNow.ToUnixTimeMilliseconds();
payload.executeAt = (DateTimeOffset.UtcNow.ToUnixTimeMilliseconds() / 1000) + 10;

BootpayApi api = new BootpayApi(Constants.application_id, Constants.private_key);
await api.GetAccessToken();
var res = await api.reserveSubscribe(payload);

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

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
