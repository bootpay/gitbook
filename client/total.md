# 통합결제

통합결제창은 부트페이 관리자에서 활성화된 결제수단이 모두 노출되는 결제창으로, 부트페이에서 제공하는 결제화면입니다. 통합결제 연동시 **소스코드 수정없이 PG사를 변경할 수 있다는 장점**이 있습니다.&#x20;

![](<../.gitbook/assets/스크린샷 2021-07-19 오후 4.59.58 (1).png>)



## Bootpay 설치하기&#x20;

먼저 부트페이를 설치하기 위해서는 [일반결제](pg/)의 개발중인 개발환경의 bootpay 설치하기를 참조해주세요.

## 통합결제 구현하기&#x20;

통합결제는 [일반결제](pg/)와 기본적으로 동일합니다. 일반결제 요청시 `pg, method`를 지정하지 않거나 `methods`에 배열로 원하는 `method` 값들을 넣으시면 되겠습니다.

{% tabs %}
{% tab title="Javascript" %}
```javascript
//통합결제는 부트페이 관리자에서 활성화된 결제수단만 UI에 노출됩니다 
BootPay.request({
	...
	//pg: '', //지정하지 않으면 통합결제
	//method: '', //지정하지 않으면 통합결제
	methods: ['card', 'phone', 'vbank', 'bank'] //또는 사용할 결제수단 지정 
	...
})
```
{% endtab %}

{% tab title="Android" %}
```java
//통합결제는 부트페이 관리자에서 활성화된 결제수단만 UI에 노출됩니다 
Payload payload = new Payload();

//지정하지 않으면 통합결제
//payload.setPg("")
//       .setMethod("") ;

payload.setMethods(Arrays.asList("card","phone","vbank","bank")); //또는 사용할 결제수단 지정 

Bootpay.setPayload(payload)
       .requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```swift
//통합결제는 부트페이 관리자에서 활성화된 결제수단만 UI에 노출됩니다 
let payload = BootpayPayload()
//payload.pg = "" //지정하지 않으면 통합결제
//payload.method = "" //지정하지 않으면 통합결제
payload.methods = ["card", "phone", "bank", "vbank"] //또는 사용할 결제수단 지정 

Bootpay.request(self, sendable: self, payload: payload)
```
{% endtab %}

{% tab title="Flutter" %}
```dart
//통합결제는 부트페이 관리자에서 활성화된 결제수단만 UI에 노출됩니다 
Payload payload = Payload();
//payload.pg = ''; //지정하지 않으면 통합결제
//payload.method = ''; //지정하지 않으면 통합결제
payload.methods = ["card", "phone", "bank", "vbank"] //또는 사용할 결제수단 지정 

Bootpay().request(
    ..
    payload: payload
    ..
);
```
{% endtab %}

{% tab title="React Native" %}
```jsx
//통합결제는 부트페이 관리자에서 활성화된 결제수단만 UI에 노출됩니다 
const bootpay = useRef<BootpayWebView>(null);

const payload = {
    //pg: '', //지정하지 않으면 통합결제
    //method: '', //지정하지 않으면 통합결제
    methods: ['card', 'phone', 'bank', 'vbank'] //또는 사용할 결제수단 지정 
    ...
}

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
