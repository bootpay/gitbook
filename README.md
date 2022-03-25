---
coverY: 0
---

# 부트페이 개발매뉴얼

## 목차&#x20;



#### 시작하기에 앞서&#x20;

* [연동 전 설정하기 ](master.md)
* [결제 순서도 ](flow.md)

#### 결제 연동하기&#x20;

* [요청 파라미터](client/payload.md)
* [일반결제](client/pg/)&#x20;
* [통합결제](client/total.md)
* [정기결제 ](client/subscription/)
* [정기결제 (비밀번호)](client/subscription\_password.md)&#x20;
* [정기결제 (생체인증)](client/subscription\_bio.md)
* [본인인증](client/auth.md)&#x20;
* [통계연동](client/analytics/)

#### 결과 통지받기&#x20;

* [서버로 통지 받기 (Webhook)](webhook/server.md)

#### 서버 연동하기&#x20;

* [Response 응답처리 (공통)](server/response.md)
* [토큰 발급받기 (필수)](server/token.md)
* [결제 검증하기](server/verify.md)&#x20;
* [결제 취소하기](server/cancel.md)&#x20;
* [결제 링크 생성하기](server/link.md)&#x20;

#### 더 깊게 다루기&#x20;

* [가상계좌 ](advance/vbank.md)
* [팝업 결제 진행 ](advance/popup.md)
* [서버에서 결제승인](advance/submit.md)&#x20;
* [PG 오류 데이터 요청하기 ](advance/pg.md)
* [결제오류 확인하기 ](advance/error-check.md)

#### 데이터 코드표&#x20;

* [결제상태값 ](code/status.md)
* [Response Data](code/response-data.md)
