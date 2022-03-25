# 연동 예제

통계는 클라이언트 SDK로 연동됩니다. 결제요청 시 통계를 위한 추가 데이터를 보내주시거나, 페이지 이동시 통계전용 함수를 호출해주시면 되겠습니다.

## Bootpay 설치하기

설치하기는 [이곳](broken-reference/)을 참조해주세요. 사용하실 언어별로 정리되어있으니 살펴보시면 되겠습니다.

## 1. 회원 추적하기

로그인한 사용자의 정보를 채워 함수를 실행합니다. 로그인 직 후 한번만 호출하시면 되겠습니다.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
BootPay.startLoginSession({
    id: '[ 회원의 고유 아이디 ]',
    username: '[ 가입된 회원의 이름 혹은 닉네임 ]',
    birth: '[ 가입된 회원의 생년월일 ex) 1988-06-10 (yyyy-mm-dd 포맷으로 보내주세요) ]',
    phone: '[ 회원의 전화번호 ]',
    email: '[ 회원의 이메일 정보 ]',
    gender: '[ 회원의 성별 ] ex) 0 - 여자, 1 - 남자',
    area: '[ 회원의 거주지 혹은 배송지 ] ex) 서울|인천|대구|광주|부산|울산|경기|강원|충청북도|충북|충청남도|충남|전라북도|전북|전라남도|전남|경상북도|경북|경상남도|경남|제주|세종|대전'
});
```
{% endtab %}

{% tab title="Android" %}
```
BootpayAnalytics.init(this, application_id); //this는 context, 한번만 호출하면 된다

BootpayAnalytics.userTrace(
      "user_1234", //user_id
      "test1234@gmail.com", //email
      "홍길동", //user name
      1, //성별 남자:1, 여자:0
      "19941014", //생년월일
      "01012345678", //고객 전화번호
      "서울" //ex) 서울|인천|대구|광주|부산|울산|경기|강원|충청북도|충북|충청남도|충남|전라북도|전북|전라남도|전남|경상북도|경북|경상남도|경남|제주|세종|대전 중 1
);
```
{% endtab %}

{% tab title="iOS" %}
```
//회원이 로그인 했을때 한번 호출하여, 통계를 쌓는다
BootpayAnalytics.userTrace(
        id: "1234",
        email: "testUser@gmail.com",
        gender: 1,
        birth: "1994-10-14",
        phone: "01012341234",
        area: "서울",
        applicationId: "5b8f6a4d396fa665fdc2b5e9" //ios application id
)
```
{% endtab %}

{% tab title="Flutter" %}
#### Bootpay 설치하기

```java
...
dependencies:
 ...
 bootpay: last_version
...
```

프로젝트 폴더에서 flutter packages get 을 수행하면 설치됩니다.

#### web/index.html 설정하기

flutter web 빌드하면 web/index.html 파일이 생성됩니다. 해당 파일 Header에 아래 script를 추가해주세요.

```javascript
<script src="https://cdn.bootpay.co.kr/js/bootpay-3.3.3.min.js"></script>
<script src="bootpay_api.js" defer></script>
```

bootpay\_api.js 파일을 프로젝트 경로에 추가합니다. 파일 내용은 링크를 참조해주세요.

![](<../../.gitbook/assets/스크린샷 2021-09-16 오후 1.56.01.png>)

위 사진을 참조하셔서 프로젝트 구조를 구성하시면 되겠습니다.

#### 1. 회원 추적하기

```dart
//통계용 함수
  bootpayAnalyticsUserTrace() async {
    String? ver;
    if(kIsWeb) ver = '1.0'; //web 일 경우 버전 지정, 웹이 아닌 android, ios일 경우 package_info 통해 자동으로 생성


    await Bootpay().userTrace(
        id: 'user_1234',
        email: 'user1234@gmail.com',
        gender: -1,
        birth: '19941014',
        area: '서울',
        applicationId: applicationId,
        ver: ver
    );
  }
```
{% endtab %}

{% tab title="React Native" %}
```
import { BootpayWebView, userTrace, pageTrace } from 'react-native-bootpay-api';

//회원 추적
await userTrace(
  '5b8f6a4d396fa665fdc2b5e9',
  'user_1234',
  '01012345678',
  'rupy1014@gmail.com',
  1,
  '861014',
  '서울'
);
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**회원의 정보 중 이메일과 연락처 정보는 왜 필요한가요?**

