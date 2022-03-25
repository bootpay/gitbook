# 결제 링크 생성하기

#### 결제 링크 생성하는 시점

**먼저, 해당 페이지는 결제 연동을 위한 필수 사항은 아닙니다.** 더 유연한 결제 시나리오를 수용하기 위한 추가 페이지입니다. REST API 형태로 결제 링크를 생성하고, 원하는 시점에 해당 링크로 이동시켜 결제를 진행하면 됩니다.

## **결제 링크 생성하기 REST API**

{% swagger baseUrl=" https://api.bootpay.co.kr" path="/request/payment.json" method="post" summary="" %}
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

```
(post) https://api.bootpay.co.kr/request/payment.json
```

post body의 데이터포맷인 [payload 파라미터](broken-reference/)와 [extra 파라미터](broken-reference/)는 [일반결제](../client/pg/)와 동일합니다. 아래 예제를 참고해주세요.

## 언어별 예제

{% tabs %}
{% tab title="PHP" %}
```php
require_once('../autoload.php');
spl_autoload_register('BootpayAutoload');

use BootpayRestBootpayApi;

$bootpay = BootpayApi::setConfig(
    '[ REST API Application ID ]',
    '[ Private Key는 보안 문제상 표기하지 않습니다. ]',
);

$response = $bootpay->requestAccessToken();

if ($response->status === 200) {
    $result = $bootpay->requestPayment([
        'pg' => 'danal', // PG Alias
        'method' => 'card', // 결제 수단 Alias
        'order_id' => time(), // 사용할 ORDER ID
        'price' => 1000, // 결제금액
        'name' => '테스트 부트페이 상품', // 상품명
        'return_url' => 'https://dev-api.bootpay.co.kr/callback' // 결제정보 및 취소, 결제 에러등의 정보를 받을 return_url
    ]);
    if ($response->status === 200) {
        print($response->data); // 결제 링크 리턴값
    }
}
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

    $params = [
        'callback1' => '그대로 콜백받을 변수 1',
        'callback2' => '그대로 콜백받을 변수 2',
        'callback3' => '그대로 콜백받을 변수 3'
    ];

    $optional = [
        'pg' => 'nicepay',
        // 'method' => 'card',
        'methods' => ['card', 'phone', 'bank', 'vbank'], //통합결제창 이용시
        'params' => json_encode($params),
        'extra' => [
            'raw_data' => 1
        ]
    ];

    $result = $bootpay->requestPayment(
        time(),
        '테스트 부트페이 상품',
        1000,
        $optional
    );  
    var_dump($result);
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
    response = @api.request_link(
      pg:             'nicepay',
      price:          1000,
      tax_free:       1000,
      order_id:       '1234',
      name:           '결제테스트'
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
async function requestPayment() {
    const Bootpay = require('bootpay-backend-nodejs').Bootpay
    Bootpay.setConfig(
        '5b8f6a4d396fa665fdc2b5ea',
        'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw='
    )
    let token = await Bootpay.getAccessToken()
    if (token.status === 200) {
        let response
        try {            
            response = await Bootpay.requestPayment({
                pg: 'kcp',
                method: 'card',
                orderId: (new Date).getTime(),
                price: 1000,
                itemName: '테스트 부트페이 상품',
                returnUrl: 'https://www.yourdomain.com/callback',
                extra: {
                    expire: 30
                }
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

result = bootpay.request_payment({
        'pg': 'kcp',
        'method': 'card',
        'order_id': '1234-1234',
        'price': 1000,
        'name': '테스트 부트페이 상품',
        'return_url': 'https://www.yourdomain.com/callback',
        'extra': {
            'expire': 30
        }
    })
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
        
public static void requestLink() {
    Payload payload = new Payload();
    payload.orderId = "1234";
    payload.price = 1000;
    payload.name = "테스트 결제";
    payload.pg = "nicepay";

    try {
        ResDefault res = bootpay.requestLink(payload);
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
    RequestLink(api) 
}

func RequestLink(api *bootpay.Api) {
    payload := bootpay.Payload{
        Pg: "kcp",
        Method: "card",
        Price: 1000,
        OrderId: fmt.Sprintf("%+8d", (time.Now().UnixNano() / int64(time.Millisecond))),
        Name: "테스트 결제 상품",
    }
    fmt.Println("--------------- RequestLink() End ---------------")
    res, err := api.RequestLink(payload)

    fmt.Println(res)
    if err != nil {
        fmt.Println("get token error: " + err.Error())
    }
    fmt.Println("--------------- RequestLink() End ---------------")
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

Payload payload = new Payload();
payload.orderId = "" + DateTimeOffset.UtcNow.ToUnixTimeMilliseconds();
payload.price = 1000;
payload.name = "테스트 결제";
payload.pg = "nicepay";

BootpayApi api = new BootpayApi(Constants.application_id, Constants.private_key);
await api.GetAccessToken();
var res = await api.requestPayment(payload);

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
