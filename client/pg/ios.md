# iOS

Native 방식으로 iOS 앱을 만들때 이 페이지를 참조하시면 됩니다.

PG 결제창은 기본적으로 Javascript로 연동됩니다. 부트페이 iOS SDK는 내부적으로 Webview 방식으로 구현하였으며, 사용방법은 아래와 같습니다.

{% hint style="info" %}
iOS 10 버전부터는 보안정책으로 **LSApplicationQueriesSchemes** 을 통하여 사용하고자 하는 URL scheme들을 등록하길 권장합니다. 하지만 부트페이에서는 각 은행사들의 scheme를 변경/추가/삭제에 대응하기 어렵다고 판단하여, custom URL scheme 요청시 WKWebView에서 앱투앱 처리를 합니다. 코드가 궁금하신 분들인[ 이 곳](https://github.com/bootpay/SwiftyBootpay/blob/master/SwiftyBootpay/Classes/BootpayWebView.swift)을 참고하세요
{% endhint %}

## Bootpay 설치하기

먼저 ObjectC, Swift로 부트페이를 설치하기 위해서는 [이곳](broken-reference/)을 참조해주세요. 설치하기 가이드 `ios` 를 참조해주세요.

```
pod 'Bootpay'  // object-C 또는 swift로 연동시 

또는 

pod 'BootpayUI' // swiftUI 또는 생체인증(Touch ID) 결제 이용시 
```

## 프로젝트 설정하기

결제 진행시 카드사앱 호출 후 기존 앱으로 돌아오기 위해서는 `info.plist`에서 `CFBundleURLName` 설정을 해주셔야 합니다. 들어갈 값은 Custom 하게 설정하시면 되겠습니다.

{% tabs %}
{% tab title="(iOS) info.plist" %}
```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    ...

    <key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads</key>
        <true/>
    </dict>
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleTypeRole</key>
            <string>Editor</string>
            <key>CFBundleURLName</key>
            <string>kr.co.bootpaySample</string> // 사용하고자 하시는 앱의 bundle url name
            <key>CFBundleURLSchemes</key>
            <array>
                <string>bootpaySample</string> // 사용하고자 하시는 앱의 bundle url scheme
            </array>
        </dict>
    </array>

    ...
    <key>NSFaceIDUsageDescription</key>
    <string>생체인증 결제 진행시 권한이 필요합니다</string>
</dict>
</plist>
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Object C로 연동하실 경우** Swift 사용에 대한 Bridge 파일을 생성해줘야 링크에러가 나지 않습니다. Proejct > New 에서 empty.swift 파일을 생성하시면, 자동으로 Bridge 헤더파일을 생성할 수 있습니다.
{% endhint %}

![](../../.gitbook/assets/create\_bridge\_header-f9b2c40160a9342cf23abc172e117906bd79ffc92a869042b239f365bbc36285.jpeg)

{% hint style="info" %}
**카드사 앱 실행 후 개발중인 원래 앱으로 돌아오지 않는 경우**

상단의 프로젝트 설정의 info.plist에서 CFBundleURLSchemes를 설정해주시면 부트페이 SDK가 해당 값을 읽어 extra.appScheme 에 값을 채워 결제데이터를 전송합니다.
{% endhint %}

## 결제창 띄우는 iOS 코드

{% tabs %}
{% tab title="Swift" %}
```swift
//https://github.com/bootpay/ios_swift/blob/main/Example/Bootpay/NativeController.swift
import UIKit
import Bootpay

class NativeController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        setUI()
    }
    
    func setUI() {
        self.view.backgroundColor = .white
        let btn = UIButton()
        btn.setTitle("결제하기", for: .normal)
        btn.addTarget(self, action: #selector(showBootpay), for: .touchUpInside)
        btn.frame = CGRect(
            x: self.view.frame.width/2 - 40,
            y: self.view.frame.height/2 - 40,
            width: 80,
            height: 80
        )
        btn.setTitleColor(.darkGray, for: .normal)
        self.view.addSubview(btn)
    }
    
    @objc func showBootpay() {
        let payload = Payload()
        payload.applicationId = "5b8f6a4d396fa665fdc2b5e9" //ios application id
                
        payload.price = 1000
        payload.orderId = String(NSTimeIntervalSince1970)
        payload.pg = "payletter"
        payload.method = "card"
        payload.name = "테스트 아이템"
        payload.extra = BootExtra() 
         
        let customParams: [String: String] = [
            "callbackParam1": "value12",
            "callbackParam2": "value34",
            "callbackParam3": "value56",
            "callbackParam4": "value78",
        ]
        
        payload.params = customParams
        

        let user = BootUser()
        user.username = "테스트 유저"
        user.phone = "01040334678"
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
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```
import SwiftUI
import WebKit
import Bootpay
import BootpayUI

struct ContentView: View {
//    @State private var showModal = false
    @State private var showingBootpay = false
    private var payload = Payload()
     


    var body: some View {
        GeometryReader { geometry in
            VStack {
                
                
                if(self.showingBootpay) {
                    BootpayUI(payload: payload)
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
                            self.showingBootpay = false 
                        }
                } else {
                    Button("부트페이 결제테스트") {
                        showingBootpay = true

                        #if os(macOS)
                        payload.applicationId = "5b8f6a4d396fa665fdc2b5e7" //web application id
                        #elseif os(iOS)
                        payload.applicationId = "5b8f6a4d396fa665fdc2b5e9" //ios application id
                        #endif
 

                        payload.price = 1000
                        payload.orderId = String(NSTimeIntervalSince1970)
                        payload.name = "테스트 아이템"
                         
                        let customParams: [String: String] = [
                            "callbackParam1": "value12",
                            "callbackParam2": "value34",
                            "callbackParam3": "value56",
                            "callbackParam4": "value78",
                        ]
                        
                        payload.params = customParams

                        let user = BootUser()
                        user.username = "테스트 유저"
                        user.phone = "01012345678"
                        payload.userInfo = user
                    }.sheet(isPresented: self.$showingBootpay) {
                    }
                }
            }.frame(width: geometry.size.width, height: geometry.size.height)
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```
{% endtab %}

{% tab title="Object-C" %}
```objectivec
// https://github.com/bootpay/bootpay_objc_example

#import "NativeController.h"
#import "Bootpay-Swift.h"


@implementation NativeController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view setBackgroundColor:[UIColor whiteColor]];
    
    [self setUI];
    [self bootpayInit];
}


- (void) setUI {
    float w = [[UIScreen mainScreen] bounds].size.width;
    float h = [[UIScreen mainScreen] bounds].size.height;
    
    UIButton *btn = [UIButton buttonWithType: UIButtonTypeRoundedRect];
    [btn setFrame: CGRectMake(0, 200, w, 100)];
    [btn setTitle:@"부트페이 결제하기" forState: UIControlStateNormal];
    [btn addTarget:self action:@selector(clickBtn:) forControlEvents: UIControlEventTouchUpInside];
    [self.view addSubview:btn];
    
}

- (void) clickBtn:(UIButton*)button {
    NSTimeInterval  today = [[NSDate date] timeIntervalSince1970];
    Payload *payload = [[Payload alloc] init];
    payload.applicationId = @"5b8f6a4d396fa665fdc2b5e9";
    payload.price = 1000;
    payload.orderId = [NSString stringWithFormat:@"%f", today];
    payload.pg = @"payletter";
    payload.method = @"card";
    payload.name = @"테스트 아이템";

    payload.extra = [[BootExtra alloc] init];
    payload.extra.popup = 0;
    payload.extra.quickPopup = 0;

    payload.userInfo = [[BootUser alloc] init];
    payload.userInfo.username = @"테스트 유저";
    payload.userInfo.phone = @"01040334678";
     
    
    [Bootpay requestPayment:self :payload :false :nil];
}


- (void) bootpayInit {
    [Bootpay onCancel: ^(NSDictionary *dic) {
        NSLog(@"onCancel: %@", dic);
    }];
    [Bootpay onDone: ^(NSDictionary *dic) {
        NSLog(@"onDone: %@", dic);
    }];
    [Bootpay onError:^(NSDictionary *dic) {
        NSLog(@"onError: %@", dic);
    }];
    [Bootpay onReady: ^(NSDictionary *dic) {
        NSLog(@"onReady: %@", dic);
    }];
    [Bootpay onConfirm:^BOOL(NSDictionary *dic) {
        NSLog(@"onConfirm: %@", dic);
        return true;
    }];
    [Bootpay onClose: ^() {
        NSLog(@"onClose");
    }];
}

@end
```
{% endtab %}
{% endtabs %}

## 결제진행 이벤트

{% hint style="info" %}
결제 진행 상태에 따라 LifeCycle 함수가 실행됩니다. 각 함수에 대한 상세 설명은 아래를 참고하세요.
{% endhint %}

{% tabs %}
{% tab title="onError 함수" %}
결제 진행 중 오류가 발생된 경우 호출되는 함수입니다. 진행중 에러가 발생되는 경우는 다음과 같습니다.

1. **부트페이 관리자에서 활성화 하지 않은 PG, 결제수단을 사용하고자 할 때**
2. **PG에서 보내온 결제 정보를 부트페이 관리자에 잘못 입력하거나 입력하지 않은 경우**
3. **결제 진행 도중 한도초과, 카드정지, 휴대폰소액결제 막힘, 계좌이체 불가 등의 사유로 결제가 안되는 경우**
4. **PG에서 리턴된 값이 다른 Client에 의해 변조된 경우**

에러가 난 경우 해당 함수를 통해 관련 에러 메세지를 사용자에게 보여줄 수 있습니다.

data 포맷은 아래와 같습니다.

```
{
  action: "BootpayError",
  message: "카드사 거절",
  receipt_id: "5fffab350c20b903e88a2cff"
}
```
{% endtab %}

{% tab title="onCancel 함수" %}
결제 진행 중 사용자가 PG 결제창에서 취소 혹은 닫기 버튼을 눌러 나온 경우 입니다. \*\*\*\*

data 포맷은 아래와 같습니다.

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

data 포맷은 아래와 같습니다.

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

data 포맷은 아래와 같습니다.

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
결제가 완료되더라도 클라이언트 상태에 따라서 브라우저가 종료되거나 reload 되는 등의 이슈가 있어서 done 이벤트를 못받을 수도 있습니다. 가장 확실한 처리 방법은 [webhook 통지시](../../webhook/server.md)에도 [결제검증](../../server/verify.md)을 하시어 아이템 지급이나 상품 발송 등의 비즈니스 로직을 수행하시면 더욱 완성도 있는 서비스 개발이 되시겠습니다.
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

테스트로 결제를 하시더라도 실제 결제가 될 수 있습니다. 자동취소가 누락되어 취소되지 않을 수 있으니 테스트 결제는 소액으로 진행해주세요.

가상계좌의 경우 계좌간 계좌이체 방식이기 때문에 이체간 비용이 발생하기에 PG사에서 정책적으로 가상계좌는 테스트 시 결제취소 기능을 제공하지 않습니다. 가상계좌를 테스트 하기 위해서는 PG사 가맹 후 발급받은 코드로 진행하시길 추천합니다.
{% endhint %}

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
