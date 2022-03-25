# React Native (웹앱)

Web에 구현된 서비스가 있고, React Native 에서는 Webview로 구현된 Web에 링크할때 이 페이지를 참조하시면 됩니다.&#x20;

금융감독원에서는 온라인 신용카드 결제시 안심클릭 또는 ISP 안전결제를 권고하며, 이에 따라 카드사의 앱카드 앱이 앱투앱 방식으로 호출됩니다. 그러므로 웹뷰에서 Android, iOS 대응을 위한 외부앱 호출 및 통신을 위한 처리를 해주셔야 하는데, [react-native-webview-bootpay](https://github.com/bootpay/react-native-webview-bootpay) 를 사용하시면 쉽게 해결됩니다. &#x20;

### Bootpay 설치하기&#x20;

{% tabs %}
{% tab title="npm" %}
```java
npm install react-native-webview-bootpay --save
```
{% endtab %}

{% tab title="yarn" %}
```
yarn add react-native-webview-bootpay
```
{% endtab %}
{% endtabs %}

또는 package.json 파일의 dependencies에 추가 후 yarn install을 합니다.

```swift
// ...
"dependencies": {
  "react-native-webview-bootpay": last_version
},
//...
```

### 추가 모듈 설치&#x20;

{% code title="package.json" %}
```java
"devDependencies": {    
    "react-native-webview-bootpay": last_version,
    "react-native-device-info": last_version,
    "react-native-sensitive-info": last_version,
    "react-native-base64": last_version,
},      

```
{% endcode %}

### 프로젝트 설정하기

{% tabs %}
{% tab title="Android" %}
Android는 별다른 설정이 필요하지 않습니다.

{% hint style="info" %}
실행시 Cleartext HTTP traffic to Your.Domain not permitted 와 같은 에러가 나온 경우&#x20;

Android Developer의 [Opt out of cleartext traffic](https://developer.android.com/training/articles/security-config#CertificatePinning)를 보시면 안드로이드 Pie(API28)부터 `cleartext HTTP`를 비활성화한다고 합니다. 따라서 API28 이후에서 Http에 접근하려면 `cleartext HTTP`를 활성화 시켜야 합니다.

```
<application
        android:label="@string/app_name"
        ...
        android:usesCleartextTraffic="true">
```
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

## [react-native-webview](https://github.com/react-native-webview/react-native-webview) 대신 [react-native-webview-bootpay](https://github.com/bootpay/react-native-webview-bootpay)를 사용

```swift
// https://github.com/bootpay/react-native-webview-bootpay
import React, {Component} from 'react';
import {
  StyleSheet,
  SafeAreaView
} from 'react-native';

import WebView from 'react-native-webview-bootpay';

export default class App extends Component {
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <WebView
          source={{uri: "http://www.google.com"}}
          automaticallyAdjustContentInsets={false}
        />
      </SafeAreaView>
    );
  }
}
  
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5FCFF',
    padding: 8,
  }
});

```

{% hint style="info" %}
결제 진행 상태 및 결과는 구현하신 웹 페이지에 의존하면 되므로, React Native (웹앱)에서는 신경쓸게 없습니다. 결제 테스트 시 카드사 앱이 정상 호출되어 동작하여 결제가 이루어지는지 확인해주시면 되겠습니다.
{% endhint %}

{% hint style="info" %}
[react-native-webview-bootpay](https://github.com/bootpay/react-native-webview-bootpay) 는 [react-native-webview](https://github.com/react-native-webview/react-native-webview)를 fork하여 빌드한 plugin 입니다. 한 프로젝트에 두가지 plugin을 함께 사용해도 충돌나지 않으며, react-native-webview 대신 react-native-webview-bootpay 만을 사용하셔도 무방합니다. **단, javascript message 전달 시 alias가 다르므로 아래를 참조하여 구현해주세요.**
{% endhint %}

## [react-native-webview-bootpay](https://github.com/bootpay/react-native-webview-bootpay)의 onMessage 사용

```swift
//window.ReactNativeWebView.postMessage("test data") //기존 웹뷰 
window.BootpayRNWebView.postMessage("test data"); //부트페이 웹뷰 
```

사용 방법은 react-native-webview와 동일하며 javascript interface 이름이 `ReactNativeWebView` 가 아닌 `BootpayRNWebView` 값으로 보내주시면 되겠습니다.

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
