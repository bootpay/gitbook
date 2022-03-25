---
description: 결제수단별 response data 값
---

# Response Data

## 카드결제&#x20;

| Response Data  | 타입     | 설명                                 |
| -------------- | ------ | ---------------------------------- |
| card\_name     | string | 카드사명                               |
| card\_no       | string | 카드번호 (중간 자리수는 0000 으로 가려서 전달됩니다)   |
| card\_quota    | string | 할부 개월수   **\* 00 - 일시불, 03 - 3개월** |
| card\_code     | string | PG사에서 정의한 카드사 코드                   |
| card\_auth\_no | string | 카드 승인 번호                           |

## 실시간 계좌이체

| Response Data | 타입     | 설명                        |
| ------------- | ------ | ------------------------- |
| bankname      | string | 계좌이체 은행                   |
| cash\_result  | string | 현금영수증 번호 (없으면 현금영수증 발행 X) |

## 가상계좌

| Response Data | 타입     | 설명                   |
| ------------- | ------ | -------------------- |
| bankname      | string | 입금할 은행명              |
| accountholder | string | 예금주                  |
| account       | string | 입금할 계좌번호             |
| expiredate    | string | 입금만료시간               |
| username      | string | 입금자명 (일부 Pg는 전달이 안됨) |
| cash\_result  | string | 현금영수증 번호             |

## 기술문의&#x20;

이 섹션에 대해 궁금하신 부분은 [채팅](https://bootpay.channel.io)으로 문의주시면 감사하겠습니다.&#x20;
