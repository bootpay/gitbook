# Flutter (웹앱)

Web에 구현된 서비스가 있고, Flutter에서는 Webview로 구현된 Web에 링크할때 이 페이지를 참조하시면 됩니다.

금융감독원에서는 온라인 신용카드 결제시 안심클릭 또는 ISP 안전결제를 권고하며, 이에 따라 카드사의 앱카드 앱이 앱투앱 방식으로 호출됩니다. 그러므로 웹뷰에서 Android, iOS 대응을 위한 외부앱 호출 및 통신을 위한 처리를 해주셔야 하는데, [bootpay\_webview\_flutter](https://pub.dev/packages/bootpay\_webview\_flutter)를 사용하시면 쉽게 해결됩니다.

{% hint style="info" %}
해당 라이브러리 적용하실때에는 꼭 부트페이에 버전문의를 주셔야 합니다. 기술문의 없이 적용시 결제진행이 정상동작 하지 않을 수 있습니다.
{% endhint %}

## Bootpay 설치하기

{% tabs %}
{% tab title="pubspec.yaml" %}
```java
...
dependencies:
 ...
 bootpay_webview_flutter: last_version
...
```
{% endtab %}
{% endtabs %}

프젝트 폴더에서 flutter packages get 을 수행하면 설치됩니다. 설치되면, import 후 사용하면 됩니다.

## 프로젝트 설정하기

{% tabs %}
{% tab title="Android" %}
Android는 별다른 설정이 필요하지 않습니다.

{% hint style="info" %}
실행시 **Webpage not available. net:ERR\_CLEARTEXT\_NOT\_PERMITTED 에러일 때**

Android Developer의 [Opt out of cleartext traffic](https://developer.android.com/training/articles/security-config#CertificatePinning)를 보시면 안드로이드 Pie(API28)부터 `cleartext HTTP`를 비활성화한다고 합니다. 따라서 API28 이후에서 Http에 접근하려면 `cleartext HTTP`를 활성화 시켜야 합니다.

```
<application
        android:label="@string/app_name"
        ...
        android:usesCleartextTraffic="true">
```
{% endhint %}

{% hint style="info" %}
[android 12](https://developer.android.com/about/versions/12?hl=ko)를 올바르게 적용하시려면, [bootpay\_webview\_flutter 2.2.0 버전 ](https://pub.dev/packages/bootpay\_webview\_flutter)이상을 적용하셔야 하며, minSdkVersion 21 이상으로 빌드하셔야 합니다. 또한 gradle 버전은 `classpath 'com.android.tools.build:gradle:4.2.0'` 이상을 추천합니다.
{% endhint %}
{% endtab %}

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

## webview\_flutter 대신 bootpay\_webview\_flutter를 사용하면 끝

```dart
//https://github.com/bootpay/bootpay_webview_flutter

import 'package:flutter/material.dart';
import 'dart:async';

import 'dart:io';
import 'package:bootpay_webview_flutter/webview_flutter.dart';

void main() => runApp(MaterialApp(home: WebViewExample()));

class WebViewExample extends StatefulWidget {
  @override
  _WebViewExampleState createState() => _WebViewExampleState();
}

class _WebViewExampleState extends State<WebViewExample> {
  final Completer<WebViewController> _controller = Completer<WebViewController>();

  @override
  void initState() {
    super.initState();
    if (Platform.isAndroid) WebView.platform = SurfaceAndroidWebView();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Flutter WebView example'),
        // This drop down menu demonstrates that Flutter widgets can be shown over the web view.
      ),
      // We're using a Builder here so we have a context that is below the Scaffold
      // to allow calling Scaffold.of(context) so we can show a snackbar.
      body: Builder(builder: (BuildContext context) {
        return WebView(
          initialUrl: 'https://d-cdn.bootapi.com/test/payment/',
          javascriptMode: JavascriptMode.unrestricted,
          onWebViewCreated: (WebViewController webViewController) {
            _controller.complete(webViewController);
          },
          onProgress: (int progress) {
            print("WebView is loading (progress : $progress%)");
          },
          navigationDelegate: (NavigationRequest request) {
            if (request.url.startsWith('https://www.youtube.com/')) {
              print('blocking navigation to $request}');
              return NavigationDecision.prevent;
            }
            print('allowing navigation to $request');
            return NavigationDecision.navigate;
          },
          onPageStarted: (String url) {
            print('Page started loading: $url');
          },
          onPageFinished: (String url) {
            print('Page finished loading: $url');
          },
          gestureNavigationEnabled: true,
        );
      }),
    );
  }
}
```

{% hint style="info" %}
```
navigationDelegate: (NavigationRequest request) {
    return NavigationDecision.navigate;
}
```

웹뷰 설정시 위 코드 부분은 필수로 입력해주셔야 합니다.
{% endhint %}

{% hint style="info" %}
[bootpay\_webview\_flutter](https://pub.dev/packages/bootpay\_webview\_flutter) 는 [webview\_flutter](https://pub.dev/packages/webview\_flutter)를 fork하여 빌드한 plugin 입니다. 한 프로젝트에 두가지 plugin을 함께 사용해도 충돌나지 않으며, webviewflutter 대신 bootpay\_webview\_flutter 만을 사용하셔도 무방합니다.
{% endhint %}

{% hint style="info" %}
결제 진행 상태 및 결과는 구현하신 웹 페이지에 의존하면 되므로, Flutter (웹앱)에서는 신경쓸게 없습니다. 결제 테스트 시 카드사 앱이 정상 호출되어 동작하여 결제가 이루어지는지 확인해주시면 되겠습니다.
{% endhint %}

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
