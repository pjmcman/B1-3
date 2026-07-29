[README.md](https://github.com/user-attachments/files/30485324/README.md)
# B1-3# 노코드 자동화 파이프라인 구축 및 비교 분석 보고서

## 1. 개요 및 미션 이해
매일 수신되는 주요 파트너사("코디세이")의 이메일과 각종 시스템 알림/스팸 메일 속에서 **중요 미션 메일을 즉시 식별 및 분류하고 답장 초안을 작성**하는 반복 업무를 노코드 자동화 도구를 활용해 자동화합니다. 

Trigger(이메일 감지)와 Action(분석 및 저장), 그리고 조건 분기(Filter/Router)와 AI 연동(Google Gemini AI)을 결합하여 이메일 처리 속도와 업무 생산성을 극대화하는 파이프라인을 구축하고 분석합니다.

---

## 2. [프로젝트 1] 자동화 도구 비교 구현

### 2.1 워크플로우 정의
* **워크플로우명**: 코디세이 수신 이메일 자동 감지 및 Gemini AI 기반 긴급도 조건 분기/요약 후 Gmail 임시보관함 자동 생성
* **구조 요약**: `Gmail(Trigger)` ➔ `Google Gemini AI(Action 1)` ➔ `Filter/Router(조건 분기)` ➔ `Gmail Draft(Action 2)`

---

### 2.2 [Make 구현]

* **Trigger**: 
  * `Gmail - Watch Emails`: 지정된 발신자(`no-reply@codyssey.kr` 또는 `from:코디세이`)의 신규 이메일 감지
* **Action 1 (AI 연동 - 보너스 과제 1)**: 
  * `Google Gemini AI - Generate a response`: 수신된 이메일 본문을 분석하여 긴급성(`[긴급]` / `[일반]`) 판정, 핵심 요약(2~3줄), 답장 초안 생성
* **조건 분기 (Router/Filter - 필수 요구사항)**: 
  * `Router`: Gemini 결과에 따른 분기 처리
    * **경로 A (긴급)**: Gemini 결과에 `[긴급]` 포함 시 ➔ 긴급 전용 모듈 실행
    * **경로 B (일반)**: Gemini 결과에 `[일반]` 포함 시 ➔ 일반 전용 모듈 실행
* **Action 2**: 
  * `Gmail - Create a Draft`: 조건 분기에 맞게 머리말이 달린 답장 초안을 Gmail 임시보관함(Drafts)에 자동 저장

> 🖼️ **[Make 워크플로우 구성 화면 캡처]**
> 
><img width="621" height="213" alt="Image" src="https://github.com/user-attachments/assets/91b1924e-c5cb-4e8b-b54c-f0d242d1dc42" />

> 🖼️ **[Make 실행 결과 화면 캡처]**  
[> `[이미지 첨부 위치: Make_Execution_Success.png]`](https://private-user-images.githubusercontent.com/299512523/628194868-a4ade4ed-a3aa-41f3-ad5a-067c15f9e830.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODUyOTAzMzIsIm5iZiI6MTc4NTI5MDAzMiwicGF0aCI6Ii8yOTk1MTI1MjMvNjI4MTk0ODY4LWE0YWRlNGVkLWEzYWEtNDFmMy1hZDVhLTA2N2MxNWY5ZTgzMC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwNzI5JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDcyOVQwMTUzNTJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01NWNmODg1NmY2YjJjMGY0NmY1OTYwZDAyZDlkOTkzMzRmY2ZmMWY5ZGM3ZGY3OGRlMDI5ZmE2NjcyZTkxN2YxJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.XkvyYivFCaWA1jQ8r7BkGlhZ28Pjqa1KJK6sXtW_xNU)

---

### 2.3 [비교 대상 도구 구현 (Zapier)]

* **도구명**: Zapier
* **Trigger**: 
  * `Gmail - New Email Matching Search`: 검색 조건(`from:no-reply@codyssey.kr`)으로 이메일 수신 감지
* **Action 1 (AI 연동 - 보너스 과제 1)**: 
  * `Google AI Studio (Gemini) - Send Prompt`: `Gemini 3.1 Flash Lite` 모델을 활용해 긴급도 판단 및 요약/답장 작성
* **조건 분기 (Filter - 필수 요구사항)**: 
  * `Filter by Zapier`: Gemini 분석 결과 텍스트 내 `[긴급]` 또는 `[일반]` 키워드 존재 여부 검증 및 경로 통과
* **Action 2**: 
  * `Gmail - Create Draft`: Gemini가 생성한 답장 초안을 Gmail 임시보관함에 저장

> 🖼️ **[Zapier 워크플로우 구성 화면 캡처]**  
> `[이미지 첨부 위치: Zapier_Workflow_Architecture.png]`

> 🖼️ **[Zapier 실행 결과 화면 캡처]**  
> `[이미지 첨부 위치: Zapier_Execution_Success.png]`

---

### 2.4 비교 분석 보고서

| 비교 항목 | Make (Integromat) | 비교 도구 (Zapier) |
| :--- | :--- | :--- |
| **1) UI/UX** | 시각적 캔버스(Canvas) 기반 노드 연결 형태로 전체 데이터 흐름 직관적 파악 가능 | 위저드 단계별(Step-by-step) 리스트 구조로 인터페이스가 매우 직관적임 |
| **2) 설정 난이도** | 데이터 매핑 및 세부 파라미터 설정 항목이 많아 초기 학습 곡선 존재 | 단계별 가이드 방식으로 초보자도 빠르고 쉽게 매핑 및 세팅 가능 |
| **3) 연동 서비스 범위** | 구글 워크스페이스, AI 커넥터 등 광범위한 앱을 깊이 있게 연동 지원 | 방대한 앱 지원 및 Gemini Flash 모델 연동이 매우 빠르고 안정적임 |
| **4) 무료 플랜 범위** | 월 1,000 Operations / 2개 Active Scenarios 제공으로 다단계 구현에 유리 | 월 100 Tasks / 5개 Single-step Zaps (시범 멀티스텝 구동 지원) |
| **5) 실행 로그 확인** | Inspector Bubble(돋보기)을 통해 각 단계별 In/Out JSON 데이터 실시간 검증 가능 | Zap History 메뉴에서 각 스텝별 Data In/Out 상태를 명확히 추적 가능 |

