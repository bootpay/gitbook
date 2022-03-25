# 팝업 결제 진행

부트페이는 기술적으로 iFrame으로 결제창을 올려서 결제를 진행하는 방식입니다. 페이지 reload 없이 SDK 내 이벤트 함수만으로 결제가 진행되도록 만들어졌기 때문에 iFrame은 필수적인 요소라고 할 수 있습니다.

문제는 iOS Mobile Safari에서는 iFrame에 대한 일부 버그가 있습니다. 그래서 결제가 매끄럽게 진행이 되지 않는 문제가 있습니다. 기술적으로 어떤 문제가 있는지 나열하면 다음과 같습니다.

1. iOS Safari ( Webview 포함 ) iFrame 풀스크린 상태에서 스크롤이 발생되었다가 스크롤이 사라지면, 터치 영역이 스크롤 사라지기 전으로 남아있는 버그
2. iOS에서 일부 외부 (앱카드, 뱅크페이등) 앱 호출이 안되는 문제

위 2가지 경우 보통 브라우저에서는 **사용자의 개입이 고려되지 않은 팝업창**이라고 판단해서 "팝업이 차단" 된 경우 팝업이 뜨지 않거나, 팝업차단을 해제하라는 안내가 뜨게 됩니다.

## **방법1-1. 사용자가 결제 팝업으로 진행하겠냐는 버튼을 누름으로서 결제 진행 ( JS SDK 기준 3.2.0 이상부터 가능 )**

```javascript
BootPay.request({
    pg: '[[ 선택할 PG ]]',
    method: '[[ 선택할 결제 수단 ]]',
    name: '상품명',
    // .... 결제 요청 내용 .... //
    extra: {
        popup: true, // 모든 결제를 팝업으로 진행 //
        // 혹은 //
        popup: BootPay.isMobile(), // 모바일 결제인 경우에만 팝업 진행 //
        popup: BootPay.isMobileSafari() // 모바일 iOS 사파리, 크롬인 경우에만 팝업 진행 //
    }
})
```

예제와 같이 extra 값에 popup: true라는 값을 주고 요청을 하게 되면 모든 결제에 대해 팝업으로 진행합니다.

모든 결제에 대해서 팝업으로 진행할 필요는 없고 모바일 환경에 따라서 조건을 줄 수도 있습니다.

1. { extra: BootPay.isMobile() } - 모바일 결제일 경우 팝업으로 진행
2. { extra: BootPay.isMobileSafari() } - 모바일 사파리 결제일 경우 팝업으로 진행

다음과 같은 방법으로 결제를 진행하게 되면 한가지 문제점이 발생됩니다. 사용자의 개입이 포함된 버튼을 눌러야 한다는 점입니다.

![](https://docs.bootpay.co.kr/assets/deep/popup/ex1-5166e2f33a4bb080ef81d083b519a60b1c452f45f3fb01594a6709008b5379c3.png)

위 사진과 같이 "결제하기" 버튼을 눌러 브라우저 정책 중 하나인 사용자 개입이 반드시 들어가야 합니다.&#x20;

{% hint style="info" %}
사용자 개입을 유도하는 팝업창을 생략하길 원하는 경우&#x20;

`extra.popup = true;` 와 함께 `extra.quick_popup = true;` 을 적용시켜 주시면 되겠습니다. 이는 부트페이에서 제시한 해결 방법이 될 수 있지만, 정석적인 방법이 아니므로 적용 후 정상 작동하는지 확인하셔야 합니다.
{% endhint %}

## **방법 1-2. 결제창을 부를때 조건에 따라 팝업을 먼저 띄운 후 Promise 비동기로 결제 데이터 전달 ( JS SDK 기준 3.3.0 이상부터 가능 )**

```javascript
BootPay.popupAsyncRequest(BootPay.isMobile(), async function () {
    try {
        let response = await $http.post({
            url: 'https://test.com/test'
        })
    } catch (e) {
        Promise.reject(e)
    }
    Promise.resolve({
        pg: '[[ 요청할 PG ]]',
        method: '[[ 요청할 method ]]',
        application_id: "[ WEB SDK용 Application ID ]",
        price: '[[ 결제할 금액 ]]',
        order_id: response.data.order_id
    })
})
```

예제코드와 같이 BootPay.popupAsyncRequest( \[ 팝업 조건 ], \[ 비동기 함수 호출 ] )형태로 요청을 해서 Promise로 결제를 요청할 Object 데이터를 전달하는 방법입니다.

예제의 방법대로 하면 **방법1**과 같이 "사용자가 팝업으로 진행한다는 버튼" 없이 바로 팝업 결제 진행이 가능합니다.

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
