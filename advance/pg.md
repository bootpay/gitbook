# PG 오류 데이터 요청하기

부트페이 SDK에서 extra 필드에서 추가적으로 데이터를 보내면 PG에서 오류가 났을 경우 PG 오류 데이터를 추가로 수집하실 수 있습니다. 필요시 extra에  raw\_data를 true 값으로 보내시면 되겠습니다. &#x20;

## 클라이언트 사이드에서 요청하기&#x20;

[일반결제](../client/pg/), [통합결제](../client/total.md) 사용시 클라이언트 사이드에서 요청할 수 있습니다.&#x20;

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	pg: ...,
	method: ..., //결제수단, 입력하지 않으면 결제수단 선택부터 화면이 시작합니다.
	extra: {
		raw_data: true 
	}	
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setPg(...)
       .setMethod("card") ;

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
let payload = BootpayPayload()
payload.pg = "kcp"
payload.method = "card"

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
Payload payload = Payload();
payload.pg = 'kcp';
payload.method = 'card';

Bootpay().request(
    ..
    payload: payload
    ..
);
```
{% endtab %}

{% tab title="React Native" %}
```jsx

const bootpay = useRef<BootpayWebView>(null);

const payload = {
    pg: 'kcp', 
    method: 'card',
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## **서버사이드에서 요청하기**&#x20;

[정기결제](../client/subscription/) 사용시 서버사이드에서 요청할 수 있습니다.&#x20;

{% tabs %}
{% tab title="PHP" %}
```php
<?php
require_once('../autoload.php');
spl_autoload_register('BootpayAutoload');

use Bootpay\Rest\BootpayApi;

$billingKey = '[[ biling_key ]]';

$bootpay = BootpayApi::setConfig(
    '[[ application_id ]]',
    '[[ private_key ]]'
);

$response = $bootpay->requestAccessToken();

if ($response->status === 200) {
    $result = $bootpay->subscribeCardBilling([
        'billing_key' => $billingKey,
        'item_name' => '정기결제 테스트 아이템',
        'price' => 3000, // 금액 입력
        'order_id' => '[[ order_id ]]',
        'quota' => 0, // 할부 개월수 0 - 일시불, 3 - 3개월 ( 5만원 이상부터 가능 )
        'items' => [
            [
                'item_name' => '정기 결제 아이템명',
                'qty' => 1,
                'unique' => '아이템 유니크 값',
                'price' => 3000,
                'cat1' => '카테고리 1',
                'cat2' => '카테고리 2',
                'cat3' => '카테고리 3'
            ]
        ],
        'extra' => [
            'raw_data' => 1 // PG 오류 발생시 Return
        ]
    ]);
    if ($result->status === 200) {
        // TODO: 결제 완료된 로직을 처리하시면 됩니다.
    }
}
```
{% endtab %}

{% tab title="Ruby" %}
```ruby
require 'bootpay-rest-client'

bootpay = Bootpay::ServerApi.new(
  '[[ application_id ]]',
  '[[ private_key ]]'
)

result = bootpay.get_access_token
if result[:status] == 200
  response = bootpay.subscribe_billing(
    billing_key: '[[ billing_key ]]',
    item_name: '정기결제 테스트 아이템',
    price: 3000,
    order_id: '[[ order_id ]]',
    quota: 0, # 0 - 일시불, 3 - 3개월 ( 5만원 이상부터 가능 )
    items: [
      {
        item_name: '정기 결제 아이템명',
        qty: 1,
        unique: '아이템 유니크값',
        price: 3000,
        cat1: '카테고리 1',
        cat2: '카테고리 2',
        cat3: '카테고리 3'
      }
    ],
    extra: {
      raw_data: 1 # PG 오류 발생시 Return
    }
  )
  if response[:status] == 200
    # TODO: 결제 완료된 로직을 수행하시면 됩니다.
  end
end
```
{% endtab %}

{% tab title="Node.js" %}
```javascript
import { RestClient } from "@bootpay/server-rest-client"
// or
const RestClient = require('@bootpay/server-rest-client').RestClient;

RestClient.setConfig(
    '[[ application_id ]]',
    '[[ private_key ]]'
);


RestClient.getAccessToken().then(function (token) {
    if (token.status === 200) {
        RestClient.requestSubscribeBillingPayment({
            billingKey: '[[ billing_key ]]', // 빌링키
            itemName: '정기결제 아이템', // 정기결제 아이템명
            price: 3000, // 결제 금액
            taxFree: 0, // 비과세 금액 ( 0원이면 생략 가능 )
            orderId: '[[ order_id ]]', // 유니크한 주문번호
            quota: 0, // 할부 개월수 0 - 일시불, 3 - 3개월 ( 5만원 이상부터 가능 )
            userInfo: {
                username: 'test',
                email: 'test@bootpay.co.kr',
                phone: '01000000000',
                address: '[[ 지역 ]]'
            },
            feedbackUrl: '[[ 결제 완료후 Feedback 받을 https가 포함된 URL ]]',
            feedbackContentType: '[[ json or urlencoded ]]',
            items: [
                {
                    itemName: '정기 결제 아이템명',
                    qty: 1,
                    unique: '아이템 유니크값',
                    price: 3000,
                    cat1: '카테고리1',
                    cat2: '카테고리2',
                    cat3: '카테고리3'
                }
            ],
            extra: {
                rawData: 1 // PG 오류 발생시 Return
            }
        }).then(function (response) {
            if (response.status === 200) {
                // TODO: 결제 완료된 로직을 처리하시면 됩니다.
            }
        });
    }
});
```
{% endtab %}

{% tab title="Python" %}
```python
from lib.BootpayApi import BootpayApi

bootpay = BootpayApi(
    '[[ application_id ]]',
    '[[ private_key ]]'
)
token = bootpay.get_access_token()
if token['status'] is 200:
    response = bootpay.subscribe_billing(
        '[[ token_key ]]',
        '정기 결제 테스트 아이템',
        3000,
        '[[ order_id ]]',
        [
            {
                'item_name': '정기 결제 아이템명',
                'qty': 1,
                'unique': '아이템 유니크값',
                'price': 3000,
                'cat1': '카테고리 1',
                'cat2': '카테고리 2',
                'cat3': '카테고리 3'
            }
        ],
        [
            'raw_data': 1
        ]
    )
    if response['status'] is 200:
        # TODO: 결제 완료된 로직을 처리하시면 됩니다.
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
	bc := bootpay.Client{}.New("5b8f6a4d396fa665fdc2b5ea", "rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=", nil, "")

	fmt.Println("--------------- GetVerify() Start ---------------")
	token, err := bc.GetToken()
	fmt.Println("token : " + token.Data.Token)

	receiptId := "610c96352386840036db8bef"
  verify, err := bc.Verify(receiptId)
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

## **Response Data 예제 ( ex. 나이스페이에서 오류가 났을 경우 )**

```javascript
{
  "status":500,
  "code":-15006,
  "message":"금액오류(100원 또는 1달러 미만 신용카드 승인 불가)",
  "data":{
    "ResultCode":"3041",
    "ResultMsg":"금액오류(100원 또는 1달러 미만 신용카드 승인 불가)"
  }
}
```

위 예제와 같이 나이스페이에서 오류가 났을 경우 **HTTP Status 500 오류**가 발생되고 response body안 data에 PG에서 리턴한 오류를 확인할 수 있습니다.

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
