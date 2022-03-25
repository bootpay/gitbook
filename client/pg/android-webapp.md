# Android (웹앱)

Web에 구현된 서비스가 있고, 안드로이드에서는 Webview로 구현된 Web에 링크할때 이 페이지를 참조하시면 됩니다.

금융감독원에서는 온라인 신용카드 결제시 안심클릭 또는 ISP 안전결제를 권고하며, 이에 따라 카드사의 앱카드 앱이 [Intent Call 방식](https://developer.android.com/training/basics/intents?hl=ko)으로 호출됩니다. 그러므로 Android에서 외부앱 호출 및 통신을 위한 처리를 해주셔야 하는데, 이 라이브러를 사용하시면 쉽게 해결됩니다. ​ ​

PG 결제창은 기본적으로 Javascript로 연동됩니다. 부트페이 Android SDK는 내부적으로 Webview 방식으로 구현하였으며, 사용방법은 아래와 같습니다.

## Bootpay 설치하기

{% tabs %}
{% tab title="Android" %}
bootpay 모듈은 [jipack](https://jitpack.io)을 통해 배포되었습니다. [bootpay github](https://github.com/bootpay/android)은 [이곳](https://github.com/bootpay/android)을 참조하세요.

#### Gradle을 통한 설치

```groovy
//build.gradle (project)
buildscript {
    repositories {
        ...
        jcenter()
    }
    dependencies {
        ...
        classpath 'com.github.dcendents:android-maven-gradle-plugin:2.1' // 비공식 해결 방법, gradle build error 가 발생시에만 추가
 
    }
}

allprojects {
    repositories {
        ...
        jcenter()
        maven {
            url "https://jitpack.io"
        }
    }
}
```

```groovy
//build.gradle (module)

android {
    compileSdkVersion 30 //Android 11 지원을 위한 30 이상 버전을 추천 
    buildToolsVersion "30.0.3"

    defaultConfig {
        ...
        minSdkVersion 16
        targetSdkVersion 30 //Android 11 지원을 위한 30 이상 버전을 추천 
    }
    }

dependencies {
    ...
    implementation 'com.github.bootpay:android:+' //최신 버전 추천
}
```

#### AndroidManifest.xml 수정

```markup
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```

{% hint style="info" %}
실행시 Cleartext HTTP traffic to Your.Domain not permitted 와 같은 에러가 나온 경우

Android [네트워크 보안 구성](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted)에 따르면 안드로이드 9(API28)부터 cleartext traffic을 기본적으로를 비활성화한다고 합니다. 따라서 API 28 이후에서 Http에 접근하려면 cleartext traffic을 활성화 시켜야 합니다.

```markup
<application
        android:label="@string/app_name"
        ...
        android:usesCleartextTraffic="true">
```
{% endhint %}
{% endtab %}
{% endtabs %}

## Webview 대신 BootpayWebView를 사용하면 끝

{% tabs %}
{% tab title="MainActivity.java" %}
{% code title="" %}
```java
//https://github.com/bootpay/android/blob/main/app/src/main/java/kr/co/bootpay/android/WebAppActivity.java

package kr.co.bootpay.android;

import android.os.Bundle;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import kr.co.bootpay.core.webview.BootpayWebView;

public class WebAppActivity extends AppCompatActivity {
    BootpayWebView bootpayWebView;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_webapp);

        bootpayWebView = findViewById(R.id.webview);
        bootpayWebView.loadUrl("https://www.yourdomain.com");
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="activity_main.xml" %}
```markup
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp" >

    <kr.co.bootpay.core.webview.BootpayWebView
        android:id="@+id/webview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        />
</LinearLayout>
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

테스트로 결제를 하시더라도 실제 결제가 될 수 있습니다. 자동취소가 누락되어 취소되지 않을 수 있으니 테스트 결제는 소액으로 진행해주세요.

가상계좌의 경우 계좌간 계좌이체 방식이기 때문에 이체간 비용이 발생하기에 PG사에서 정책적으로 가상계좌는 테스트 시 결제취소 기능을 제공하지 않습니다. 가상계좌를 테스트 하기 위해서는 PG사 가맹 후 발급받은 코드로 진행하시길 추천합니다.
{% endhint %}

## 기술문의

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.