특정 고객군을 타겟으로 마케팅을 하고 싶은 경우 \* 구매충성도, 방문횟수에 따른 통계등을 이용한 방법으로 해당 사용자에게 프로모션 혹은 이벤트 정보를 발송하기 위한 용도로 사용됩니다. 이 과정에서 제 3자 마케팅 동의가 반드시 필요합니다. 만약 마케팅을 하지 않으신다면, 이메일 정보와 연락처를 안보내시면 됩니다.
{% endhint %}

## 2. 페이지 방문

url이 변경되거나 앱에서 화면이 전환 될 경우 페이지 추적을 원할 경우 이 함수를 호출합니다.

{% tabs %}
{% tab title="JavaScript" %}
일반 페이지의 경우

```
document.addEventListener('DOMContentLoaded', function() {
  BootPay.startTrace();
});

// OR

$(document).ready(function() {
  BootPay.startTrace();
});
```

상품 판매 페이지의 경우

```
document.addEventListener('DOMContentLoaded', function() {
  BootPay.startTrace({
    items: [
      {
        item_name: '판매하는 상품1', // 판매하는 상품의 이름
        item_img: 'https://image.com/image1.png', // 판매하는 상품의 이미지 경로
        unique: 'item_unique_1', // 판매하는 상품의 고유 KEY
        price: '10000', // 판매하는 상품의 가격
        cat1: '상품 카테고리 1', // 상품 카테고리 1
        cat2: '상품 카테고리 2', // 상품 카테고리 2
        cat3: '상품 카테고리 3' // 상품 카테고리 3
      },
      {
        item_name: '판매하는 상품2', // 판매하는 상품의 이름
        item_img: 'https://image.com/image2.png', // 판매하는 상품의 이미지 경로
        unique: 'item_unique_2', // 판매하는 상품의 고유 KEY
        price: '10000', // 판매하는 상품의 가격
        cat1: '상품 카테고리 1', // 상품 카테고리 1
        cat2: '상품 카테고리 2', // 상품 카테고리 2
        cat3: '상품 카테고리 3' // 상품 카테고리 3
      }
    ]
  });
});

// OR

$(document).ready(function() {
  BootPay.startTrace({
    items: [
      {
        item_name: '판매하는 상품1', // 판매하는 상품의 이름
        item_img: 'https://image.com/image1.png', // 판매하는 상품의 이미지 경로
        unique: 'item_unique_1', // 판매하는 상품의 고유 KEY
        price: '10000', // 판매하는 상품의 가격
        cat1: '상품 카테고리 1', // 상품 카테고리 1
        cat2: '상품 카테고리 2', // 상품 카테고리 2
        cat3: '상품 카테고리 3' // 상품 카테고리 3
      },
      {
        item_name: '판매하는 상품2', // 판매하는 상품의 이름
        item_img: 'https://image.com/image2.png', // 판매하는 상품의 이미지 경로
        unique: 'item_unique_2', // 판매하는 상품의 고유 KEY
        price: '10000', // 판매하는 상품의 가격
        cat1: '상품 카테고리 1', // 상품 카테고리 1
        cat2: '상품 카테고리 2', // 상품 카테고리 2
        cat3: '상품 카테고리 3' // 상품 카테고리 3
      }
    ]
  });
});
```
{% endtab %}

{% tab title="Android" %}
```
BootpayAnalytics.init(this, application_id); //this는 context, 한번만 호출하면 된다 

List<BootStatItem> items = new ArrayList<>();
BootStatItem item1 = new BootStatItem().setItemName("마우's 스").setUnique("ITEM_CODE_MOUSE").setPrice(500d);
BootStatItem item2 = new BootStatItem().setItemName("키보드").setUnique("ITEM_KEYBOARD_MOUSE").setPrice(500d);
items.add(item1);
items.add(item2);

BootpayAnalytics.pageTrace(
        "url", // 앱 페이지 url 또는 화면이름
        "abcde",  // 분류에 해당하지만 아직 기능 미지원
        items //상품정보 
);
```
{% endtab %}

{% tab title="iOS" %}
```
//url이나 앱 화면 변경시 상품페이지 정보를 보내, 통계를 쌓는다
let item1 = BootpayStatItem()
item1.itemName = "나는 아이템1"
item1.unique = "item_01"
item1.price = 500
item1.cat1 = "TOP"
item1.cat2 = "티셔츠"
item1.cat3 = "반팔티"

let item2 = BootpayStatItem()
item2.itemName = "나는 아이템1"
item2.unique = "item_02"
item2.price = 250
item2.cat1 = "TOP"
item2.cat2 = "데님"
item2.cat3 = "청자켓"

BootpayAnalytics.pageTrace(
    "main_page_1234", // 앱 페이지 url 또는 화면이름
    applicationId: "5b8f6a4d396fa665fdc2b5e9",
    items: [item1, item2]
)
```
{% endtab %}

