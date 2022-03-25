# Flutter (Web)

Flutter 앱을 만들때 이 페이지를 참조하시면 됩니다.&#x20;

PG 결제창은 기본적으로 Javascript로 연동됩니다. 부트페이 Flutter SDK는 내부적으로 Webview 방식으로 구현하였으며, 사용방법은 아래와 같습니다.&#x20;

## Bootpay 설치하기&#x20;

{% tabs %}
{% tab title="pubspec.yaml" %}
```java
...
dependencies:
 ...
 bootpay: last_version
...
```
{% endtab %}
{% endtabs %}

프로젝트 폴더에서 flutter packages get 을 수행하면 설치됩니다. 설치되면, import 후 사용하면 됩니다.

## web/index.html 설정하기&#x20;

flutter web 빌드하면 web/index.html 파일이 생성됩니다. 해당 파일 Header에 아래 script를 추가해주세요.

```javascript
<script src="https://cdn.bootpay.co.kr/js/bootpay-3.3.3.min.js"></script>
<script src="bootpay_api.js" defer></script>
```

[bootpay\_api.js](https://github.com/bootpay/bootpay\_flutter/blob/main/example/web/bootpay\_api.js) 파일을 프로젝트 경로에 추가합니다. 파일 내용은 [링크](https://github.com/bootpay/bootpay\_flutter/blob/main/example/web/bootpay\_api.js)를 참조해주세요.

![](<../../.gitbook/assets/스크린샷 2021-09-16 오후 1.56.01.png>)

위 사진을 참조하셔서 프로젝트 구조를 구성하시면 되겠습니다.

## 결제창 띄우는 Flutter 코드

```swift
//https://github.com/bootpay/bootpay_flutter
import 'package:bootpay/bootpay.dart';
import 'package:bootpay/model/extra.dart';
import 'package:bootpay/model/item.dart';
import 'package:bootpay/model/payload.dart';
import 'package:bootpay/model/user.dart';
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  Payload payload = Payload();

  @override
  void initState() {
    // TODO: implement initState
    super.initState();

    Item item1 = Item();
    item1.itemName = "미키 마우스"; // 주문정보에 담길 상품명
    item1.qty = 1; // 해당 상품의 주문 수량
    item1.unique = "ITEM_CODE_MOUSE"; // 해당 상품의 고유 키
    item1.price = 500; // 상품의 가격

    Item item2 = Item();
    item2.itemName = "키보드"; // 주문정보에 담길 상품명
    item2.qty = 1; // 해당 상품의 주문 수량
    item2.unique = "ITEM_CODE_KEYBOARD"; // 해당 상품의 고유 키
    item2.price = 500; // 상품의 가격
    List<Item> itemList = [item1, item2];

    payload.webApplicationId = '5b8f6a4d396fa665fdc2b5e7'; // web application id
    payload.androidApplicationId = '5b8f6a4d396fa665fdc2b5e8'; // android application id
    payload.iosApplicationId = '5b8f6a4d396fa665fdc2b5e9'; // ios application id


    payload.pg = 'nicepay';
    payload.methods = ['card', 'phone', 'vbank', 'bank', 'kakao'];
    payload.name = '테스트 상품'; //결제할 상품명, 12글자 이내 추천 
    payload.price = 1000.0; //정기결제시 0 혹은 주석
    payload.orderId = DateTime.now().millisecondsSinceEpoch.toString(); //주문번호, 개발사에서 고유값으로 지정해야함
    payload.params = {
      "callbackParam1" : "value12",
      "callbackParam2" : "value34",
      "callbackParam3" : "value56",
      "callbackParam4" : "value78",
    }; // 전달할 파라미터, 결제 후 되돌려 주는 값
    payload.items = itemList; // 상품정보 배열

    User user = User(); // 구매자 정보
    user.username = "사용자 이름";
    user.email = "user1234@gmail.com";
    user.area = "서울";
    user.phone = "010-4033-4678";
    user.addr = '서울시 동작구 상도로 222';

    Extra extra = Extra(); // 결제 옵션
    extra.appScheme = 'bootpayFlutterExample';
    extra.quota = "0,2,3";
    //extra.popup = 1; - flutter web에서는 주석처리 
    //extra.quick_popup = 1; - flutter web에서는 주석처리 

    payload.user = user;
    payload.extra = extra;
  }

  String _data = "";
  void goBootpayTest(BuildContext context) {
    Bootpay().request(
      context: context,
      payload: payload,
      showCloseButton: false,
      closeButton: Icon(Icons.close, size: 35.0, color: Colors.black54),
      onCancel: (String data) {
        print('------- onCancel: $data');
      },
      onError: (String data) {
        print('------- onCancel: $data');
      },
      onClose: () {
        print('------- onClose');
        Bootpay().dismiss(context); //명시적으로 부트페이 뷰 종료 호출
        //TODO - 원하시는 라우터로 페이지 이동
      },
      onCloseHardware: () {
        print('------- onCloseHardware');
      },
      onReady: (String data) {
        print('------- onReady: $data');
      },
      onConfirm: (String data) {
        /**
        1. 바로 승인하고자 할 때
        return true;
        **/
        /***
        2. 비동기 승인 하고자 할 때
        checkQtyFromServer(data);
        return false;
        ***/
        checkQtyFromServer(data);
        return false;
      },
      onDone: (String data) {
        print('------- onDone: $data');
      },
    );
    
    Future<void> checkQtyFromServer(String data) async {
      //TODO 서버로부터 재고파악을 한다
      print('checkQtyFromServer http call');
  
      //재고파악 후 결제를 승인한다. 아래 함수를 호출하지 않으면 결제를 승인하지 않게된다.
      Bootpay().transactionConfirm(data);
    }
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: Builder(builder: (BuildContext context) {
          return Container(
            child: Center(
              child: TextButton(
                onPressed: () => goBootpayTest(context),
                child: Text('부트페이 결제테스트'),
              )
            ),
          );
        }),
      ),
    );
  }
}
```

## 결제진행 이벤트

{% hint style="info" %}
&#x20;결제 진행 상태에 따라 LifeCycle 함수가 실행됩니다. 각 함수에 대한 상세 설명은 아래를 참고하세요.
{% endhint %}

{% tabs %}
{% tab title="onError 함수" %}
결제 진행 중 오류가 발생된 경우 호출되는 함수입니다. 진행중 에러가 발생되는 경우는 다음과 같습니다.

1. **부트페이 관리자에서 활성화 하지 않은 PG, 결제수단을 사용하고자 할 때**
2. **PG에서 보내온 결제 정보를 부트페이 관리자에 잘못 입력하거나 입력하지 않은 경우**
3. **결제 진행 도중 한도초과, 카드정지, 휴대폰소액결제 막힘, 계좌이체 불가 등의 사유로 결제가 안되는 경우**
4. **PG에서 리턴된 값이 다른 Client에 의해 변조된 경우**

에러가 난 경우 해당 함수를 통해 관련 에러 메세지를 사용자에게 보여줄 수 있습니다.

&#x20;data 포맷은 아래와 같습니다.

```
{
  action: "BootpayError",
  message: "카드사 거절",
  receipt_id: "5fffab350c20b903e88a2cff"
}
```
{% endtab %}

{% tab title="onCancel 함수" %}
결제 진행 중 사용자가 PG 결제창에서 취소 혹은 닫기 버튼을 눌러 나온 경우 입니다. ****&#x20;

&#x20;data 포맷은 아래와 같습니다.

```
{
  action: "BootpayCancel",
  message: "사용자가 결제를 취소하였습니다.",
  receipt_id: "5fffab350c20b903e88a2cff"
}
```
{% endtab %}

{% tab title="onReady 함수" %}
가상계좌 발급이 완료되면 호출되는 함수입니다. 가상계좌는 다른 결제와 다르게 입금할 계좌 번호 발급 이후 입금 후에 Feedback URL을 통해 통지가 됩니다. 발급된 가상계좌 정보를 ready 함수를 통해 확인하실 수 있습니다.

&#x20; data 포맷은 아래와 같습니다.

```
{
  account: "T0309260001169"
  accounthodler: "한국사이버결제"
  action: "BootpayBankReady"
  bankcode: "BK03"
  bankname: "기업은행"
  expiredate: "2021-01-17 00:00:00"
  item_name: "테스트 아이템"
  method: "vbank"
  method_name: "가상계좌"
  order_id: "1610591554856"
  params: null
  payment_group: "vbank"
  payment_group_name: "가상계좌"
  payment_name: "가상계좌"
  pg: "kcp"
  pg_name: "KCP"
  price: 3000
  purchased_at: null
  ready_url: "https://dev-app.bootpay.co.kr/bank/7o044QyX7p"
  receipt_id: "5fffad430c20b903e88a2d17"
  requested_at: "2021-01-14 11:32:35"
  status: 2
  tax_free: 0
  url: "https://d-cdn.bootapi.com"
  username: "홍길동"
}
```
{% endtab %}

{% tab title="onConfirm 함수" %}
결제 승인이 되기 전 호출되는 함수입니다. 승인 이전 관련 로직을 서버 혹은 클라이언트에서 수행 후 결제를 승인해도 될 경우`BootPay.transactionConfirm(data); 또는 return true;`

코드를 실행해주시면 PG에서 결제 승인이 진행이 됩니다.

**\* 페이앱, 페이레터 PG는 이 함수가 실행되지 않고 바로 결제가 승인되는 PG 입니다. 참고해주시기 바랍니다.**

&#x20;data 포맷은 아래와 같습니다.

```
{
  receipt_id: "5fffc0460c20b903e88a2d2c",
  action: "BootpayConfirm"
}
```
{% endtab %}

{% tab title="onDone 함수" %}
PG에서 거래 승인 이후에 호출 되는 함수입니다. 결제 완료 후 다음 결제 결과를 호출 할 수 있는 함수 입니다.

이 함수가 호출 된 후 반드시 REST API를 통해 [결제검증](https://docs.bootpay.co.kr/rest/verify)을 수행해야합니다. data 포맷은 아래와 같습니다.

{% hint style="warning" %}
결제가 완료되더라도 클라이언트 상태에 따라서 브라우저가 종료되거나 reload 되는 등의 이슈가 있어서 done 이벤트를 못받을 수도 있습니다. 가장 확실한 처리 방법은 [webhook 통지시](../../webhook/server.md)에도 [결제검증](../../server/verify.md)을 하시어 아이템 지급이나 상품 발송 등의 비즈니스 로직을 수행하시면 더욱 완성도 있는 서비스 개발이 되시겠습니다.&#x20;
{% endhint %}

```
{
  action: "BootpayDone"
  card_code: "CCKM",
  card_name: "KB국민카드",
  card_no: "0000120000000014",
  card_quota: "00",
  item_name: "테스트 아이템",
  method: "card",
  method_name: "카드결제",
  order_id: "1610596422328",
  payment_group: "card",
  payment_group_name: "신용카드",
  payment_name: "카드결제",
  pg: "kcp",
  pg_name: "KCP",
  price: 100,
  purchased_at: "2021-01-14 12:54:53",
  receipt_id: "5fffc0460c20b903e88a2d2c",
  receipt_url: "https://app.bootpay.co.kr/bill/UFMvZzJqSWNDNU9ERWh1YmUycU9hdnBkV29DVlJqdzUxRzZyNXRXbkNVZW81%0AQT09LS1XYlNJN1VoMDI4Q1hRdDh1LS10MEtZVmE4c1dyWHNHTXpZTVVLUk1R%0APT0%3D%0A",
  requested_at: "2021-01-14 12:53:42",
  status: 1,
  tax_free: 0,
  url: "https://d-cdn.bootapi.com"
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
**결제 테스트 시 주의사항**

1. 결제 테스트시에는 소액(1,000원)으로 진행해주세요.
2. 가상계좌는 결제테스트하지 말아주세요.
3. 휴대폰 결제는 이월될 경우 취소되지 않습니다.

테스트로 결제를 하시더라도 실제 결제가 될 수 있습니다. 자동취소가 누락되어 취소되지 않을 수 있으니 테스트 결제는 소액으로 진행해주세요.&#x20;

가상계좌의 경우 계좌간 계좌이체 방식이기 때문에 이체간 비용이 발생하기에 PG사에서 정책적으로 가상계좌는 테스트 시 결제취소 기능을 제공하지 않습니다. 가상계좌를 테스트 하기 위해서는 PG사 가맹 후 발급받은 코드로 진행하시길 추천합니다.
{% endhint %}

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
