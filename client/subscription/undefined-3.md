# 4-2. 예약된 결제 취소하기

[빌링키로 예약된 결제 건](undefined-2.md)에 대해 아직 결제가 되지 않았다면 예약을 취소할 수 있는 API 입니다.

## **예약된 결제 취소하기 REST API**

{% swagger baseUrl="https://api.bootpay.co.kr" path="/subscribe/billing/reserve/:reserve_id" method="delete" summary="" %}
{% swagger-description %}
요청 주소는 위와 같으며 payload 파라미터는 아래와 같습니다.
{% endswagger-description %}

{% swagger-parameter in="query" name="reserve_id" type="string" required="false" %}
결제 예약시 발급받은 reserve_id
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```javascript
{
  "status": 200,
  "code": 0,
  "message": ""
}
```
{% endswagger-response %}
{% endswagger %}

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
#### 설치하기

[Composer](http://getcomposer.org)을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
composer require bootpay/backend-php
```

#### 사용 예제

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
    $response = $bootpay->subscribeBillingReserveCancel(
        '613af2600199430027b5cb83'
    ); 
    
    var_dump($response);
}
```
{% endtab %}

{% tab title="Ruby" %}
#### 설치하기

[Gemfile](https://rubygems.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
gem 'backend-ruby'
```

위 라인 추가 후 `bundle install` 실행

#### 사용 예제

```php
# 결제 검증하기 
receipt_id = '612df0250d681b001de61de6'

api = Bootpay::Api.new(
  application_id: '5b8f6a4d396fa665fdc2b5ea',
  private_key:    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=',
)

api.request_access_token

if api.request_access_token.success?
  reserve_id = '612deb53019943001fb52312'
  response = api.subscribe_reserve_cancel(reserve_id)
  puts response.data.to_json
end
```
{% endtab %}

{% tab title="Node.js" %}
#### NPM 통해 설치하기

```c
npm install bootpay-backend-nodejs
```

#### 사용예제

```javascript
async function subscribeBillingReserveCancel() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    let token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let response
        try {            
            response = await Bootpay.destroyReserveSubscribeBilling('612debc70d681b0039e6133d')
            console.log(response)
        } catch (e) {
            return console.log(e)
        }
        console.log(response)
    }
}
```
{% endtab %}

{% tab title="Python" %}
#### 설치하기

[Pypl](https://pypi.org) 을 통해 설치 ([Github](https://github.com/bootpay/backend-python) 코드 보기)

```javascript
pip install bootpay 
```

#### 사용 예제

```python
from bootpay import Bootpay

bootpay = Bootpay('5b8f6a4d396fa665fdc2b5ea', 'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=')
bootpay.get_access_token() # 토큰 얻어오기

reserve_id = '612debc70d681b0039e6133d'
result = bootpay.subscribe_billing_reserve_cancel(
    reserve_id
)
print(result)
```
{% endtab %}

{% tab title="Java" %}
#### 설치하기

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

#### 사용 예제

```java
import kr.co.bootpay.Bootpay;
import kr.co.bootpay.model.response.ResDefault;


bootpay = new Bootpay("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");
        
public static void reserveCancelSubscribe() {
    String receiptId = "6100e892019943002150fef3";
    try {
        ResDefault res = bootpay.reserveCancelSubscribe(receiptId);
        System.out.println(res.toJson());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```
{% endtab %}

{% tab title="Go" %}
#### 설치하기 ([Github](https://github.com/bootpay/backend-go) 주소)

```javascript
go get github.com/bootpay/backend-go
```

#### 사용 예제

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
**1. Visual Studio에서 추가하기**

1\. 솔루션 탐색기(Solution Explorer) 열기\
2\. 만드신 솔루션 프로젝트 우클릭\
3\. Manage Nuget Packages 클릭\
4-1. '[Bootpay.framework](https://www.nuget.org/packages/Bootpay.framework)' (.net standard 2.0 이상)\
4-2. 또는 '[Bootpay.net](https://www.nuget.org/packages/Bootpay.net)' (.net core 3.1 이상)

#### 2. 사용 예제

```javascript
BootpayApi api = new BootpayApi("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=");
await api.GetAccessToken();

string reserveId = "615d08a67b5ba4002011cd41";
             
var res = await api.reserveCancelSubscribe(reserveId);

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