{% tab title="Flutter" %}
일반 페이지의 경우

```
bootpayAnalyticsPageTrace() async {
    String? ver;
    if(kIsWeb) ver = '1.0'; //web 일 경우 버전 지정, 웹이 아닌 android, ios일 경우 package_info 통해 자동으로 생성

    await Bootpay().pageTrace(
        url: 'main_1234',
        pageType: 'sub_page_1234',
        applicationId: applicationId,
        userId: 'user_1234',
        ver: ver
    );
  }
```

상품 판매 페이지의 경우

```
bootpayAnalyticsPageTrace() async {
    String? ver;
    if(kIsWeb) ver = '1.0'; //web 일 경우 버전 지정, 웹이 아닌 android, ios일 경우 package_info 통해 자동으로 생성

    StatItem item1 = StatItem();
    item1.itemName = "미키 마우스"; // 주문정보에 담길 상품명
    item1.unique = "ITEM_CODE_MOUSE"; // 해당 상품의 고유 키
    item1.price = 500; // 상품의 가격
    item1.cat1 = '컴퓨터';
    item1.cat2 = '주변기기';

    StatItem item2 = StatItem();
    item2.itemName = "키보드"; // 주문정보에 담길 상품명
    item2.unique = "ITEM_CODE_KEYBOARD"; // 해당 상품의 고유 키
    item2.price = 500; // 상품의 가격
    item2.cat1 = '컴퓨터';
    item2.cat2 = '주변기기';

    List<StatItem> items = [item1, item2];

    await Bootpay().pageTrace(
        url: 'main_1234',
        pageType: 'sub_page_1234',
        applicationId: applicationId,
        userId: 'user_1234',
        items: items,
        ver: ver
    );
  }
```
{% endtab %}

{% tab title="React Native" %}
```
//결제되는 상품정보들로 통계에 사용되며, price의 합은 결제금액과 동일해야함 
const items = [
  {
    item_name: '키보드', //통계에 반영될 상품명  
    unique: 'ITEM_CODE_KEYBOARD', //개발사에서 관리하는 상품고유번호 
    price: 1000, //상품단가 
    cat1: '패션', //카테고리 상 , 자유롭게 기술
    cat2: '여성상의', //카테고리 중, 자유롭게 기술 
    cat3: '블라우스', //카테고리 하, 자유롭게 기술
  },
  {
    item_name: '마우스', //통계에 반영될 상품명  
    unique: 'ITEM_CODE_KEYBOARD', //개발사에서 관리하는 상품고유번호 
    price: 2000, //상품단가 
    cat1: '패션2', //카테고리 상 , 자유롭게 기술
    cat2: '여성상의2', //카테고리 중, 자유롭게 기술 
    cat3: '블라우스2', //카테고리 하, 자유롭게 기술
  }
]

//페이지 추적 
await pageTrace(
  '5b8f6a4d396fa665fdc2b5e9',
  'main_page_1234',
  '',
  items
);
```
{% endtab %}
{% endtabs %}

## 3. 결제호출 시 데이터 추가

결제시점부터 분석할 데이터를 추가 하고자 반 한다면 아래와 같이 상품정보, 회원정보를 넣어주시면 되겠습니다.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
BootPay.request({
	items: [
	   {
		item_name: '나는 아이템', //상품명
		qty: 1, //수량
		unique: '123', //해당 상품을 구분짓는 primary key
		price: 1000, //상품 단가
		cat1: 'TOP', // 대표 상품의 카테고리 상, 50글자 이내
		cat2: '티셔츠', // 대표 상품의 카테고리 중, 50글자 이내
		cat3: '라운드 티', // 대표상품의 카테고리 하, 50글자 이내
	   }
	],
	user_info: {
		username: '사용자 이름',
		email: '사용자 이메일',
		addr: '사용자 주소',
		phone: '010-1234-4567'
	},
	...
})
```
{% endtab %}

{% tab title="Android" %}
```
BootpayAnalytics.init(this, application_id); //this는 context, 한번만 호출하면 된다

//통계용 데이터 추가 
List<BootItem> items = new ArrayList<>();
BootItem item1 = new BootItem().setItemName("마우's 스").setUnique("ITEM_CODE_MOUSE").setQty(1).setPrice(500d);
BootItem item2 = new BootItem().setItemName("키보드").setUnique("ITEM_KEYBOARD_MOUSE").setQty(1).setPrice(500d);
items.add(item1);
items.add(item2);