#### 각 도구의 장단점 및 추천 상황
* **Make**: 
  * **장점**: 자유도가 높은 시각적 캔버스, 세밀한 데이터 가공 및 복잡한 조건 분기(Router) 처리에 강점.
  * **단점**: 필드 매핑 및 에러 핸들링 구조가 초보자에겐 다소 복잡함.
  * **추천 상황**: 다단계 AI 데이터 가공 및 조건별 분기 처리가 많은 복잡한 자동화 파이프라인.
* **Zapier**: 
  * **장점**: 직관적인 Step-by-step UI, 빠른 구축 속도, 적은 에러 발생률.
  * **단점**: 무료 플랜 Task 수 한도가 타이트함.
  * **추천 상황**: 직관적이고 빠르게 결과를 확인해야 하는 3~4단계 이내의 핵심 업무 자동화.

---

## 3. [프로젝트 2] 자유 주제 자동화 설계 및 구현

### 3.1 업무 정의 및 도구 선정
* **반복 업무 정의**: 오래된 메일함에 쌓인 스팸/알림 메일 사이에서 "코디세이" 파트너사의 중요 미션 메일을 분리하여, 긴급도를 판단하고 답장 초안을 자동으로 미리 생성하는 업무.
* **선정 도구**: **Zapier** (선정 이유: 직관적인 UI로 신속하게 파이프라인을 구축할 수 있으며, Gemini Flash 모델 연동이 매우 안정적임)

---

### 3.2 워크플로우 설계 문서

#### 흐름도 (Workflow Diagram)
`[Gmail: 코디세이 메일 수신]` ➔ `[Gemini AI: 긴급도 분류/요약]` ➔ `[Filter: 분기 조건 검증]` ➔ `[Gmail: 임시보관함 초안 생성]`

#### 주요 기능 요구사항 충족 여부
* **Trigger (1개 이상)**: Gmail - New Email Matching Search (`from:no-reply@codyssey.kr`)
* **Action (2개 이상)**:
  1. Google AI Studio (Gemini) - Send Prompt (긴급도 판단 + 요약 + 답장 작성)
  2. Gmail - Create Draft (임시보관함 저장)
* **조건 분기 (Filter/Router 1개 이상)**: Filter by Zapier (Gemini 결과 텍스트 검증 및 분기)
* **AI 연동 (보너스 과제)**: `Gemini 3.1 Flash Lite` 활용 텍스트 요약 및 초안 자동 작성
* **프롬프트 설계**:
  ```text
  너는 이메일 분석 및 답장 작성 보조 AI야.
  발신자 주소가 "no-reply@codyssey.kr" (코디세이) 인 수신 이메일을 읽고 다음 요구사항에 맞게 작성해 줘.

  [요구사항]
  1. 이 메일의 긴급성을 판단해서 맨 첫 줄에 반드시 "[긴급]" 또는 "[일반]" 표기를 넣어줘.
     - 당장 처리가 필요하거나, 오늘 내 마감, 장애/오류, 결제/계약 관련건 등은 [긴급]
     - 단순 공지, 안내, 정기 뉴스레터, 시간 여유가 있는 일반 문의는 [일반]
  2. 두 번째 줄부터는 아래 내용을 작성해 줘:
     - 📌 핵심 요약 (2~3줄)
     - 📝 추천 답장 초안

  ---
  [발신자]: {{1. From Email}}
  [메일 제목]: {{1. Subject}}
  [메일 본문]: {{1. Body Plain}}






