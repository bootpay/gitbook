# 일반결제

일반결제는 PG사에서 제공하는 결제창으로 **카드결제, 휴대폰결제, 계좌이체, 가상계좌**가 주요 결제수단으로 제공됩니다. **네이버페이, 카카오페이, 페이코**의 간편결제 또한 이 페이지에서 함께 다루도록 하겠습니다.

> 쇼핑몰에서 사용자가 구매하길 원하는 시점마다 PG사의 결제창이 뜨는것이 **일반결제**라 한다면, 카카오택시처럼 결제수단을 미리 등록해놓고 택시에서 내릴때 자동 결제되는 것을 **정기결제**라 합니다. 결제시점에 PG사의 결제창이 뜨는게 아니라, 미리 등록된 결제수단으로 결제되는 방법을 제공하길 원한다면 [**정기결제**](../subscription/)를 참조해주세요.

일반결제는 Client Side인 Javascript로 결제창을 띄우고, 결제후 이뤄지는 과정은 Server Side에서 작업하게 됩니다. 부트페이는 Javascript 뿐만아니라, Android, iOS, Flutter, React Native 등의 SDK를 제공하니 개발환경에 맞는 SDK로 결제를 연동하신 후, 결제 후 과정은 [결과 통지받기](../../webhook/server.md) 및 [서버 연동하기](../../server/token.md)를 참조하세요.

{% hint style="info" %}
결제창을 띄우는 작업은 Client에서 이루어지며, 결제 후 서버로 통지를 받고, 결제를 검증하고, 결제를 취소하는 작업은 Server에서 이루어집니다. 전체 결제 흐름이 궁금하다면 [이곳](../../flow.md)을 참조하세요.
{% endhint %}

부트페이는 여러 PG사와 결제수단을 지원하며, 연동시 pg와 method 라는 파라미터에 값만 변경하여 연동하시면 해당 PG의 결제수단이 뜨게됩니다. 참고할 코드값은 아래와 같습니다.

### PG사 별 코드값

| PG사           | 코드값 (pg)   |
| ------------- | ---------- |
| NHN KCP       | kcp        |
| 다날            | danal      |
| KG 이니시스       | inicis     |
| KG 모빌리언스      | mobilians  |
| 나이스페이         | nicepay    |
| 토스페이먼츠        | toss       |
| 페이앱           | payapp     |
| 유디페이          | udpay      |
| 이지페이 (한국정보통신) | kicc       |
| tPay          | tpay       |
| 페이레터          | payletter  |
| 웰컴페이먼츠        | welcome    |
| 세틀뱅크          | settlebank |
| 네이버페이         | npay       |
| 카카오페이         | kakao      |
| 페이코           | payco      |

### 결제수단 별 코드값

| 결제수단        | 코드값 (method)                |
| ----------- | --------------------------- |
| 카드결제        | card                        |
| 카드결제 (정기결제) | card\_rebill                |
| 휴대폰         | phone                       |
| 계좌이체        | bank                        |
| 가상계좌        | vbank                       |
| 네이버페이       | npay                        |
| 카카오페이       | kakao                       |
| 페이코         | payco                       |
| 간편결제        | easy (pg사가 간편결제사 일 경우에만 유효) |



## 예시 1) KCP - 카드결제

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	pg: 'kcp',
	method: 'card', //결제수단, 입력하지 않으면 결제수단 선택부터 화면이 시작합니다.
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setPg("kcp")
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

## 예시 2) 다날 - 휴대폰

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	pg: 'danal',
	method: 'phone', //결제수단, 입력하지 않으면 결제수단 선택부터 화면이 시작합니다.
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setPg("danal")
       .setMethod("phone") ;

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
let payload = BootpayPayload()
payload.pg = "danal"
payload.method = "phone"

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
Payload payload = Payload();
payload.pg = 'danal';
payload.method = 'phone';

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
    pg: 'danal', 
    method: 'phone',
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 예시 3) 나이스페이 - 네이버페이

네이버페이, 카카오페이, 페이코와 같은 간편결제는 각각이 PG사 이기도 하지만, `결제수단으로 취급` 될 수 있습니다.

예를들어 네이버페이가 KCP나 나이스페이와 같은 PG사와 결제수단으로서의 공급 협약을 맺어, 개발사가 KCP나 나이스페이에 가맹을 하더라도 네이버페이를 사용할 수 있는 형태가 됩니다. 이때의 사용 코드는 아래와 같습니다.&#x20;

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	pg: 'nicepay',
	method: 'npay', //결제수단, 입력하지 않으면 결제수단 선택부터 화면이 시작합니다.
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setPg("nicepay")
       .setMethod("npay") ;

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
let payload = BootpayPayload()
payload.pg = "nicepay"
payload.method = "npay"

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
Payload payload = Payload();
payload.pg = 'nicepay';
payload.method = 'npay';

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
    pg: 'nicepay', 
    method: 'npay',
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 예시 4) 네이버페이 - 결제형&#x20;

네이버페이, 카카오페이, 페이코와 같은 간편결제는 `각각이 PG사로 취급`될 수 있습니다. 이때 결제수단인 `method` 값은 간편결제를 뜻하는 `easy` 가 됩니다.

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	pg: 'npay',
	method: 'easy', 
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setPg("npay")
       .setMethod("easy") ;

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
let payload = BootpayPayload()
payload.pg = "npay"
payload.method = "easy"

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
Payload payload = Payload();
payload.pg = 'npay';
payload.method = 'easy';

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
    pg: 'npay', 
    method: 'easy',
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 예시 5) 카카오페이

{% tabs %}
{% tab title="Javascript" %}
```javascript
//실제 복사하여 사용시에는 모든 주석을 지운 후 사용하세요
BootPay.request({
	...
	pg: 'kakao',
	method: 'easy', //결제수단, 입력하지 않으면 결제수단 선택부터 화면이 시작합니다.
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
Payload payload = new Payload();
payload.setPg("kakao")
       .setMethod("easy") ;

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
let payload = BootpayPayload()
payload.pg = "kakao"
payload.method = "easy"

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
Payload payload = Payload();
payload.pg = 'kakao';
payload.method = 'easy';

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
    pg: 'kakao', 
    method: 'easy',
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