Payload payload = new Payload();
payload.setApplicationId(application_id)
        .setName("맥\"북프로's 임다")
        .setPg(pg)
        .setOrderId("1234")
        .setMethod(method)
        .setPrice(price)
        .setUser(user)
        .setExtra(extra)
        .setItems(items);
Map<String, Object> map = new HashMap<>();
map.put("1", "abcdef");
map.put("2", "abcdef55");
map.put("3", 1234);
payload.setParams(map);

Bootpay.init(getSupportFragmentManager(), getApplicationContext())
        .setPayload(payload)
        .setEventListener(new BootpayEventListener() {
            @Override
            public void onCancel(String data) {
                Log.d("cancel", data);
            }

            @Override
            public void onError(String data) {
                Log.d("error", data);
            }

            @Override
            public void onClose(String data) {
                Log.d("close", data);
                Bootpay.removePaymentWindow();
            }

            @Override
            public void onReady(String data) {
                Log.d("ready", data);
            }

            @Override
            public boolean onConfirm(String data) {
                Log.d("confirm", data);
//                        Bootpay.transactionConfirm(data); //재고가 있어서 결제를 진행하려 할때 true (방법 1)
                return true; //재고가 있어서 결제를 진행하려 할때 true (방법 2)
//                        return false; //결제를 진행하지 않을때 false
            }

            @Override
            public void onDone(String data) {
                Log.d("done", data);
            }
        }).requestPayment();
```
{% endtab %}

{% tab title="iOS" %}
```
let payload = Payload()
//        payload.applicationId = "5b8f6a4d396fa665fdc2b5e9" //ios application id
payload.applicationId = "59f95ca684382624094ea74a" //ios application id

        
payload.price = 0
payload.orderId = String(NSTimeIntervalSince1970)
payload.pg = "danal"
payload.method = "auth"
payload.name = "테스트 아이템"
payload.extra = BootExtra()
payload.extra?.popup = 0
payload.extra?.quickPopup = 0
//        payload.extra?.carrier = "SKT" //본인인증 시 고정할 통신사명, SKT,KT,LGT 중 1개만 가능
//        payload.extra?.ageLimit = 40 // 본인인증시 제한할 최소 나이 ex) 20 -> 20살 이상만 인증이 가능


//통계를 위한 상품데이터
let item1 = BootItem()
item1.itemName = "나는 아이템1"
item1.qty = 1
item1.unique = "item_01"
item1.price = 500
item1.cat1 = "TOP"
item1.cat2 = "티셔츠"
item1.cat3 = "반팔티"

let item2 = BootItem()
item2.itemName = "나는 아이템1"
item2.qty = 2
item2.unique = "item_02"
item2.price = 250
item2.cat1 = "TOP"
item2.cat2 = "데님"
item2.cat3 = "청자켓"
payload.items = [item1, item2]


let user = BootUser()
user.username = "테스트 유저"
user.phone = "01012345678"
payload.userInfo = user
        
Bootpay.requestPayment(viewController: self, payload: payload)
    .onCancel { data in
        print("-- cancel: \(data)")
    }
    .onReady { data in
        print("-- ready: \(data)")
    }
    .onConfirm { data in
        print("-- confirm: \(data)")
        return true //재고가 있어서 결제를 최종 승인하려 할 경우
//                            return false //재고가 없어서 결제를 승인하지 않을때
    }
    .onDone { data in
        print("-- done: \(data)")
    }
    .onError { data in
        print("-- error: \(data)")
    }
    .onClose {
        print("-- close")
    }
```
{% endtab %}

{% tab title="Flutter" %}
```
//결제용 데이터 init
bootpayReqeustDataInit() {
  //통계를 위한 상품정보 
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

  payload.pg = 'welcome';
  payload.method = 'card';
  // payload.methods = ['card', 'phone', 'vbank', 'bank', 'kakao'];
  payload.name = '테스트 상품'; //결제할 상품명
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
  user.phone = "010-1234-5678";
  user.addr = '서울시 동작구 상도로 222';

  Extra extra = Extra(); // 결제 옵션
  extra.appScheme = 'bootpayFlutterExample';
  extra.quotas = [0,2,3];
  extra.popup = 1;
  extra.quickPopup = 1;
  // extra.clo

  // extra.carrier = "SKT,KT,LGT"; //본인인증 시 고정할 통신사명
  // extra.ageLimit = 20; // 본인인증시 제한할 최소 나이 ex) 20 -> 20살 이상만 인증이 가능

  payload.user = user;
  payload.extra = extra;
}



