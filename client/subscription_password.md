# 정기결제 (비밀번호)

&#x20;\
![](https://docs.bootpay.co.kr/assets/deep/easy/2-0006cfc2d14afd078753986ab28d2ebc21e27feca8c33aba919eb8fee9ee55e5.png)![](https://docs.bootpay.co.kr/assets/deep/easy/3-a35cc4ef3673a18a60ec492b399d6969586de3c75725e33e0875652bb2d11db0.png)

정기결제 (비밀번호)는 PG사의 정기결제(비인증 방식)를 응용하여 부트페이에서 제공하는 **`간편 결제창`** 입니다.

쿠팡의 쿠페이처럼, 고객에게 최초 결제시 결제수단 등록을 유도하고, 재구매시 기존 설정한 비밀번호 방식으로 빠르게 결제하는 것으로, 간편 결제 경험을 제공할 수 있어 재구매율이 올라가는 효과가 있습니다.

현재 `paypp, nicepay, welcome`만 이용이 가능합니다.

## 1. 결제수단 활성화 설정



![](https://docs.bootpay.co.kr/assets/deep/easy/1-74f126b55a3101cdd23b0bde117698715175c339817e7600d5a5860af11640e2.png)

[관리자 페이지](https://admin.bootpay.co.kr/install/method)로 이동 후"카드정기결제 ( REST )"가 가능한 결제 수단을 선택 후 부트페이가 **제공하는 간편 결제창을 활성화**를 해주시면 됩니다.

## 2. 고객 토큰(User Token) 발급받기

이 작업은 **`서버사이드`**에서 **REST API**로 수행합니다.\
부트페이 서버 통신을 위한 [토큰 발급](../server/token.md)받은 후 간편 결제창 사용을 위한 고객 토큰을 발급받습니다.

{% swagger baseUrl="https://api.bootpay.co.kr" path="/request/user/token" method="post" summary="" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="body" name="user_id" type="string" %}
개발사에서 관리하는 고객 고유 아이디
{% endswagger-parameter %}

{% swagger-parameter in="body" name="email" type="string" %}
개발사에서 관리하는 고객 이메일
{% endswagger-parameter %}

{% swagger-parameter in="body" name="name" type="string" %}
고객 이름
{% endswagger-parameter %}

{% swagger-parameter in="body" name="gender" type="integer" %}
`0`

 \- 여성

\




`1`

 \- 남성
{% endswagger-parameter %}

{% swagger-parameter in="body" name="birth" type="string" %}
고객 생년월일
{% endswagger-parameter %}

{% swagger-parameter in="body" name="phone" type="string" %}
고객 전화번호
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```javascript
{
  "status": 200,
  "code": 0,
  "message": "",
  "data": {
    "user_token": "5e81bc176f9c290363a65f87",
    "expired_unixtime": 1585646999,
    "expired_localtime": "2020-03-31 18:29:59 +0900"
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Data      | 타입      | 설                                                                                  |
| ------------------ | ------- | ---------------------------------------------------------------------------------- |
| user\_token        | string  | <p>부트페이가 간편 결제창 사용을 위해 발급한 고객 토큰 값</p><p>해당 토큰은 1시간 뒤 갱신되므로, 1시간 뒤에는 재발급 받아야 함</p> |
| expired\_unixtime  | integer | 만료되는 시각의 unixtime                                                                  |
| expired\_localtime | string  | 만료되는 시각의 localtime 값                                                               |



## 3-1. Bootpay 설치하기&#x20;

먼저 부트페이를 설치하기 위해서는 [이곳](broken-reference)을 참조해주세요.

## 3-2. 고객 토큰 발급받은 후 결제 요청하기

이 작업은 **`클라이언트`**에서 수행합니다. `2. 고객 토큰(User Token) 발급받기`의 결과값은 작업하신 서버에서 알 고 있으므로, 클라이언트에서 그 결과값을 가져오기 위해 내부적으로 서버와 한번 통신하시기를 추천합니다.

[일반결제](pg/)와 연동방법이 유사합니다. 상세한 내용은 [일반결제](pg/)를 참조해주세요.

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	method: 'easy_card',// ( easy_card 간편 카드 결제, easy_bank 간편 계좌이체 - 지원예정 ) 
	user_token: '2번의 과정을 통해 발급받은 고객 토큰 값',
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setMethod("easy_card") // ( easy_card 간편 카드 결제, easy_bank 간편 계좌이체 - 지원예정 )
       .setUserToken("2번의 과정을 통해 발급받은 고객 토큰 값") ;

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
let payload = BootpayPayload() 
payload.method = "easy_card" // ( easy_card 간편 카드 결제, easy_bank 간편 계좌이체 - 지원예정 )
payload.userToken = "2번의 과정을 통해 발급받은 고객 토큰 값"

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
Payload payload = Payload(); 
payload.method = 'easy_card'; // ( easy_card 간편 카드 결제, easy_bank 간편 계좌이체 - 지원예정 )
payload.userToken = "2번의 과정을 통해 발급받은 고객 토큰 값";

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
    method: 'easy_card', // ( easy_card 간편 카드 결제, easy_bank 간편 계좌이체 - 지원예정 )
    userToken: "2번의 과정을 통해 발급받은 고객 토큰 값"
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
