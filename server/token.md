# 토큰 발급받기 (필수)

## 1. 서버간 통신이 필요한 이유&#x20;

보안상 필요합니다. 결제/본인인증을 Client SDK로 호출하여 결제를 완료하는 것 까지는 가능하지만, 결제를 검증하고 취소하는 요청은 Server Side에서 이루어져야 합니다. 고객의 결제환경 특성상 다양한 IP에서 결제요청이 진행되지만, 유효한 결제인지 검증하거나 매출에 영향을 주는 결제취소 요청은 개발사의 고정 IP환경에서 수행하시길 추천합니다.



### 서버간 통신을 위한 Access Token 발급

부트페이의 Access Token의 방식은 [OAuth 2.0](https://oauth.net/2)의 규칙을 준수합니다. 결제검증, 결제취소, 빌링키 발급, 빌링키를 통한 결제 요청, 서버에서 결제 승인 요청 등 **REST API를 통해 부트페이 서버와 통신**하기 위해 **`Access Token 필수적`**으로 필요합니다.

## 2. 접근 허용 IP 설정&#x20;

안전한 서버간 통신을 위해 IP 보안 규칙을 설정할 수 있습니다. **테스트 개발 목적이시면 모든 IP의 요청으로 설정**하시고, **상용화 목적이시면 고정 IP**를 지정하셔서 사용하시길 추천합니다.

![](<../.gitbook/assets/스크린샷 2021-07-22 오후 2.06.20.png>)



또한 부트페이 API 서버는 허가된 서버 IP의 요청만 응답합니다. 허가된 서버 IP를 관리하려면 부트페이 관리자에서 [접근 허용 IP 리스트](https://admin.bootpay.co.kr/install/key)를 등록해주셔야 합니다.

## 3. Acess Token 발행시 필요한 연동키 2가지

부트페이 회원가입시 계정별로 Rest용 Application ID와 Private Key 등이 생성되며, Private Key의 경우 필요시 재발급을 하실 수 있으십니다. 이 2가지 키는은 Access Token 요청시 필수적으로 포함되어야 합다.  해당값은 [부트페이 관리자 결제설치 > 인증키 및 보안](https://admin.bootpay.co.kr/install/key)에서 확인 가능합니다.

![](<../.gitbook/assets/스크린샷 2021-07-22 오후 2.19.55.png>)

## **4. Access Token 발급받기 예제**

{% swagger baseUrl="https://api.bootpay.co.kr" path="/request/token" method="post" summary="" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="body" name="application_id" type="string" %}
부트페이 관리자에서 확인 가능한 Rest Application ID
{% endswagger-parameter %}

{% swagger-parameter in="body" name="private_key" type="string" %}
 부트페이 관리자에서 확인 가능한 값 
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```javascript
{
  "status": 200,
  "code": 0,
  "message": "",
  "data": {
    "token": "d6941c650061e3eaddd3f4718ab63e0983c1f6a0a0a01370c1b1ffa90ddd0b51",
    "server_time": 1527569986,
    "expired_at": 1527571765
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Data | 타입       | 설                                                                                                   |
| ------------- | -------- | --------------------------------------------------------------------------------------------------- |
| token         | string   | <p>발급된 Access Token 값 <br>HTTP Header에 <code>Authorization</code> 에 넣어서 사용</p>                      |
| server\_time  | interger | <p>부트페이 서버의 Unixtime입니다. <br><strong>( 부트페이 서버는 ubuntu.pool.ntp.org 시간 기준으로 동기화 됩니다. )</strong></p> |
| expired\_at   | integer  | <p>Access Token이 만료되는 Unixtime입니다. <br><strong>( 첫 발급된 후 30분 뒤 만료됩니다. )</strong></p>                |

## 언어별 예제

{% tabs %}
{% tab title="CURL" %}
```perl
curl -H "Content-Type: application/json" \
-d '{"application_id": "[ REST용 Application ID ]", "private_key": "[ Project의 Private Key ( 보안 문제상 예제에 표기하지 않습니다. 부트페이 관리자에서 확인해주세요. ) ]"}' \
-X POST https://api.bootpay.co.kr/request/token

```
{% endtab %}

{% tab title="PHP" %}
## 설치하기&#x20;

[Composer](http://getcomposer.org)을 통해 설치 ([Github](https://github.com/bootpay/backend-php) 주소)

```javascript
composer require bootpay/backend-php
```

## 사용 예제

```php
<?php
use Bootpay\Rest\BootpayApi;

$bootpay = BootpayApi::setConfig(
    '5b8f6a4d396fa665fdc2b5ea', // Project의 REST용 Application ID
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=' // Project의 Private Key ( 보안 문제상 예제에 표기하지 않습니다. 부트페이 관리자에서 확인해주세요.
);

$response = $bootpay->requestAccessToken();
if ($response->status === 200) {
    var_dump($response);
//    print $response->data->token;
//    print $response->data->server_time;
//    print $response->data->expired_at;
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
if api.request_access_token.success?
  response = api.verify(receipt_id)
  print  response.data.to_json
end
```
{% endtab %}

{% tab title="Node.js" %}
## 설치하기&#x20;

1-1. npm을 통해 설치

```c
npm install @bootpay/server-rest-client
```

1-2. [github](https://github.com/bootpay/server\_nodejs)으로 다운&#x20;

먼저 패키지를 모두 설치합니다

```
yarn install
```

이후 빌드하여 dist로 js로 컴파일 합니다.

```
npm run build
```

## 사용예제&#x20;

1-1. npm을 통해 설치하였을 경우&#x20;

```javascript
import { RestClient } from "@bootpay/server-rest-client"
// or
const RestClient = require('@bootpay/ ').RestClient;

RestClient.setConfig(
    '5b8f6a4d396fa665fdc2b5ea',
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=',
);

RestClient.getAccessToken().then(function (response) {
	// Access Token을 발급 받았을 때
	if (response.status === 200 && response.data.token !== undefined) {
        RestClient.verify('612df0250d681b001de61de6').then(function (_response) {
			// 검증 결과를 제대로 가져왔을 때
			if (_response.status === 200) {
				console.log(_response);
				// 원래 주문했던 금액이 일치하는가?
				// 그리고 결제 상태가 완료 상태인가?
				if (_response.data.price === price && _response.data.status === 1) {
					// TODO: 이곳이 상품 지급 혹은 결제 완료 처리를 하는 로직으로 사용하면 됩니다.
				}
			}
		});
	}
});
```

1-2. github을 통해 다운받은 경우&#x20;

```javascript
import { RestClient } from './dist/bootpay'
// or
const RestClient = require('./dist/bootpay').RestClient

RestClient.setConfig(
    '5b8f6a4d396fa665fdc2b5ea',
    'rm6EYECr6aroQVG2ntW0A6LpWnkTgP4uQ3H18sDDUYw=',
);

RestClient.getAccessToken().then(function (response) {
	// Access Token을 발급 받았을 때
	if (response.status === 200 && response.data.token !== undefined) {
        RestClient.verify('612df0250d681b001de61de6').then(function (_response) {
			// 검증 결과를 제대로 가져왔을 때
			if (_response.status === 200) {
				console.log(_response);
				// 원래 주문했던 금액이 일치하는가?
				// 그리고 결제 상태가 완료 상태인가?
				if (_response.data.price === price && _response.data.status === 1) {
					// TODO: 이곳이 상품 지급 혹은 결제 완료 처리를 하는 로직으로 사용하면 됩니다.
				}
			}
		});
	}
});
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
    GetToken(api)
}

// 1. 토큰 발급
func GetToken(api *bootpay.Api) {
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

## **발급된 Access Token을 이용하여 통신하기**&#x20;

Access Token 을 받은 후 부트페이 REST API 통신을 할때 `HTTP Header`에 **`Authorization`: d6941c650061e3eaddd3f4718ab63e0983c1f6a0a0a01370c1b1ffa90ddd0b51**(data의 token 값) 을 포함하여 보내주시면 됩니다. 부트페이가 제공하는 SDK를 사용할 경우 get Access Token 함수 호출 후 별도로 해주실 작업은 없으십니다.

## **참조. Access Token 발급 Flow**&#x20;

![AccessToken 발행시 전체 Flow](https://docs.bootpay.co.kr/assets/rest/token/1-cac027f6eef67aac13b5cee2e8db4e24b48e33ddeb0d5acfa3780ca559996308.svg)

![Access Token 유효시간과 재발급 ](https://docs.bootpay.co.kr/assets/rest/token/2-cf0469bc9884d3ce4671167c185bfa96d12511b03979c5b54ad7a13852273dd2.svg)

Access Token을 발급 받고 나서 **30분**동안 유효하며, 30분이 지나면 다시 재발급을 받아야 합니다. 발급 받은 이후 **0\~25분**사이에 다시 요청한 경우 같은 Token키를 발급하지만 **25분 이후**에 다시 요청한 경우에 Refresh된 새로운 Token키를 발행합니다. 다시 발행되더라도 기존의 Token키는 남은 5분동안을 계속 사용할 수 있습니다.&#x20;

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
