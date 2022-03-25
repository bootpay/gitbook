# Table of contents

* [부트페이 개발매뉴얼](README.md)
* [결제 순서도](flow.md)
* [연동 전 설정하기](master.md)

## 결제 연동하기 <a href="#client" id="client"></a>

* [요청 파라미터](client/payload.md)
* [일반결제](client/pg/README.md)
  * [Web](client/pg/web.md)
  * [Android](client/pg/android.md)
  * [Android (웹앱)](client/pg/android-webapp.md)
  * [iOS](client/pg/ios.md)
  * [iOS (웹앱)](client/pg/ios-webapp.md)
  * [Flutter](client/pg/flutter.md)
  * [Flutter (웹앱)](client/pg/flutter-webapp.md)
  * [Flutter (Web)](client/pg/flutter-web.md)
  * [React Native](client/pg/react-native.md)
  * [React Native (웹앱)](client/pg/react-native-webapp.md)
  * [Unity](client/pg/unity.md)
* [통합결제](client/total.md)
* [정기결제](client/subscription/README.md)
  * [1-1. 빌링키 발급받기 (인증)](client/subscription/undefined.md)
  * [1-2. 빌링키 발급받기 (비인증)](client/subscription/1-2..md)
  * [2. 빌링키로 결제 요청하기](client/subscription/undefined-1.md)
  * [3. 빌링키로 결제 예약하기](client/subscription/undefined-2.md)
  * [4-1. 결제 취소하기](client/subscription/4-1..md)
  * [4-2. 예약된 결제 취소하기](client/subscription/undefined-3.md)
  * [5. 빌링키 취소하기](client/subscription/undefined-4.md)
* [정기결제 (비밀번호)](client/subscription\_password.md)
* [정기결제 (생체인증)](client/subscription\_bio.md)
* [본인인증](client/auth.md)
* [통계](client/analytics/README.md)
  * [연동 예제](client/analytics/example.md)

## 결과 통지받기 <a href="#webhook" id="webhook"></a>

* [서버로 통지 받기 (Webhook)](webhook/server.md)

## 서버 연동하기 <a href="#server" id="server"></a>

* [Response 응답처리 (공통)](server/response.md)
* [토큰 발급받기 (필수)](server/token.md)
* [결제 검증하기](server/verify.md)
* [결제 취소하기](server/cancel.md)
* [결제 링크 생성하기](server/link.md)

## 더 깊게 다루기 <a href="#advance" id="advance"></a>

* [가상계좌](advance/vbank.md)
* [팝업 결제 진행](advance/popup.md)
* [서버에서 결제승인](advance/submit.md)
* [PG 오류 데이터 요청하기](advance/pg.md)
* [결제오류 확인하기](advance/error-check.md)

## 데이터 코드표 <a href="#code" id="code"></a>

* [결제상태값](code/status.md)
* [Response Data](code/response-data.md)
