# Joint Strike Fighter Air Vehicle C++ Coding Standards 학습 가이드

> **정식 명칭**: *JOINT STRIKE FIGHTER AIR VEHICLE C++ CODING STANDARDS FOR THE SYSTEM DEVELOPMENT AND DEMONSTRATION PROGRAM*
> **문서 번호**: 2RDU00001 Rev C (December 2005) — Lockheed Martin Aeronautics Company
> **배포 등급**: DISTRIBUTION STATEMENT A — 일반 공개, 배포 무제한
> **원문 PDF**: https://www.stroustrup.com/JSF-AV-rules.pdf
>
> 본 가이드는 위 공개 원문에서 **직접 발췌·검증한 규칙 번호/내용**을 기반으로 작성했습니다. 규칙 번호는 원문 표기를 그대로 사용합니다.

이 문서는 **혼자서 공부하기 위한 참고 자료**입니다. 순서대로 읽고, 각 장 끝의 "확인 문제"로 자기 점검을 하세요.

---

## 목차

1. [역사](#1-역사)
2. [관련 근거 문서의 버전별 발전 방향](#2-관련-근거-문서의-버전별-발전-방향)
3. [검증 가능한 하드웨어 및 타겟보드 조사](#3-검증-가능한-하드웨어-및-타겟보드-조사)
4. [문법 기본 → 심화 (JSF C++ 규칙 체계)](#4-문법-기본--심화)
5. [표준 C++와의 다른 점](#5-표준-c와의-다른-점)
6. [부록](#부록)

---

## 1. 역사

### 1.1 왜 이런 표준이 만들어졌나

F-35 라이트닝 II(개발 당시 *Joint Strike Fighter* / JSF)는 미군 3군(공군·해군·해병대)이 공용으로 쓰는 무인전투기급의 스텔스 전투기입니다. 이 기체의 **System Development and Demonstration(SDD)** 단계에서 **록히드 마틴 항공 사업부(Lockheed Martin Aeronautics)** 는 기체용 운용 비행 프로그램(Operational Flight Program)을 개발하면서, **안전-critical 항공 소프트웨어에 최초로 C++를 대규모로 적용**해야 했습니다.

원문 서문(Introduction)은 이 표준의 목적을 이렇게 명시합니다:

> "The intent of this document is to provide direction and guidance to C++ programmers that will enable them to employ good programming style and proven programming practices leading to **safe, reliable, testable, and maintainable** code. Consequently, the rules contained in this document are **required for Air Vehicle C++ development** and recommended for non-Air Vehicle C++ development."

즉 JSF C++ 표준(줄여서 **JSF++**라고도 부름)은 "멋진 C++ 코드"가 아니라, **안전성(신뢰성)·검증 가능성·결정성(determinism)** 을 최우선으로 한, 극도로 제한된 C++ 사용 규칙 모음입니다.

### 1.2 요구 품질 (원문 Section 3 General Design)

표준은 코드가 갖춰야 할 품질을 나열합니다:

| 품질 | 의미 |
|---|---|
| **Reliability** (신뢰성) | 모든 요구사항을 예측 가능한 방식으로 일관되게 충족 |
| **Portability** (이식성) | 컴파일러/링커에 의존하지 않는 소스 |
| **Maintainability** (유지보수성) | 일관·가독·단순·디버그 쉬운 코드 |
| **Testability** (시험성) | 코드 크기·복잡도·정적 경로 수 최소화 |
| **Reusability** (재사용성) | 컴포넌트 재사용 권장 |
| **Extensibility** (확장성) | 요구 변화를 전면 수정 없이 국소 확장으로 대응 |
| **Readability** (가독성) | 읽고 이해하기 쉬운 코드 |

### 1.3 문서의 탄생과 공개

- 작성자: **Lockheed Martin Corporation** (저작권 © 2005)
- 개정: Rev B(원판, 2005-10) → Rev C(개정본, 2005-11/2005-12 문서 발행)
- 공개 배포(2005) 근거: `Distribution Statement A`, 즉 **미 국방부 승인하에 일반에게 완전 공개**된 문서라서 지금까지 회사/교육/도구 공급업체에서 널리 인용되고 있습니다.
- 원문 파일은 C++ 창시자 **비야네 스트라우스트럽(Bjarne Stroustrup)** 의 공식 웹사이트(`stroustrup.com`)에 호스팅되어 있습니다(표준의 참고문헌에도 그의 저서가 다수 인용됨).

### 1.4 언어 선택 배경 (Ada → C++)

F-35 이전의 군용 항공 소프트웨어는 **Ada**(특히 Ada 83/95)가 사실상 표준이었습니다(예: F-22, F-16 계열. 무기체계에도 AdaMULTI 등 Ada 도구체계 사용). JSF는 미션 시스템 일부와 디스플레이·지원 시스템에 **C++를 도입**했는데, 그 이유는 데이터 추상화·객체지향·제네릭 프로그래밍(C++의 강점)을 유지하면서 C와의 호환성도 보장되기 때문이었습니다(원문 1장). 그러나 안전-critical 환경에서 C++ 언어 기능을 **무제한으로 쓰기엔 위험**했고, 그래서 이 표준이 탄생했습니다.

> 핵심 개념: JSF++는 "위험한 부분만 제거한 C++의 **안전한 서브셋(subset)** + 이탈 시 승인받는 **프로세스**"의 결합입니다. 원문은 이를 "programs are written in a 'safer' subset of a superset(수퍼셋의 더 안전한 서브셋)"이라고 표현합니다.

### 1.5 표준의 계보와 영향(영향력)

JSF++는 이후 안전-critical C++ 표준의 직간접적 기원이 되었습니다:

```
MISRA C (1998, 자동차)
   └─► "Vehicle Systems Safety Critical Coding Standards for C" (Lockheed 사내, MISRA C 확장)
          └─► JSF AV C++ Coding Standards (2005)  ◄── 본 문서
                 ├─► MISRA C++:2008 (자동차·연안·의료 등 안전-critical C++)
                 │      └─► AUTOSAR C++14 Guidelines (R17-10, 2017, 154/342조항이 MISRA C++2008 차용)
                 ├─► NASA·ESA 등 무기체계/우주 사업 표준에 영향
                 └─► Polyspace / Klocwork 등 정적 분석 도구의 검사 규칙으로 구현
```

그래서 **"JSF++를 알면 MISRA C++:2008, AUTOSAR C++14의 룰을 이해하는 지름길"**이 됩니다.

### 1.6 연대표 요약

| 시기 | 사건 |
|---|---|
| 1992 | 스웨덴 Ellemtel, *Programming in C++ Rules and Recommendations* 발간(원문 참고문헌 14) |
| 1996–2001 | JSF 콘셉트 개발/CE&C, 캘리포니아 그룹 경쟁 → 2001-10-26 록히드 마틴 SDD 수주 |
| 1998 | MISRA C 첫판(참고문헌 8) |
| 2005-10 | JSF AV C++ 표준 **Rev B(원판)** |
| 2005-12 | **Rev C** 최종 공개 (change log 도입·오타 수정·룰 159/32 명확화) |
| 2006-12 | F-35 최초 비행 (표준 모델 기준 소프트웨어로 개발) |
| 2007-06 | 외부 문건에 따름 **Rev D** 존재(공식 배포는 Rev C가 일반적) |
| 2008 | **MISRA C++:2008** 발간 — JSF++ 룰 대량 계승 |
| 2017 | **AUTOSAR C++14 Guidelines** 발간 — MISRA C++2008의 후속/현대화 |
| 2020년대 | F-35 TR-3(Technology Refresh 3) 하드웨어 개량 → 현대 RTOS/개방형 아키텍처로 이행 |

---

## 2. 관련 근거 문서의 버전별 발전 방향

### 2.1 공식 문서 개정 이력 (원문 Table 1. Change Log)

| Revision | 문서일 | 권한자 | 영향 | 비고 |
|---|---|---|---|---|
| 0001 **Rev B** | Oct 2005 | K. Carroll | 전체 | **Original(원판)** — 표준의 실질적 첫 배포판 |
| 0001 **Rev C** | Nov 2005 | K. Carroll | 1장 3절, Rule 52/76/91/93/129/167/218, 부록 A Rule 3, Table 2 | **Change log 추가**, 오타 수정 |
| Rev C 추가 | — | — | Rule 159 | "**unary &**"(단항 &)만 금지임을 명확화(이항 &는 예외 아님) |
| Rev C 추가 | — | — | Rule 32 | 템플릿 클래스/함수의 헤더–구현 분할 예외 명확화 |
| (별도 배포) | Jun 2007 | — | — | 일부 외부 문건이 **Rev D** 인용 — Lockheed 공식 온라인 배포는 Rev C가 표준 |

> **학습 포인트**: 규칙 번호가 개정을 거치며 **명확화(clarification)** 위주로만 바뀌었다는 점이 중요합니다. 즉 이 표준의 "정신"은 2005년 이후 변하지 않았고, 이후 진화는 **후속 표준(MISRA C++ 2008, AUTOSAR C++14) 쪽**에서 일어났습니다.

### 2.2 참고문헌(원문 Section 2)이 보여주는 발전 축

JSF++는 백지에서 나온 것이 아니라 아래 자료들의 "종합"입니다. 이것이 "관련 근거 문서의 발전 방향"의 핵심입니다:

| # | 참고 문서 | JSF++에 기여한 것 |
|---|---|---|
| 8 | **MISRA C (1998)** | C 언어 안전 서브셋, 대부분의 룰이 "(MISRA Rule n)"으로 직결됨 |
| — | **Vehicle Systems Safety Critical Coding Standards for C** | MISRA C 확장판(Lockheed 사내) — 항공 적용 |
| 14 | Ellemtel, *Programming in C++ Rules and Recommendations* (1992) | C++ 스타일/네이밍/클래스 규칙의 원류 |
| 2,3,4 | Bjarne Stroustrup — The C++ Programming Language (3rd ed.), Glossary, Style/FAQ | C++ 용어 정의·객체지향 설계 원칙(예: AV Rule 86→Stroustrup 25.2) |
| 6,7 | Scott Meyers — Effective C++ / More Effective C++ | 개별 룰 근거(예: AV Rule 78→가상 소멸자, AV Rule 91→is-a) |
| 10 | **ISO/IEC 14882:2003(E) — C++ 표준** | AV Rule 8: "all code shall conform to ISO/IEC 14882:2002(E)" 및 UB 인용 |
| 9 | ISO/IEC 10646-1 (UCS 문자셋) | 문자셋 규칙(AV Rule 10) |
| 1 | ANSI/IEEE 754 (부동소수점) | 부동소수점 규칙 기반 |
| 15 | **RTCA/DO-178B** | AV Rule 16: "DO-178B Level A certifiable 라이브러리만 사용" |
| 12,13 | JSF Mission Systems SDP, JSF System Safety Program Plan (2YZA00045-0002) | SEAL 1/2 안전 등급·defensive programming 요구 인용 |

### 2.3 발전 방향의 핵심 논리 (한눈에 보기)

1. **MISRA C(1998)**: C의 기능별 금지/제한 목록.
2. **Lockheed 사내 C 표준**: MISRA C를 항공 안전 요구로 "강화"(should→shall화 등).
3. **JSF++(2005)**: 여기에 **C++ 고유 기능(상속·템플릿·namespace·연산자 오버로딩·예외·RTTI)에 대한 안전 규칙**을 추가.
4. **MISRA C++:2008**: JSF++를 비롯한 여러 안전 표준을 모아 자동차·산업 필드의 **업계 표준**으로 정식화.
5. **AUTOSAR C++14 / MISRA C++ 2023**: C++03에서 C++14/17로 시대를 올리고, `AMS`(주소·접근 한정)·보안·동적 메모리 정책을 확장.

> **학습 포인트**: "JSF++→MISRA C++:2008"의 계승을 이해하면, MISRA C++의 룰 대부분이 **표준 C++에서 정의되지 않은 동작(UB)·구현 의존 동작을 없애는 것**임을 깨닫게 됩니다.

### 2.4 도구 생태계 (룰 자동화)

- **MathWorks Polyspace Bug Finder**: "JSF C++ rules" 검사 옵션(기반 JSF++:2005) 제공.
- **Klocwork**: `JSF.BREAK`, `JSF.EXCEPTION`, `JSF.INHERITANCE.NON_ABSTRACT`, `JSF.LABEL` 등 커뮤니티 체커 제공.
- **Green Hills MULTI** 등 상용 항공 컴파일러가 trigraph/digraph 해제 옵션 제공(`-no_alternative_tokens`) — 원문 AV Rule 11/12 Note에 명시.
- **연계 인증**: AV Rule 16이 요구하는 "DO-178B Level A certifiable 라이브러리" → Tool Qualification·라이브러리 인증서 발행 생태계 성장.

---

## 3. 검증 가능한 하드웨어 및 타겟보드 조사

> **중요한 사전 경고**
> F-35는 미 국방부 전투기입니다. **구체적인 보드 규격 · CPU 소켓 · 파티션 배치 · 인터커넥트 상세는 대부분 비공개/제한적**입니다. 아래 내용은 영문 공개 보도·방산 언론·공급업체 자료에서 **검증 가능한 정보만** 수집한 것입니다. 각 항목에 출처 근거를 표시했습니다.

### 3.1 시스템 관점: "F-35의 두뇌" = ICP

- **ICP(Integrated Core Processor)**: F-35 미션 시스템의 핵심 컴퓨팅. 데이터는 **통신·센서(레이다/EW)·유도조종·조종석·헬멧 디스플레이 처리**를 총괄.
- 초기(legacy) ICP는 **노스롭 그루먼(Northrop Grumman)** 이 개발.
- **TR-3(Technology Refresh 3)** 개량으로 ICP를 **L3Harris(구 해리스)가 재개발** — COTS(상용 기성품) 기술과 개방형 아키텍처로 전환.
  - 출처: Lynx Software Technologies 보도·케이스스터디(L3Harris ICP, LYNX MOSA.ic 채택), F-35 MSAR FY2027.

### 3.2 프로세서 아키텍처: PowerPC (Power Architecture)

- Green Hills Software 임원(D. Kleidermacher)의 2008년 인터뷰(*Avionics Today*):
> "INTEGRITY는 그 비행기에 '곳곳에' 있다. **PowerPC는 이 비행기의 유일한 application-level 프로세서**다."
>> ⇒ F-35 초기 세대의 애플리케이션 레벨 프로세서는 **Motorola/Freescale/NXP의 PowerPC 계열**로 일치.
- Green Hills 공식 고객 페이지: *"Avionics software developed by Lockheed Martin is running on INTEGRITY-178B in multiple airborne, Power Architecture-based systems"*(록히드의 F-35 항공전자 SW가 Power Architecture 시스템에서 INTEGRITY-178B 상에 구동).
- L-3(現 L3Harris) 디스플레이 엔지니어 경력 기재자료: F-35 PCD 소프트웨어를 **"Green Hills Software INTEGRITY-178, C와 C++로, NXP/Freescale PowerPC 아키텍처에서 (MULTI IDE 4.x)"** 로 개발. 같은 회사 라인의 다른 프로그램에서는 **MPC8245** PowerPC 및 MPC7447·7448 급 프로세서 사용 흔적.
- 표준 자체가 타겟 의존 금지를 요구하므로(AV Rule 209—210), **표준 코드는 특정 보드에 묶이지 않음**. 오히려 "정수 나눗셈의 부호" 같은 구현 의존 동작을 *Green Hills PowerPC C++ 컴파일러 기준*으로 문서화하라는 **AV Rule 167**에 해당 컴파일러가 직접 언급되어 있습니다(원문 Note):
> "For the Green Hills PowerPC C++ compiler, the sign of the remainder is the same as that of the first operand."

### 3.3 RTOS / 개발 환경

| 구성 요소 | 제품 | 비고 |
|---|---|---|
| RTOS | **Green Hills Software INTEGRITY-178B** | DO-178B **Level A** 인증, 시간·공간 파티셔닝(ARINC-653), MILS(다중 레벨 보안) |
| IDE/도구 | **AdaMULTI / MULTI IDE** | C·C++·Ada95, 디버거·성능분석 |
| 컴파일러 | Green Hills C/C++ 컴파일러 | 표준에 직접 언급(AV Rule 167 Note) |
| 검증 지원 | ISIM(RTOS 시뮬레이터), EventAnalyzer | **타겟 보드 없이 PC/워크스테이션에서 개발·테스트 가능** |
| 요구사항 추적 | IBM Rational DOORS 등 | 표준의 Document/Verification 요구 연계 |

- 초기 세대의 파트너 RTOS로 **DDC-I Deos**도 F-35 계열에서 병행 사용된 기록이 공급업체 문서(예: CoreAVI CoreSuite 2.0)에 보입니다.

### 3.4 주요 하위 시스템 사례 (검증 가능)

1.  **PCD — Panoramic Cockpit Display (파노라마 조종석 디스플레이)**
    - 개발: L-3 Communications Display Systems (現 L3Harris Display Systems)
    - RTOS: **INTEGRITY-178B**, 언어: **C/C++**, 프로세서: PowerPC, 도구: MULTI 4.0.5–4.2.x
    - 출처: Green Hills 2006-10-30 보도, L-3 엔지니어 경력기재, Avionics Today 2008.
2.  **TR-3 ICP (Mission Computer 개량)**
    - 개발: **L3Harris**, 프레임워크: **Lynx MOSA.ic**(LynxSecure 분리 커널 하이퍼바이저, LynxOS-178 게스트)
    - TR3 첫 시험 비행: **2023-01-06**. Intel·Arm·PowerPC 아키텍처 지원, DO-178C DAL A 하이퍼바이저 최초 인증 사례로 알려짐.
    - 출처: Lynx Software Technologies Press Release / Case Study.

### 3.5 "검증 가능한" 조사 표 (요약)

| 항목 | 초기/legacy | TR-3(현대화) | 출처 신뢰도 |
|---|---|---|---|
| ICP 개발사 | Northrop Grumman | L3Harris | 중 · 공개 보도 |
| CPU 아키텍처 | PowerPC (Power Architecture) | COTS 다중(Intel 세대 포함 후보) | 중 · Green Hills/Lynx 보도 |
| RTOS | INTEGRITY-178B | Lynx MOSA.ic (LynxSecure) | 높음 · 공식 보도 |
| 언어 | C, C++ (일부 Ada95) | C, C++ (개방형 스택) | 중 · L-3 자료 |
| 인증 | DO-178B Level A, JSF SEAL | DO-178C DAL A, CAST-32A(멀티코어) | 높음 · 공식 보도 |
| 특이사항 | "PowerPC는 기체 유일의 app-level 프로세서"(2008) | 개방형 아키텍처/오픈소스 컴포넌트 허용 | 중 |

### 3.6 표준과 하드웨어의 관계 (정리)

- JSF++ 표준은 **"타겟 자체보다는 타겟 위에서 도는 소프트웨어의 행동"을 규율**합니다.
- 의도된 실행 환경: **결정적(deterministic) 실시간, 파티셔닝된 멀티태스킹, 리소스 제한적인 임베디드** (정수÷나눗셈 부호를 문서화하라는 AV Rule 167, float→int 변환을 "하드웨어 인터페이스 필요 시에만" 허용하는 AV Rule 184가 이를 뒷받침).
- 초기화 후 **동적 메모리 금지(AV Rule 206)** 도 "파편화로 인한 비결정적 지연"을 막기 위한 것으로, 실시간 항공 하드웨어의 특성과 직결됩니다 → "왜"를 이해하는 데 결정적 단서.

### 3.7 실습 가능한 개발 환경 대안 (학습 목적)

실제 F-35 ICP 보드는 못 만지므로, JSF++의 **결정성·정적분석·서브셋**을 체험할 수 있는 대안:

1. **Green Hills MULTI + INTEGRITY 시뮬레이터(ISIM)**: 표준에서 이미 언급된 방식. PowerPC 에뮬레이션으로 보드 없이 학습.
2. **QEMU 기반 PowerPC 에뮬레이션** + Zephyr/FreeRTOS로 실시간 태스크 체험.
3. **정적 분석 도구**: Polyspace Bug Finder의 *Check JSF C++ rules*, Klocwork의 JSF 체커로 자기 코드 검증 → 표준 준수 감각 학습.
4. **호스트(Linux/g++ -std=c++03)** 위에서 JSF 서브셋만 쓰고, `-fno-exceptions -fno-rtti -fno-threadsafe-statics` 등으로 컴파일러 플래그까지 표준답게 재현.

---

## 4. 문법 기본 → 심화

> JSF++는 "문법"을 억지로 다르게 만드는 표준이 아니라, **C++ 표준 문법에서 사용 금지/제한을 규정 + 필수 검증 절차를 정의**한 표준입니다. 따라서 아래 학습은 **기본 → 심화 단계로 규칙을 체계화**한 것입니다. 규칙마다 `근거(Rationale)`를 함께 적었습니다. 학습 효율을 위해 모든 규칙을 다루진 않고, **자주 실무에 등장하고 인용되는 규칙**을 우선합니다.

### 4.0 먼저: 규칙 체계 읽는 법 (필수 선수 지식)

#### 4.0.1 세 가지 규칙 강도 (원문 4.2.1)

| 강도 | 뜻 | 검증 | 위반 시 |
|---|---|---|---|
| **should** | 강하게 권고(advisory) | 불요 | 소프트웨어 엔지니어링 리더 승인 필요(AV Rule 4) |
| **will** | 의도된 필수 요구. 주로 네이밍 등 비안전·비검증 항목 | 검증 불요 | 리더 + 제품 매니저 승인 필요(AV Rule 5) |
| **shall** | 필수 요구. 반드시 지키고 **검증** 필요(자동/수동) | 필수 | 리더 + 제품 매니저 승인 + **위반 파일 내 문서화**(AV Rule 6) |

#### 4.0.2 예외(Exception) 규칙

- 일부 규칙에는 예외가 명시됩니다. 예외에 해당하면 **승인 불요**(AV Rule 7).
- 규칙마다 "(MISRA Rule n)" 표기는 출처 규칙 매핑입니다.

#### 4.0.3 번호 체계

- 본문 규칙: **AV Rule 1 ~ AV Rule 221**(누락 번호는 다른 주제 섹션에 존재).
- 부록 A: 각 규칙의 상세 예제·근거. 부록 B: 준수 체크리스트.

#### 4.0.4 전체 섹션 지도 (원문 목차 기준)

```
1  Introduction
2  Referenced Documents
3  General Design (3.1 Coupling & Cohesion, 3.2 Code Size & Complexity)
4  C++ Coding Standards
   4.4 Environment(언어/문자셋/런타임체크)  4.5 Libraries
   4.6 Pre-Processing  4.7 Header  4.8 Implementation
   4.9 Style(네이밍)   4.10 Classes(라이프타임/상속/가상)
   4.11 Namespaces  4.12 Templates
   4.13 Functions    4.14 Comments   4.15 Declarations/Definitions
   4.16 Initialization  4.17 Types  4.18 Constants  4.19 Variables
   4.20 Unions & Bit Fields  4.21 Operators  4.22 Pointers & References
   4.23 Type Conversions  4.24 Flow Control  4.25 Expressions
   4.26 Memory Allocation  4.27 Fault Handling
   4.28 Portable Code  4.29 Efficiency  4.30 Miscellaneous
5  Testing (상속 계층 테스트)
Appendix A: 상세 예제·근거  Appendix B: Compliance
```

---

### 4.1 기초 문법 (환경 · 문자셋 · 크기 · 스타일 · 전처리기)

#### 4.1.1 언어와 문자셋

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 8** | 모든 코드는 **ISO/IEC 14882:2002(E) 표준 C++** 준수. 언어 확장/변형 금지 | 표준 C++로 정의되는 UB·구현 정의 동작 제거 |
| **AV Rule 9** | **기본 소스 문자셋(96자)** 만 사용: 공백·탭·CR·LF + 91개의 그래픽 문자 | 최소 필요 문자셋 |
| **AV Rule 11** | **Trigraph**(`??=`, `??/`, `??(` 등) 금지 | 가독성, 예: `"(\?\?-\?\?-\?\?\?\?)"`가 `"(~~?]"`처럼 해석됨 |
| **AV Rule 12** | **Digraph**(`<%`, `:>`, `%:%:` 등) 금지 | 가독성 |
| **AV Rule 13** | 멀티바이트 문자·와이드 문자열 리터럴 금지 | 구현 정의 동작 |
| **AV Rule 14** | 리터럴 접미사는 **대문자**(`64L`, `1.0F`) | `64l` ≠ 실수 641 유사 혼동 방지 |

#### 4.1.2 코드 크기·복잡도 (3.2절)

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 1** | 함수는 **200 L-SLOC** 이하 | 긴 함수는 복잡해 이해·시험 어려움 |
| **AV Rule 2** | **자가 수정 코드(self-modifying code)** 금지 | 에러 유발·가독성/시험 곤란 |
| **AV Rule 3** | 함수의 **사이클로매틱 복잡도 ≤ 20** (큰 switch는 예외) | `v(G) = e − n + 2` |

#### 4.1.3 스타일·네이밍

| 규칙 | 원문 요지 |
|---|---|
| **AV Rule 41** | 소스 한 줄 **120자 이하** |
| **AV Rule 42** | 표현식 문장은 **한 줄에 하나** |
| **AV Rule 43** | **탭 지양**(스페이스 권장) |
| **AV Rule 44** | 들여쓰기 **최소 2칸**, 파일 내 일관 |
| **AV Rule 45** | 식별자 내 단어는 **`_`로 구분**(예: `flight_data`) |
| **AV Rule 46** | 식별자 유효 의미 길이 **64자 이내**에 의존 금지 |
| **AV Rule 47** | 식별자가 **`_`로 시작 금지**(라이브러리명 충돌) |
| **AV Rule 61** | 중괄호 `{}`가 있는 줄엔 중괄호만(주석 제외) |
| **AV Rule 62** | 포인터/참조 연산자는 **타입과 붙임**: `int32* p;`(O), `int32 *p;`(X) |
| **AV Rule 63** | `.`/`->`/단항 연산자 주위 공백 금지 |

```cpp
// AV Rule 41/42/44/45/62 예시 (JSF 스타일)
int32 compute_fuel_usage(float32 rate, uint32 seconds_IP)   // 한 줄 120자 이내
{
    int32 total = 0;
    total = static_cast<int32>(rate * static_cast<float32>(seconds_IP));  // AV Rule 185
    return total;
}
```

#### 4.1.4 전처리기 · 헤더

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 26** | 전처리기 지시자는 **`#ifndef, #define, #endif, #include`만** | 전처리기 사용 최소화 |
| **AV Rule 29** | **inline 매크로 금지** → `inline` 함수 사용 | 타입 체크·일관적 호출 |
| **AV Rule 30** | **`#define` 상수 금지** → `const` 사용 | 스코프·타입 체크 |
| **AV Rule 32** | `#include`는 **헤더(`.h`)만**(템플릿 구현 분할 예외) | 명확성 |
| **AV Rule 33** | 헤더 include는 **`<filename.h>` 표기** | 벤더 간 일관성 |
| **AV Rule 35** | 모든 헤더는 **다중 포함 방지 장치** 포함 | AV Rule 27 메커니즘 사용 |
| **AV Rule 38** | 포인터/참조로만 쓰이는 클래스는 **forward header(선언 전방 위치)** | 컴파일 의존성 최소화 |
| **AV Rule 39** | 헤더에 **비-const 변수 정의/함수 정의 금지**(inline·템플릿 예외) | interface/implementation 분리 |

```cpp
// JSF 스타일 헤더 (AV Rule 27/28/30/35)
#ifndef telemetry_frame_h
#define telemetry_frame_h

class Tracker
{
public:
    Tracker();                     // 구조체가 아닌 클래스 → public data 금지(AV Rule 67)
    void reset();
    float32 get_rate() const;      // 상태를 안 바꾸면 const(AV Rule 69)

private:
    uint32 sample_count_;          // 단어는 _로 구분 (AV Rule 45)
};

#endif
```

#### 4.1.5 기초 학습 확인 문제

1. shall/will/should의 차이와 위반 시 절차를 서술하시오.
2. AV Rule 8이 표준 컴파일러의 확장 기능을 금지하는 이유는?
3. 헤더 가드를 만드는 표준 방법은? (AV Rule 27)

---

### 4.2 중급 문법 (클래스 · 객체 수명 · 상속 · 가상 함수)

#### 4.2.1 클래스 인터페이스와 접근

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 64** | 클래스 인터페이스는 **완전하고 최소화** | 클라이언트 편의 + 복잡도 최소화 |
| **AV Rule 65/66** | **불변식(invariant)이 없으면 `struct`**, 있으면 `class` | 접근 통제로 불변식 보호 |
| **AV Rule 67** | public/protected 데이터는 **struct에서만**(class는 private) | 클래스의 데이터 보호 |
| **AV Rule 68** | 쓰지 않을 **암시 생성 함수는 명시적으로 차단**(예: 복사·대입을 `private`에 미정의) | 컴파일러 암시 생성의 "놀람" 제거 |
| **AV Rule 69** | 객체 상태를 바꾸지 않으면 **member 함수는 const** | 예기치 않은 수정 방지 |
| **AV Rule 70** | friend는 private 접근이 **논리적/성능상 필요한 경우만** | friend 과용 방지 |

#### 4.2.2 객체 수명 · 생성자 · 소멸자

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 70.1** | 수명 시작 전/종료 후 객체 사용(비정의 동작) 금지 | UB 회피 |
| **AV Rule 71** | 외부에서 보이는 연산은 **초기화 완료 후에만** 호출 | 부분 초기화 문제 |
| **AV Rule 71.1** | **생성자/소멸자에서 가상 함수 호출 금지**(정적 바인딩됨) | 예상과 다른 동작 방지 |
| **AV Rule 73** | 불필요한 **기본 생성자 정의 금지** | 부분 초기화된 객체 방지 |
| **AV Rule 74** | 멤버 초기화는 **본문 대입이 아닌 초기화 목록(init list)** | 초기화 순서 보장 |
| **AV Rule 75** | 초기화 목록은 **선언 순서대로** | 실제 초기화 순서와 일치 |
| **AV Rule 76** | 포인터 데이터/비자명 소멸자 보유 클래스는 **복사 생성자+대입 연산자 선언** | 리소스 관리 |
| **AV Rule 77** | 복사 생성자는 **불변식에 영향 주는 모든 데이터 멤버/기반 복사** | 불변식 유지 |
| **AV Rule 78** | virtual 함수 있는 모든 기반 클래스는 **가상 소멸자** | base 포인터로 delete 시 UB 방지 |
| **AV Rule 79** | 클래스가 획득한 **리소스는 소멸자에서 모두 해제** | 리소스 누수 방지 |
| **AV Rule 81** | 대입 연산자는 **자기 대입(self-assignment) 정확 처리** (`a = a;`) | 클래식 미묘한 버그 |
| **AV Rule 82** | 대입 연산자는 **`*this`에 대한 참조 반환** | 문법 규약 일관성 |

```cpp
// AV Rule 74/75/78 (JSF 스타일 생성자/소멸자)
class Engine_controller : public Device_base   // base는 가상 소멸자 필요(AV 78)
{
public:
    Engine_controller(uint32 id, float32 max_thrust)
        : Device_base(id),    // 기반부터
          max_thrust_(max_thrust),   // 선언 순서대로
          running_(false)
    {
    }
    ~Engine_controller();     // 가상 소멸자

private:
    float32 max_thrust_;      // 선언 순서 == 초기화 순서
    bool    running_;
};
```

#### 4.2.3 상속 계층 (Inheritance)

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 86** | 단순 독립 개념은 **구체 타입(concrete type)** 사용 | 효율·독립성 |
| **AV Rule 87** | 계층은 **추상 클래스 기반** | 깨끗한 인터페이스·의존성 최소화 |
| **AV Rule 88** | **다중 상속은 제한형만**: 인터페이스 n개 + private 구현 m개 + protected 구현 최대 1개 | 계층 복잡도 통제 |
| **AV Rule 88.1** | **stateful virtual base** 접근 클래스는 명시 선언 | 가상 베이스 데이터의 배타성 오해 방지 |
| **AV Rule 89** | 같은 계층에서 base가 virtual과 non-virtual **혼용 금지** | 이해·사용 곤란 |
| **AV Rule 91** | public 상속 = **"is-a"** 관계에만 | 의미의 정확성 |
| **AV Rule 92** | 파생 메서드의 **전제조건은 약하게, 사후조건은 강하게** (리스코프 치환 원칙, LSP) | base 컨텍스트에서 파생의 예측 가능 동작 |
| **AV Rule 93** | "has-a"/"구현 in terms-of"는 **멤버 또는 비공개 상속** | 의미의 정확성 |
| **AV Rule 94** | **비가상 함수를 파생에서 재정의 금지** | 이중 얼굴(two-faced) 동작 방지 |
| **AV Rule 95** | 기반의 **기본 인자(default parameter) 재정의 금지** | 가상 함수+기본 인자의 함정 |
| **AV Rule 96** | **배열을 다형적으로 다루지 말 것**(`Base arr[]`에 Derived 대입 금지) | 포인터 산술과 다형성 비호환 |
| **AV Rule 97** | **인터페이스에 C 배열 사용 금지** → `Array` 컨테이너 사용 | 배열→포인터 붕괴(decay) 사고 방지 |

#### 4.2.4 가상 함수 보충

| 규칙 | 원문 요지 |
|---|---|
| **AV Rule 71.1** | 생성자/소멸자에서 가상 함수 호출 금지(위 참조) |
| **AV Rule 97.1** | 가상 멤버 함수 포인터에 `==`/`!=` 사용 금지(결과 비지정) |
| **AV Rule 221** | 가상 함수 계층의 **다형적 동적 디스패치 경로 다중 커버리지** 시험 |

#### 4.2.5 중급 학습 확인 문제

1. 왜 JSF++는 다중 상속을 아예 금지하지 않고 "제한형"만 허용하나? (AV Rule 88)
2. 생성자에서 가상 함수를 호출하면 무슨 일이 벌어지나? (AV Rule 71.1)
3. 스마트 포인터 없이 자원 관리가 필요한 이유(AV Rule 79)를 DO-178B 라이브러리 제약(AV Rule 16)과 연결하여 설명하시오.

---

### 4.3 심화 문법 (namespace · 템플릿 · 함수 규약 · 연산자 · 변환 · 흐름 · 메모리 · 예외 · 이식성 · 시험)

#### 4.3.1 Namespace와 Template

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 98** | `main()` 제외 모든 전역 이름은 **namespace 안에** | 대규모 이름 충돌 방지 |
| **AV Rule 99** | namespace **중첩 2단 이하** | 단순성·명확성 |
| **AV Rule 100** | 적은 이름(≈5)은 `using` 선언/명시적 자격, 많은 이름은 `using` 지시문 | 불필요한 네임스페이스 인입 방지 |
| **AV Rule 101** | 템플릿은 **①단독 리뷰(인자 가정) ②모든 실체화에 대한 리뷰** | 실체화 가짓수 폭발 대응 |
| **AV Rule 102** | 템플릿 시험은 **모든 실제 실체화 커버** | 실체화별 검증 |
| **AV Rule 103** | 템플릿 사용 시 **해당 타입 전용 특수화로 문제 해결 권장** | 제네릭 최적화 |

#### 4.3.2 함수 (Functions)

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 107** | 함수는 반드시 **파일 스코프에서 선언** | 블록 스코프 선언 괴담 제거 |
| **AV Rule 108** | **가변 인자 함수(`...`) 금지** | 타입 안전성 상실 |
| **AV Rule 110** | **인자 7개 초과 금지**(생성자 일부 예외) | 가독·유지보수 |
| **AV Rule 111** | **지역 객체의 포인터/참조를 반환 금지** | 수명 종료 후 접근(UB) |
| **AV Rule 112** | 반환값이 **자원 소유권을 모호하게 하지 말 것** | 자원 누수 |
| **AV Rule 113** | 함수는 **단일 출구(single exit point)** | 분석·이해 용이(구조가 헷갈리면 예외 인정) |
| **AV Rule 114** | 값을 반환하는 함수는 **`return`으로만 종료** | 끝까지 흘러내려가는 종료는 UB |
| **AV Rule 115** | 오류 정보를 반환하면 **그 값을 반드시 검사** | 오류 무시 → 잘못된 가정 지속 |
| **AV Rule 116** | 작은(2~3워드) 구체 타입은 **값 전달** 선호 | 단순·안전 |
| **AV Rule 117/117.1/117.2** | NULL 불가 → **`const T&` / `T&`** | NULL 체크 불필요 |
| **AV Rule 118/118.1/118.2** | NULL 가능 → **`const T*` / `T*`** | 참조는 NULL 표현 불가 |
| **AV Rule 119** | **재귀 금지**(SEAL 3·일반 SW 또는 리소스 증명 시 예외) | 스택 오버플로 방지 |
| **AV Rule 120** | 오버로드는 **같은 의미·목적의 패밀리**로만(인자로 구분) | 혼동 방지 |

```cpp
// AV Rule 117/118 인자 전달 선택 요약
void set_throttle(const Throttle_state& st);   // NULL 불가 + 수정 안 함 → const T&
void update_throttle(Throttle_state& st);      // NULL 불가 + 수정 → T&
void configure_device(const Device* d);        // NULL 가능 + 수정 안 함 → const T*
```

#### 4.3.3 연산자 · 포인터 · 변환

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 84** | 연산자 오버로딩은 **드물고 관습적으로만** | 자유 남용→혼동 |
| **AV Rule 85** | 반대 연산자(`==`/`!=`)는 **둘 다 정의하고 하나를 다른 것으로 정의** | 유지보수 단순화 |
| **AV Rule 157** | `&&`/`||`의 **오른쪽 피연산자에 부수효과 금지** | 단락 평가 특성 |
| **AV Rule 158** | 논리 연산 피연산자가 이항 연산자면 **괄호** | 가독성 |
| **AV Rule 159** | **`||`, `&&`, 단항 `&` 오버로딩 금지** | 단락 평가 불가·incomplete type의 UB |
| **AV Rule 160** | **대입은 표현식 문장으로만 사용**(조건 등에서 `=` 금지) | `=`/`==` 혼동 |
| **AV Rule 162** | **signed/unsigned 혼용 연산 금지** | 묵시적 변환 함정 |
| **AV Rule 163** | **unsigned 산술 금지** | 시간이 지나면 signed와 섞임(AV 162) |
| **AV Rule 164/164.1** | 시프트량은 타입 폭 범위 내 / **우시프트 좌항 음수 금지** | 비정의 동작 |
| **AV Rule 165** | unsigned에 **단항 마이너스 금지** | 이상한 래핑 |
| **AV Rule 166** | 부수효과 있는 표현식에 `sizeof` 금지 | 부수효과 미평가 |
| **AV Rule 167** | 정수 나눗셈 동작을 **컴파일러별로 문서화**(예: GHS PowerPC는 나머지 부호=첫 피연산자) | 구현 정의 제거 |
| **AV Rule 168** | **콤마 연산자 금지** | 가독성 |
| **AV Rule 169** | 포인터의 포인터** 지양 | 가독성 |
| **AV Rule 170** | **간접 2단 초과 금지** | 복잡도·버그 |
| **AV Rule 171** | 포인터 관계 연산자는 **같은 객체/배열** 내에서만 | 비지정 동작 |
| **AV Rule 173** | **자동 저장 객체 주소를 수명 종료 후 사용 금지** | UB |
| **AV Rule 174** | **NULL 역참조 금지** | UB |
| **AV Rule 175** | **NULL 대신 `0` 사용**(`nullptr` 없던 C++03 시대 규칙) | 매크로 구현 차이 |
| **AV Rule 176** | **함수 포인터 선언은 typedef** | 가독성 |

#### 4.3.4 형변환 (Type Conversions) — 특히 중요

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 177** | **사용자 정의 변환 함수 지양** | 의도하지 않은 암묵 호출 |
| **AV Rule 178** | **다운 캐스트(base→derived)** 는 ①가상 함수 에뮬레이션 ②visitor 패턴 만 허용. **`dynamic_cast`는 툴 지원 부족으로 불허**(일반 SW에선 허용) | 캐스트 합법성 보장 |
| **AV Rule 179** | **가상 베이스 포인터 → 파생 포인터 변환 금지** | 레이아웃 불명(컴파일 시점) |
| **AV Rule 180** | **정보 손실 묵시 변환 금지** | 프로그래머 인지 부재 |
| **AV Rule 181** | **중복 명시 캐스트 금지** | 클러터 |
| **AV Rule 182** | **포인터 ⇔ 정수 캐스트 금지**(예외: 하드웨어 주소 리터럴 등 저수준) | 정렬/정보 손실 |
| **AV Rule 183** | 캐스트 자체를 **최대한 피하라** | 캐스트 오류는 치명적 |
| **AV Rule 184** | **float→int 변환 제한(알고리즘 요구/하드웨어 인터페이스 시에만)** | 오버플로·정밀도 |
| **AV Rule 185** | **C 스타일 캐스트 금지 → `static_cast`/`const_cast`/`reinterpret_cast`(명명 캐스트)** | 의도 명확·검색 용이 |

> **JSF와 `dynamic_cast`/`typeid`의 관계(중요)**: 표준 C++의 RTTI 기능은 JSF 환경에서 **사용되지 않습니다**. 원문은 `dynamic_cast`가 "툴 지원 부족"으로 불허되고, 검토 후 주 _허용 가능성이 열려 있으나(AV Rule 178 Note), 실질적으로 아래 3종 명명 캐스트만 실용적으로 사용됩니다:
> - `static_cast`(컴파일 타임 검사 변환)
> - `const_cast`(const/volatile 제거)
> - `reinterpret_cast`(비검사 변환 — 저수준만, AV Rule 182와 연동)
> - `dynamic_cast`(런타임 검사 — **불허**) · `typeid`(RTTI — 미사용)

```cpp
// AV Rule 185 + 178 예시 (JSF에서 다운 캐스트 대신 가상 함수)
class Sensor
{
public:
    virtual float32 read_raw() = 0;
};
class Radar_sensor : public Sensor
{
public:
    float32 read_raw() override;   // 구현에서 실제 동작
    // "Radar인지 확인"은 dynamic_cast 없이 가상함수로만(가상 에뮬레이션/visitor)
};
```

#### 4.3.5 제어 흐름

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 186** | **도달 불가 코드 금지** | 죽은 코드(DO-178B 관점) |
| **AV Rule 187** | 비-널 문장은 **부수효과** 있어야 | 의미 없는 문장 오류 |
| **AV Rule 188/189** | label은 **switch에서만**, **goto 금지**(다중 루프 탈출 등 예외) | 제어 흐름 이해 곤란 |
| **AV Rule 190** | **`continue` 금지** | 가독성(단순 루프 구조) |
| **AV Rule 191** | **`break` 금지**(switch case 종료만 예외, 단일 루프 탈출 허용 예외) | 흐름 투명성 |
| **AV Rule 192** | `if/else if` 계열은 **마지막 `else` 또는 이유 주석** | defensiveness |
| **AV Rule 193** | **switch case는 `break`로 종료** | fall-through 혼동 |
| **AV Rule 194** | 모든 enum을 검사하지 않으면 **`default` 포함** | 누락 처리 |
| **AV Rule 195** | **switch 식에 bool 금지**(if 사용) | 자연스러운 표현 |
| **AV Rule 196** | switch는 **최소 2 case + default** | if가 자연스러우면 if |
| **AV Rule 197** | **float를 루프 카운터로 금지** | 반올림 |
| **AV Rule 198/199** | for 루프의 초기화/증가식은 **한 파라미터만** | 가독성 |
| **AV Rule 200** | null 초기화/증가식은 **while**로 | 자연스러운 표현 |
| **AV Rule 201** | **루프 본문에서 루프 카운터 수정 금지** | 유지보수 |

#### 4.3.6 표현식 · 메모리 · 예외 · 이식성 · 시험

| 규칙 | 원문 요지 | 근거 |
|---|---|---|
| **AV Rule 202** | **float 동등 비교(`==`/`!=`) 금지** | 반올림 오차 |
| **AV Rule 203** | **오버플로/언더플로 유발 표현식 금지**(필요 시 문서화) | 오류 신호 |
| **AV Rule 204** | 부수효과 있는 단일 연산은 **제한된 컨텍스트에서만** | 가독성 |
| **AV Rule 204.1** | **평가 순서에 결과가 의존하지 않게**(순서 비지정 대응) | 이식성·버그 |
| **AV Rule 205** | **`volatile`은 하드웨어 인터페이스에서만** | 최적화 억제 남용 방지 |
| **AV Rule 206** | **초기화 후 힙 할당/해제 금지**(placement new는 저수준 메모리 관리 루틴만) | 파편화→비결정적 지연 |
| **AV Rule 207** | **캡슐화되지 않은 전역 데이터 회피** | 접근 보호 |
| **AV Rule 208** | **예외 금지: `throw`, `catch`, `try` 사용 시** | 툴 지원 부족(2005년 당시) |
| **AV Rule 209** | **`int, short, long, float, double` 직접 사용 금지** → UniversalTypes typedef | 크기 이식성 |
| **AV Rule 210/210.1** | **메모리 표현 가정 금지**(endian, 데이터 멤버 배치 등) | 비이식성 |
| **AV Rule 211** | 데이터가 **특정 주소에서 시작한다는 가정 금지** | 구조체 패딩·배치 |
| **AV Rule 212** | 오버플로/언더플로에 **특별히 의존 금지** | 비이식성 |
| **AV Rule 213** | **산술 연산자 아래 우선순위는 괄호로 명시** | 가독성 |
| **AV Rule 214** | **번역단위 간 비-지역 static 초기화 순서 가정 금지** | 초기화 순서 버그 |
| **AV Rule 215** | **포인터 산술 금지**(컨테이너/이터레이터/할당기 예외) | 오류·범위 밖 참조 |
| **AV Rule 216** | **조기 최적화 금지** (Knuth 인용) | 가독성 희생 방지 |
| **AV Rule 217** | **컴파일/링크 타임 오류를 런타임보다 선호** | 검출 시점 상향 |
| **AV Rule 218** | 컴파일러 **경고 레벨은 프로젝트 정책 준수** | 경고로 사전 검출 |
| **AV Rule 219** | base 클래스에 적용한 **모든 시험은 derived에도** | 투명 대체(LSP) |
| **AV Rule 220** | 구조적 커버리지는 **flattened(파생 포함) 클래스 기준** | 다중 컨텍스트 혼합 방지 |
| **AV Rule 221** | 가상 함수 계층은 **동적 디스패치의 모든 해상 조합 커버** | 디스패치 테이블 의사결정 커버 |

**UniversalTypes 파일 (AV Rule 209의 실사례 — 원문 부록 A의 예)**

```cpp
typedef unsigned char  uint8;
typedef char           int8;
typedef unsigned short uint16;
typedef short          int16;
typedef unsigned long  uint32;   // 컴파일러별 크기 확인 후 정의
typedef long           int32;
// ... int64, uint64, float32, float64
// 기본 제공: bool, char
```

**심화 학습 확인 문제**

1. 예외(AV Rule 208)를 금지한 "툴 지원 부족" 근거를, 2005년 당시 DO-178B 정적 분석/인증 도구 상황과 연결해서 설명하시오.
2. AV Rule 206(초기화 후 힙 금지)이 **결정성**과 무슨 관련인지, 파편화 → 비결정적 지연 관점에서 서술하시오.
3. 왜 `dynamic_cast`를 금지하면서 AV Rule 178은 "가상 함수 에뮬레이션 또는 visitor 패턴"만 허용하나?
4. AV Rule 209가 `int`를 금지하는 이유와, 오늘날의 `<cstdint>`/`std::int32_t`와의 관계를 화면에 서술 + 설명하시오.

---

## 5. 표준 C++와의 다른 점

JSF++는 **새 문법을 "추가"하지 않습니다. 반대로 표준 C++(2003 시점)의 기능을 "제한"** 하는 서브셋 규격입니다. 아래 표로 정리합니다.

### 5.1 한눈에 보는 비교표

| 영역 | 일반(원전) C++ | JSF AV C++ (2RDU00001 Rev C) |
|---|---|---|
| 언어 기준 | ISO/IEC 14882 (C++20/17/11 등 최신) | **ISO/IEC 14882:2002/2003의 안전 서브셋만**, 확장 금지 (AV 8) |
| 예외 | `try/throw/catch` 자격 | **완전 금지** (AV 208) |
| RTTI | `typeid`, `dynamic_cast` 허용 | **미사용/불허**(다운캐스트는 가상 함수·visitor만, AV 178/179) |
| 캐스트 | C 스타일·명명 캐스트 병용 | **명명 캐스트만**(C 스타일 금지, AV 185) |
| 연산자 오버로딩 | 광범위 허용 | 드물게 + 관습적으로만; `\|\|/&&/단항 &` 금지 (AV 84/159) |
| 다중 상속 | 자유 | **제한형만**: 인터페이스 n + private 구현 m + protected 구현 ≤1 (AV 88) |
| `new`/`delete` | 런타임 자유 | **초기화 후 금지** (AV 206) |
| 기본 타입 | `int`, `long`, `float` 등 직접 사용 | **`int8~int64, uint8~uint64, float32/64` typedef 사용** (AV 209) |
| 전처리기 | 매크로 흔히 사용 | `#ifndef/#define/#endif/#include` 외 금지, `const`/`inline` 대체 (AV 26–31) |
| 표준 라이브러리 | std::vector 등 STL 전부 | **DO-178B Level A certifiable 또는 SEAL 1 인증 라이브러리만** (AV 16). `errno`, `setjmp`, `signal.h`, `stdio.h`, `time.h`, `atof/atoi/atol`, `abort/exit/getenv/system` 금지 (AV 17–25) |
| 제어 흐름 | `goto`, `continue`, `break` 자유 | goto/continue/break `sh`. 회귀적 제한, 단일 출구 (AV 113, 186–201) |
| 널 포인터 리터럴 | `NULL`(0 현대엔 `nullptr`) | **`0` 사용**(C++03 환경), NULL 매크로 금지 (AV 175) |
| 재귀 | 자유 | 금지(자원 증명 예외) (AV 119) |
| 힙 동적 할당 | 자유 | 초기화 후 금지 → **컨테이너/정적 할당 전략** (AV 206) |
| `volatile` | 언제든 | **하드웨어 인터페이스에서만** (AV 205) |
| unsigned 산술 | 빈번 | **금지**(signed/unsigned 혼용 방지) (AV 162/163) |
| 함수 크기·복잡도 | 규칙 없음 | 200 L-SLOC, 사이클로매틱 ≤20 (AV 1/3) |
| 생성자/소멸자 | 가상 함수 호출 가능(비권장) | **생성자/소멸자에서 가상 함수 호출 금지** (AV 71.1) |
| 소멸자 규칙 | 권고 | **가상 함수 있는 base는 가상 소멸자 필수** (AV 78) |
| 시험 요구 | 프로젝트 결정 | base 시험 상속, flattened 커버리지, 다형성 디스패치 커버 (AV 219–221) |

### 5.2 대표적인 "결" 차이 3가지 — 심층 설명

**① "예외 없음"은 무슨 뜻인가?**
- 표준 C++: 오류 시 `throw`하여 예외 객체를 전파.
- JSF++: 오류는 **반환 값 + 전역 오류 플래그 대신 호출부 검사**(AV Rule 115), 상태 코드·방어적 체크(AV Rule 15)로 처리.
- 이유: 예외 처리 경로는 정적 분석 도구로 해석하기 어렵고(2005년 당시), RAII·스택 풀기로 인한 **비결정적 시간**·**추적 불가능한 암묵적 제어 전환**이 DO-178B 검증과 충돌했기 때문.

**② "초기화 후 힙 금지"는 어떻게 운영하나?**
- 표준 C++: `new`/`delete` 상시 사용.
- JSF++: 프로그램 **시작 시(초기화 단계)에 필요한 객체를 완전히 할당**하고, 이후 실시간(비행 중)에는 힙 접근이 없도록 설계. → **결정적 실시간 동작**, 파편화로 인한 지연 예측 불가 해소. `placement new`는 저수준 메모리 관리 루틴에서만(AV Rule 206 Note).

**③ "RTTI/다운캐스트 정책"은?**
- 표준 C++: `dynamic_cast<Derived*>(base_ptr)`로 안전 다운캐스트.
- JSF++: 그 대신 인터페이스를 **가상 함수로 설계**(행동 버튼만 노출)하거나 **visitor 패턴**으로 파생별 동작을 분기 → **타입 태그/캐스트 없이** 다형성을 구현. (AV Rule 178)

### 5.3 코드 비교 예제

**일반 C++ (자유도 높음):**

```cpp
#include <new>
#include <iostream>

class Base { public: virtual ~Base() {} };
class Derived : public Base {
    double* huge_;
public:
    Derived() : huge_(new double[1'000'000]) {}
    ~Derived() { delete[] huge_; }
};

int main() {
    try {
        Base* b = new Derived;
        if (Derived* d = dynamic_cast<Derived*>(b)) {
            std::cout << "Is Derived" << std::endl;
        }
        delete b;
    } catch (const std::bad_alloc&) {
        return -1;
    }
    return 0;
}
```

**JSF AV C++ 스타일 (예상 형태로 재작성):**

```cpp
// telemetry.h
#ifndef telemetry_h
#define telemetry_h

class Sensor
{
public:
    virtual ~Sensor();                  // AV 78
    virtual float32 read_value() = 0;   // dynamic_cast 대신 virtual로 해결 (AV 178)
};
#endif

// main.cpp — 허상: 초기화 단계에서만 할당(AV 206), 예외 없음(AV 208), stdio/time 금지(AV 22/25)
int32 main()
{
    Sensor* sensors[2];                 // 초기화 단계에서 정적/사전 할당 전략
    sensors[0] = acquire_radar_sensor();   // 반환 오류 검사 (AV 115)
    if (sensors[0] == 0) return -1;        // NULL= 0 사용 (AV 175)

    int32 result = run_mission(sensors);
    return result;                         // 단일 출구 (AV 113)
}
```

> **요약**: JSF++ 코드는 표준 C++ 처리기 + {사용자, 컴파일러, 정적 분석기}가 **"허용/금지 지도"를 명확히 알 수 있는 제약된 C++** 입니다. 코드 스타일·네이밍·테스트 관례까지 **검증 가능한 절차**와 묶인 것이 표준 C++의 "그냥 언어"와 가장 큰 차이입니다.

---

## 부록

### A. 주요 규칙 번호 빠른 색인

| 주제 | 규칙 번호 |
|---|---|
| 크기/복잡도 | 1–3 |
| 이탈/독립 허용 절차 | 4–7 |
| 언어/문자셋 | 8–14 |
| 런타임 검사·라이브러리 | 15–16 |
| C 라이브러리 금지 | 17–25 |
| 전처리기/헤더 | 26–40 |
| 스타일/네이밍 | 41–63 |
| 클래스/생성자/소멸자 | 64–83 |
| 연산자 오버로딩 | 84–85 |
| 상속/가상 | 86–97.1 |
| namespace | 98–100 |
| 템플릿 | 101–103 |
| 함수 규약 | 107–120 |
| 연산자 | 157–168 |
| 포인터/참조 | 169–176 |
| 형변환 | 177–185 |
| 제어 흐름 | 186–201 |
| 표현식 | 202–205 |
| 메모리/전역 | 206–207 |
| 예외 | 208 |
| 이식성 | 209–215 |
| 효율·기타 | 216–218 |
| 시험 | 219–221 |

### B. 혼자 공부하는 로드맵

1. **1주차 — 개념**: 이 가이드 1·2장 + 원문 1~3장 읽기. shall/will/should와 deviation 절차(AV Rule 4–7)를 정확히 이해.
2. **2주차 — 규칙 카탈오그**: 4장(본 가이드)을 원문과 대조하며 각 rule의 rationale를 1줄로 요약 연습.
3. **3주차 — 실전 코딩**: 다음 제약만 써서 작은 태스크(센서 상태 머신, 메시지 파서)를 C++03 서브셋으로 작성.
   - `-fno-exceptions -fno-rtti` 컴파일
   - 초기화 후 `new` 금지 → 사전 할당/정적 테이블
   - `dynamic_cast` 금지 → 가상 함수/visitor
   - `int32`/`uint16`/`float32` typedef
   - 단일 출구, 오류 반환 검사
4. **4주차 — 도구로 검증**: Polyspace(Check JSF C++ rules) 또는 Klocwork JSF 체커 등으로 정적 분석 실행, 위반 항목을 deviation 절차대로 기록하는 연습.
5. **5주차 — 확장**: MISRA C++:2008과 AUTOSAR C++14를 대조하며 "JSF 룰이 어떻게 현대 C++로 발전했는가" 정리.

### C. 참고 자료 목록 (출처)

- [원문 PDF] JSF AV C++ Coding Standards, Doc No. 2RDU00001 Rev C (Dec 2005) — https://www.stroustrup.com/JSF-AV-rules.pdf (Distribution Statement A)
- ISO/IEC WG23 TR24772 교차 참조(각 AV Rule → 안전 취약점 카테고리 매핑) — https://www.open-std.org/jtc1/sc22/wg23/docs/s0006.html
- MathWorks Polyspace JSF C++ 규칙 지원 — https://www.mathworks.com/help/bugfinder/jsf-c-rules.html
- Klocwork JSF C/C++ 체커 참조 — https://help.klocwork.com/2024/en-us/concepts/jsfcommunitycandccheckerreference.htm
- Green Hills: Lockheed F-35 고객 페이지 — https://www.ghs.com/customers/lockheedf35.html
- Green Hills: L-3 F-35 Panoramic Cockpit Display 채택 보도(2006) — https://www.ghs.com/news/20061030_panoramic.html
- Avionics Today(2008): F-35 임베디드 및 "PowerPC는 유일한 application-level 프로세서" 인터뷰 — https://www.aviationtoday.com/2008/11/01/embedded-overall/
- Lynx Software: F-35 TR3 & L3Harris ICP 채택 보도/케이스스터디 — https://www.lynx.com/casestudies/lockheedmartin , https://www.lynx.com/lynx-mosaic-selected-for-f35-lightning-ii-mission-systems-avionics
- F-35 MSAR FY2027(TR-3 선전, Block 4) — DoD Selected Acquisition Report (공개판)
- EDN: F-35 electronics 아키텍처 개요(ICP 그룹 포함) — https://www.edn.com/f-35-lightning-ii-advanced-electronics-for-stealth-sensors-and-communications

### D. 주의사항 · 면책

- 규칙 번호와 원문 인용은 **2RDU00001 Rev C(2005-12)** 기준입니다. 이후 사내 개정(Rev D 등)에서는 번호가 달라질 수 있습니다.
- **3장의 하드웨어/타겟 정보는 F-35 관련 공개 자료의 한계상 비공개 상세를 포함하지 않으며**, 발췌 출처 기반의 개요 수준입니다. 정확한 보드 규격은 취급 권한이 있는 자료에서 확인해야 합니다.
- 본 표준은 **2005년(C++03) 시대**의 결정(예: 예외·동적 메모리 금지 "툴 지원 부족")을 반영합니다. 현재 사업 환경에서는 최신 컴파일러·인증 도구·멀티코어 인증(CAST-32A)을 고려한 추가 정책이 필요합니다.
- 학습 목적의 예제 코드는 표준 준수 "스타일"을 보여주기 위한 것이며, 실제 인증 소프트웨어 작성 절차를 대체하지 않습니다.

---

*본 가이드는 공개 배포 문서(Distribution Statement A)를 근거로 학습을 돕기 위해 작성되었습니다.*