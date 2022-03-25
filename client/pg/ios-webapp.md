# iOS (웹앱)

Web에 구현된 서비스가 있고, iOS에서는 Webview로 구현된 Web에 링크할때 이 페이지를 참조하시면 됩니다.&#x20;

금융감독원에서는 온라인 신용카드 결제시 안심클릭 또는 ISP 안전결제를 권고하며, 이에 따라 카드사의 앱카드 앱이 [URL Scheme 방식](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)으로 호출됩니다. 그러므로 iOS에서 외부앱 호출 및 통신을 위한 처리를 해주셔야 하는데, BootpayWebview를 사용하시면 쉽게 해결됩니다. &#x20;

PG 결제창은 기본적으로 Javascript로 연동됩니다. 부트페이 iOS SDK는 내부적으로 Webview 방식으로 구현하였으며, 사용방법은 아래와 같습니다.&#x20;

## Bootpay 설치하기&#x20;

먼저 ObjectC, Swift로 부트페이를 설치하기 위해서는 [이곳](broken-reference)을 참조해주세요. 설치하기 가이드 `ios` 를 참조해주세요.&#x20;

```
pod 'Bootpay'  // object-C 또는 swift로 연동시 

또는 

pod 'BootpayUI' // swiftUI 또는 생체인증(Touch ID) 결제 이용시 
```

&#x20;

## 프로젝트 설정하기

결제 진행시 카드사앱 호출 후 기존 앱으로 돌아오기 위해서는 `info.plist`에서 `CFBundleURLName` 설정을 해주셔야 합니다. 들어갈 값은 Custom 하게 설정하시면 되겠습니다.&#x20;

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

상단의 프로젝트 설정의 info.plist에서 CFBundleURLSchemes를 설정해주시면 부트페이 SDK가 해당 값을 읽어 extra.appScheme 에 값을 채워 결제데이터를 전송합니다.&#x20;
{% endhint %}

## Webview 대신 BootpayWebView를 사용하면 끝

{% tabs %}
{% tab title="Swift" %}
```swift

//https://github.com/bootpay/ios_swift/blob/main/Example/Bootpay/WebAppController.swift

import UIKit
import Bootpay

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        setUIWebView()
    }
    
    func setUIWebView() {
        let webview = BootpayWebView()
        
        var topPadding = CGFloat(0)
        var bottomPadding = CGFloat(0)
        if #available(iOS 11.0, *) {
            let window = UIApplication.shared.keyWindow
            topPadding = window?.safeAreaInsets.top ?? CGFloat(0)
            bottomPadding = window?.safeAreaInsets.bottom ?? CGFloat(0)
        }
        
        webview.frame = CGRect(x: 0,
                               y: topPadding,
                               width: UIScreen.main.bounds.width,
                               height: UIScreen.main.bounds.height - topPadding - bottomPadding)
        webview.webview.frame = CGRect(x: 0,
                               y: topPadding,
                               width: UIScreen.main.bounds.width,
                               height: UIScreen.main.bounds.height - topPadding - bottomPadding - 60)
        
        if let url = URL(string: "https:/www.yourdomain.com") {
            webview.webview.load(URLRequest(url: url))
        }
        
        self.view.addSubview(webview)
    }
}
```
{% endtab %}

{% tab title="Object-C" %}
```objectivec
// https://github.com/bootpay/bootpay_objc_example

#import "WebAppController.h"
#import "Bootpay-Swift.h"

@implementation WebAppController 


- (void)viewDidLoad {
    [super viewDidLoad];
    
    [self setUI];
}

- (void) setUI {
    BootpayWebView *webView = [[BootpayWebView alloc] init];
    [webView setFrame:self.view.frame];
    [webView.webview setFrame:self.view.frame];
    [self.view addSubview:webView];
    
    NSString *url = @"https://www.google.com";
    NSURLRequest *request = [[NSURLRequest alloc] initWithURL:[NSURL URLWithString: url]];
    [webView.webview loadRequest:request];
}
 
@end

```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
결제 진행 상태 및 결과는 구현하신 웹 페이지에 의존하면 되므로, iOS (웹앱)에서는 결제테스트 시 카드사 앱 호출, 결제진행 후 원래 기존 앱으로 돌아오는 것 까지 확인해주시면 되겠습니다.
{% endhint %}

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
