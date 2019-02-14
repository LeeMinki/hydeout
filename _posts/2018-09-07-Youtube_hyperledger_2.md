---
layout: post
title: "박승철의 블록체인 제 2강 하이퍼레저 패브릭 구조"
excerpt_separator:  <!--more-->
categories:
 - Blockchain
tags:
  - 블록체인
  - 하이퍼레저
  - 하이퍼레저 패브릭
  - YouTube
  - 박승철의 블록체인 및 정보보호론 강의
---

# 출처

## YouTube, <https://www.youtube.com/watch?v=rrQp-ncNFm4>

---

<!--more-->

# 하이퍼레저 패브릭 구조

## 구성

* 레저(ledger) + 전체 상태(world state)

![image](https://user-images.githubusercontent.com/28076542/45217911-cad25300-b2e0-11e8-90fc-910afa08198a.png)

* 블록에는 거래정보가 들어감
* 블록들은 연결됨
* 체인코드를 실행한 결과는 저장이 되어야 하는데 이더리움은 State Tree에 저장하는데
* 하이퍼레저는 World State(전체 상태 자료구조)에 저장함
* 상태 정보를 유지하는 방식은 일반적임
* 이더리움은 계좌정보를 저장, 하이퍼레저 패브릭은 어떤 정보라도 버전별로 저장함
* Key-Value 방식으로 저장하며 같은 Key에 대하여 값이 계속 달라지므로 버전 방식으로 관리함

## 전체 상태(world state)

* 거래 실행 결과에 따라 변경되는 블록체인의 상태 변화 정보를 저장
* 버전형 키-값 저장소(vKVS-versioned Key-Value Store) 형태로 모델링
* 키(Key): 체인코드(chaincode)가 사용하는 정보 이름
* 값(value): 이름에 대응되는 정보
* 버전(version)은 특정 키, 값 쌍의 상태를 번호(number)로 표시
* 값이 갱신될 때마다 새로운 버전 번호(version number)가 부여되어 기존의 키-값 쌍의 상태 구분

## 전체 상태의 예

### 상태 변화 예

### (car.no = 1234, car.owner = park, car.maker = Hyundai)

### --> (car.no = 1234, car.owner = kim, car.maker = Hyundai)

* 같은 차를 다른 사람에게 판 상황
* 상태 정보가 변경된 것

### 키 버전 변화

### (s(car.owner.value) = park, s(car.owner.version) = 5)

### --> (s(car.owner.value) = kim, s(car.owner.version) = 6)

* 이 때 차에 대한 버전을 5에서 6으로 변화
* 변경되는 상태 정보를 유지할 수 있게 함
* (key, version, value) 형태로 만든 것

### 전체 상태 = 블록체인의 모든 거래가 접근하는 키 집합에 대한 현재의 값과 버전 정보 집합

## 레저(ledger)

* 시스템 운영 과정에서 발생하는 모든 거래 정보를 해시체인(hash chain) 형태로 저장
* 블록에 포함되는 거래의 수는 응용의 요구사항에 따라 달라질 수 있음
* 레저를 통해 전체 상태 변경의 이력 추적 가능

---

## 거래 처리 방식 비교

![image](https://user-images.githubusercontent.com/28076542/45218833-e12dde00-b2e3-11e8-8719-4b200202e399.png)

### 기존 블록체인 거래 처리 방식

* 거래들이 발생하면 채굴자들이 블록의 순서를 정하고 블록을 저장
* 순서가 정해진 후 블록을 받은 채굴자나 노드들이 블록의 내용을 검증
* 즉, 블록의 순서를 결정하고 검증
* 모든 노드들의 블록 순서가 같고 스마트 계약도 deterministic code로 작성되어 결과가 같음을 보장
* 따라서 블록은 순서대로 검증이 되며 이는 성능을 떨어뜨리는 요인이 됨
* 순서를 정하고 거래를 실행하면 블록체인을 유지하는 모든 시스템이 블록에 있는 거래가 적합한 거래인지 확인해야 함
* 모든 블록체인 피어가 다 실행을 하게 됨

### 하이퍼레저 패브릭 거래 처리 방식

* 우선 실행을 함
* 실행된 결과가 포함된 거래를 배포
* 블록에 저장될 거래의 순서를 매김 --> 합의(Consensus) 노드들이 함
* 순서가 정해지면 블록체인을 유지하는 피어들이 검증
* 검증에 문제가 없으면 확정을 해서 블록체인에 기록, 확정(commit)
* 실행은 지정된 피어가 함 --> **endorsing peer**

## 기존 블록체인 시스템

* 대부분 발생한 거래들을 합의 알고리즘을 통해 순서화시킨 다음 피어(풀 노드)들에게 전달
* 블록체인을 유지하는 피어에 의해 독립적으로 처리
* 거래들은 순서가 매겨진대로 순차적으로 실행
* 결과는 동일하게 유지되도록 하기 위해 스마트 계약 프로그램은 반드시 결정적으로(deterministically) 작성

---

## 하이퍼레저 패브릭의 거래 처리 순서

![image](https://user-images.githubusercontent.com/28076542/45219920-6a92df80-b2e7-11e8-92ef-f9d3feee06b8.png)

* CC = ChainCode
* 체인코드의 실행 결과는 몇몇의 endorser에게 의뢰하고 그 결과를 받음
* 실행결과와 거래 정보를 Order Tx에서 합의함
* Ordering service는 다른 블록체인에서는 Consensus 프로토콜이라 함
* 순서가 결정되면 committer에게 보내며 문제가 없는지 검증함
* 검증이 완료되면 Ledger에 저장

---

* **예를 들어**
* 복잡한 계약을 블록체인에 올렸다치면
* 이 거래를 누군가가 요청하면 누가 실행하게 할 것인가
* 이는 개발자들이 실행시킬 노드를 따로 정하면 됨

---

* 비결정적 프로그램(non-deterministic program) 지원, 거래의 병렬 처리 등을 위해 거래 처리 구조를 변경
* 실행 --> 순서화 --> 검증 및 확정의 단계로 처리
* 클라이언트는 해당 체인코드의 보증 정책에 맞는 보증 노드에게 거래를 송신하고 거래의 실행은 보증 노드에 의해 수행
* 보증 노드는 거래 실행 결과를 별도의 자료구조(readset, writeset)에 담아 클라이언트에게 회신
* 보증 노드는 거래 실행 결과를 블록체인에 미적용
* 실행을 한 곳에서 실행하게 한다면 결과는 항상 동일할 것
* 예를 들어 자바를 한 노드에게 실행시키고 그 결과를 모든 노드들이 공유하면
* 결과는 항상 같음 왜냐하면 한 노드에서 실행했기 때문
* 따라서 솔리디티처럼 deterministic 언어를 만들지 않아도 결과를 같게 할 수 있음
* 만약 여러 endorser가 있으면 결과가 달라질 수 있음
* 이 때는 어떤 값을 쓸지 Client가 정해야 함
* 이럴 땐 정책으로 정해야 함
* endorser가 client에게 실행결과를 알려주는 자료구조는 **readset, writeset**

---

### readset

* 거래를 처리하기 위해 어떤 정보(key, version)를 읽었는가
* 읽은 정보에서 중요한 것은 정보의 **version**

### writeset

* 처리 결과 정보
* 이게 어떻게 사용되는가는 나중에

---

* 실행 결과를 수신한 클라이언트는 순서화 서비스 채널을 통해 거래 결과를 전송
* 순서화 서비스를 수행하는 노드들은 적용되는 합의 알고리즘에 따라 그 동안 발생한 거래들을 순서화
* 순서화된 거래들은 블록에 담겨 채널에 연결된 모든 피어들에게 안전하게 전달
* 거래 결과를 수신한 피어들은 거래 결과가 보증 정책에 맞게 만들어졌는지 검증하고 적합하게 실행된 거래의 결과를 블록체인에 저장함으로써 해당 거래를 확정

## 멤버십 서비스 제공자 구조

### 책임성(accountability) 보장

* 누가 무슨 짓을 했는지 추적할 수 있어야 함
* 문제가 생기면 책임소재를 분명하게 가릴 수 있어야 함
* 모든 참가자의 행위에 대해 정당한 책임 부여
* 참가자의 행위 추적
* 참가자가 자신의 행위 부인 방지
* 다른 참가자의 행위에 대해 부당하게 책임지지 않을 것을 보장
* 제 3자를 통한 참가자의 행위 감사(audit) 보장

### 프라이버시(privacy) 보장

* 책임성 보장과 약간 상충됨
* 본인이 무슨 일을 할 때마다 신원이 노출되기 때문
* 가능한 한 거래를 할 때 본인의 신원 정보는 노출되지 않도록 해야 함
* 거래 익명셩(transanction anonymity) 보장
* 거래 비연결성(transaction unlinkability) 보장

### 하이퍼레저 패브릭의 PKI 구조

![image](https://user-images.githubusercontent.com/28076542/45228114-3d512c00-b2fd-11e8-9c8b-3696e9ed42b8.png)

* 기본적으로 멤버십 서비스를 제공할 때 인증기관(CA, Certification Authority)를 활용
* CA를 통해 사용자의 신원을 확인, 즉, 공개키를 증명함
* 결론은 사용자의 공개키가 맞다는 것을 인증함

#### ECA(Enrollment Certification Authority)

* 하이퍼레저 패브릭에 등록된 사용자의 공개키가 맞다는 것을 증명해 줌
* 공인인증서와 유사
* 블록체인에 접근하고자 하는 참가자의 신원을 검증한 후에 등록 인증서(ECert - Enrollment Certificate)를 발급하는 역할을 수행
* 등록된 참가자를 확인하는데 필요한 공개키와 개인키 쌍의 생성을 포함
* 참가자 등록을 위한 신원 확인은 등록기관(RA - Registration Authority)에 위임 가능
* 신원 확인 방법과 확인 결과의 전달 방법은 응용의 요구사항에 따라 달라질 수 있음

#### TCA(Transaction Certification Authority)

* 하이퍼레저 패브릭 네트워크에서 거래를 할 때
* 동일한 인증서를 계속 쓰면(예를 들어 ECA) 누가 언제 어떤 거래를 하는지 다 알게 되어 프라이버시 보장 못하게 됨
* 즉, ECA를 가진 참여자는 거래를 할 때마다 다른 TCA를 사용
* 따라서 ECA는 하나만 필요하고 TCA는 여러 개가 있을 수 있음
* ECA와 TCA는 상관관계를 추론할 수 없도록 만듦
* ECA에 의해 발급된 등록 인증서에 근거하여 신원이 확인된 참가자에게 거래 인증서(TCert - Transaction Certificate)를 발급하는 역할
* 등록 인증서(ECert)를 기반으로 충분한 수의 거래 인증서(TCert)를 발급
* 등록 인증서를 유추할 수 있는 어떤 정보도 포함하지 않음으로써 참가자의 신원이 노출되지 않도록 보장
* 각 거래 인증서는 동일 등록 인증서를 통해 발급된 다른 거래 인증서와의 연관성 정보를 포함하지 않음으로써 거래 비연결성 보장

#### TLS_CA(TLS Certification Authority)

* TLS: HTTPS를 가능하게 만드는 프로토콜
* TLS에서 상대방을 인증할 때 사용하는 인증서를 발행하는게 TLS_CA
* 동일한 인증서로 계속 사용할 수 있음
* 신원이 확인된 참가자를 대상으로 보안 통신 프로토콜인 TLS 프로토콜이 사용할 수 있는 인증서를 발급
* 네트워크 연결을 설정하는 등록된 참가자를 확인하는데 필요한 공개키와 개인키 쌍의 생성 포함

#### 하이퍼레저 패브릭과 블록체인

* 블록체인은 기본적으로 중앙 서버가 없는 P2P 방식
* 하이퍼레저 패브릭의 MSP는 다른 블록체인과 다르게 중앙에 Security Certification Authority를 발행해 신원을 확인할 수 있음
* 공인인증서 체계와 상당히 유사함
* 현재 공인인증서를 폐지하고 P2P 방식의 핀테크를 가려는 목소리가 많은데 블록체인과 이전의 방식을 결합한 형태라 할 수 있음

---

## 사용자와 클라이언트에 대한 멤버십 서비스 제공 과정

![image](https://user-images.githubusercontent.com/28076542/45231247-65915880-b306-11e8-9498-c236b50e7c3a.png)

* 등록 인증기관에서 참여 인증을 받은 후 거래 마다 거래 인증서를 받아야 함
* 거래 인증서는 신원 정보가 절대 노출되지 않도록 인증서 발행
* 등록 인증서와 거래 인증서는 연관이 되어 있음을 증명해야 함(수학적으로)
* 거래 인증서에 있는 공개키를 갖고 사용자의 거래의 서명을 검증하는 것
* 서명얼 검증하는 공개키는 등록 인증서의 공개키와 연계가 되어있게 키를 만듦
* 따라서 사용자의 신원 정보는 노출되지 않으면서 등록 인증서와 거래 인증서를 연관시킨 것

### 기존의 중앙집중 시스템을 분산화한 것

* 장부를 여러 곳에 퍼뜨린 것
* 다른 블록체인과는 많이 다름
* MSP는 신원 확인 즉 실명 확인
* 특정 비공개 서버를 유지하지 않고 블록체인으로 유지하게 할 때 블록체인을 사용