//버튼클릭시 부트페이 결제요청 실행
void goBootpayTest(BuildContext context) {
  Bootpay().request(
    context: context,
    payload: payload,
    showCloseButton: true,
    // closeButton: Icon(Icons.close, size: 35.0, color: Colors.black54),
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
      print('------- onConfirm: $data');
      return true; //결제를 최종 승인하고자 할때 return true

      //서버승인을 위한 로직 시작
      // _data = data;
      // Future.delayed(const Duration(milliseconds: 100), () {
      //   Bootpay().transactionConfirm(_data); // 서버승인 이용시 해당 함수 호출
      // });
      // return false;
      //서버 승인을 위한 로직 끝
    },
    onDone: (String data) {
      print('------- onDone: $data');
    },
  );
}
```
{% endtab %}

{% tab title="React Native" %}
```
const payload = {
  pg: 'nicepay',  //['kcp', 'danal', 'inicis', 'nicepay', 'lgup', 'toss', 'payapp', 'easypay', 'jtnet', 'tpay', 'mobilians', 'payletter', 'onestore', 'welcome'] 중 택 1
  name: '마스카라', //결제창에 보여질 상품명
  order_id: '1234_1234', //개발사에 관리하는 주문번호 
  method: 'phone', 
  price: 1000 //결제금액 
} 

//결제되는 상품정보들로 통계에 사용되며, price의 합은 결제금액과 동일해야함 
const items = [
  {
    item_name: '키보드', //통계에 반영될 상품명 
    qty: 1, //수량 
    unique: 'ITEM_CODE_KEYBOARD', //개발사에서 관리하는 상품고유번호 
    price: 1000, //상품단가 
    cat1: '패션', //카테고리 상 , 자유롭게 기술
    cat2: '여성상의', //카테고리 중, 자유롭게 기술 
    cat3: '블라우스', //카테고리 하, 자유롭게 기술
  }
]

//구매자 정보로 결제창이 미리 적용될 수 있으며, 통계에도 사용되는 정보 
const user = {
  id: 'user_id_1234', //개발사에서 관리하는 회원고유번호 
  username: '홍길동', //구매자명
  email: 'user1234@gmail.com', //구매자 이메일
  gender: 0, //성별, 1:남자 , 0:여자
  birth: '1986-10-14', //생년월일 yyyy-MM-dd
  phone: '01012345678', //전화번호, 페이앱 필수 
  area: '서울', // [서울,인천,대구,광주,부산,울산,경기,강원,충청북도,충북,충청남도,충남,전라북도,전북,전라남도,전남,경상북도,경북,경상남도,경남,제주,세종,대전] 중 택 1
  addr: '서울시 동작구 상도로' //주소
}


//기타 설정
const extra = {
  app_scheme: "bootpayrnapi", //ios의 경우 카드사 앱 호출 후 되돌아오기 위한 앱 스키마명 
  expire_month: "0", //정기결제가 적용되는 개월 수 (정기결제 사용시), 미지정일시 PG사 기본값에 따름
  vbank_result: true, //가상계좌 결과창을 볼지(true), 말지(false)
  start_at: "",  //정기 결제 시작일 - 지정하지 않을 경우 - 그 날 당일로부터 결제가 가능한 Billing key 지급, "2020-10-14"
  end_at: "", // 정기결제 만료일 - 기간 없음 - 무제한, "2020-10-14"
  quota: "0,2,3",  //결제금액이 5만원 이상시 할부개월 허용범위를 설정할 수 있음, [0(일시불), 2개월, 3개월] 허용, 미설정시 12개월까지 허용
  offer_period: "", //결제창 제공기간에 해당하는 string 값, 지원하는 PG만 적용됨
  popup: 1, //1이면 popup, 아니면 iframe 연동
  quick_popup: 1, //1: popup 호출시 버튼을 띄우지 않는다. 아닐 경우 버튼을 호출한다
  locale: "ko", 
  disp_cash_result: "Y",  // 현금영수증 보일지 말지.. 가상계좌 KCP 옵션
  escrow: "0",  // 에스크로 쓸지 안쓸지
  theme: "purple", 
  custom_background: "", 
  custom_font_color: "", 
  show_close_button: true 
} 

if(bootpay != null && bootpay.current != null) bootpay.current.request(payload, items, user, extra);
```
{% endtab %}
{% endtabs %}

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
