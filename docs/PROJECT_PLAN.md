# BrokerAPIX 프로젝트 계획

## 1. 프로젝트 개요

**Project Name:** BrokerAPIX  
**Slogan:** Build Once. Connect Every Broker.  
**Mission:** 여러 증권사의 Open API를 하나의 표준 인터페이스로 통합하여 누구나 투자 애플리케이션을 개발할 수 있는 오픈소스 플랫폼을 구축한다.

BrokerAPIX는 특정 증권사를 위한 애플리케이션이 아니라, 다양한 증권사의 Open API를 공통 SDK와 Adapter 구조로 추상화하는 플랫폼이다. 개발자는 BrokerAPIX SDK만 사용하면 증권사마다 다른 인증, 주문, 계좌, 시세, 실시간 이벤트 구현을 직접 반복하지 않고 투자 애플리케이션을 만들 수 있다.

## 2. 문제 정의

현재 대부분의 투자 서비스는 특정 증권사 API에 강하게 의존한다.

```text
Application
  ↓
Broker API
```

이 구조에서는 증권사를 변경하거나 추가할 때 다음 영역을 반복 수정해야 한다.

- API 호출 방식
- 인증 방식
- 응답 데이터 구조
- 주문, 계좌, 시세 처리 로직
- UI와 상태 관리

결과적으로 투자 서비스 개발자는 제품 가치보다 증권사별 API 차이를 처리하는 데 많은 시간을 사용하게 된다.

## 3. 해결 방향

BrokerAPIX는 Application과 Broker API 사이에 SDK, Core, Adapter 계층을 둔다.

```text
Application
  ↓
BrokerAPIX SDK
  ↓
Broker Adapter
  ↓
Broker API
```

Application은 Broker가 누구인지 알 필요가 없다. Core는 표준 인터페이스만 호출하고, Adapter가 각 증권사의 실제 API 호출과 응답 변환을 책임진다.

## 4. 핵심 원칙

### Broker Agnostic

Core에는 특정 증권사 분기 코드를 두지 않는다.

```typescript
// Core에서 금지
if (broker === "toss") {}
if (broker === "kis") {}
```

증권사별 차이는 Adapter와 Plugin 경계 안에 격리한다.

### SDK First

개발 우선순위는 UI가 아니라 SDK 계약과 Core 동작이다.

```text
SDK
  ↓
Core
  ↓
Plugin
  ↓
UI
  ↓
Application
```

### Plugin First

모든 확장 기능은 Plugin으로 설계한다.

- Broker Plugin
- AI Plugin
- Strategy Plugin
- Notification Plugin
- Theme Plugin
- Widget Plugin

Core는 Plugin을 직접 알지 않고 실행 계약만 관리한다.

## 5. 시스템 아키텍처

```text
Presentation Layer
  ↓
Application Layer
  ↓
Domain Layer
  ↓
BrokerAPIX SDK
  ↓
Broker Adapter
  ↓
Broker Open API
```

Presentation Layer는 Next.js, React, Flutter, Electron 등 어떤 UI Framework도 선택할 수 있어야 한다. BrokerAPIX의 핵심 가치는 UI가 아니라 증권사 API 추상화 계층과 확장 가능한 투자 애플리케이션 기반이다.

## 6. Broker Adapter

각 증권사는 API 호출 방식이 다르다.

```text
토스증권: POST /orders
한국투자증권: POST /uapi/domestic-stock
키움증권: COM API
```

Broker Adapter는 다음 책임을 가진다.

- 인증과 토큰 갱신
- 주문 요청 변환
- 계좌, 잔고, 포트폴리오 응답 정규화
- 시세 조회와 실시간 구독 연결
- 증권사별 에러를 표준 에러로 변환
- Rate limit, 재시도, 연결 해제 처리

## 7. SDK 표준 인터페이스

모든 Broker Adapter는 다음 인터페이스를 구현해야 한다.

```typescript
authenticate()
disconnect()

getAccount()
getBalance()
getPortfolio()
getPositions()
getOrders()

buy()
sell()
cancel()
modify()

search()
getQuote()

subscribeQuote()
subscribeOrder()
subscribeAccount()
```

Core와 Application은 이 인터페이스만 사용한다.

## 8. Event Driven Architecture

BrokerAPIX는 이벤트 중심으로 동작한다.

```text
Market Event
  ↓
Broker Adapter
  ↓
Event Bus
  ↓
Portfolio
  ↓
Chart
  ↓
AI
  ↓
Notification
  ↓
Widget
```

모든 기능은 공통 이벤트를 공유한다. 이를 통해 시세, 주문 체결, 계좌 변경, 포트폴리오 변화, 알림, AI 분석을 느슨하게 연결한다.

## 9. UI/UX 방향

BrokerAPIX의 UI는 Robinhood, Toss, Apple, Linear, Bloomberg의 장점을 참고하되 단순 복제하지 않는다. 목표는 초보자에게는 쉽고, 전문가에게는 충분히 깊은 투자 경험이다.

정보는 단계적으로 공개한다.

```text
Portfolio
  ↓
Today's Profit
  ↓
Chart
  ↓
Position
  ↓
AI Strategy
  ↓
Market Summary
  ↓
News
  ↓
Advanced Analytics
```

디자인 시스템 원칙은 다음과 같다.

- Dark Mode First
- 8px Grid
- 16px Radius
- Minimal Border
- Whitespace First
- Premium Typography
- 150~250ms Motion
- Accessible Design
- Responsive Design

## 10. 기술 스택

초기 기준 기술 스택은 다음과 같다.

- **Framework:** Next.js, React, TypeScript
- **UI:** Tailwind CSS, shadcn/ui
- **State:** Zustand
- **Backend:** Supabase
- **Charts:** TradingView Lightweight Charts
- **Animation:** Framer Motion
- **Deployment:** Vercel

## 11. 레포지토리 구조

```text
brokerapix/
  apps/
  packages/
  docs/
  examples/
  scripts/
  .github/
```

## 12. 패키지 구조

```text
packages/
  brokerapix-core/
  brokerapix-sdk/
  brokerapix-ui/
  brokerapix-cli/
  brokerapix-broker-toss/
  brokerapix-broker-kis/
  brokerapix-broker-upbit/
  brokerapix-broker-binance/
  brokerapix-widget-sdk/
  brokerapix-strategy-sdk/
  brokerapix-ai-sdk/
```

## 13. Marketplace 전략

BrokerAPIX는 누구나 새로운 Broker Plugin을 만들고 배포할 수 있는 Marketplace를 지향한다.

예상 Broker Plugin:

- brokerapix-broker-kiwoom
- brokerapix-broker-mirae
- brokerapix-broker-nh
- brokerapix-broker-shinhan

Marketplace는 플러그인 설치, 버전 관리, 호환성 확인, 문서 연결을 제공해야 한다.

## 14. AI Engine

AI Engine은 Broker와 완전히 독립적으로 설계한다.

지원 목표:

- OpenAI
- Gemini
- Claude
- Local LLM
- DeepSeek

주요 기능:

- AI Strategy Builder
- Risk Analysis
- Portfolio Analysis
- Trade Journal
- Natural Language Search
- Performance Review

## 15. Backtesting Engine

Backtesting Engine은 Strategy SDK와 Historical Data를 기반으로 투자 전략을 검증한다.

```text
Strategy
  ↓
Historical Data
  ↓
Simulation
  ↓
Performance
  ↓
Risk Analysis
  ↓
Report
```

## 16. 오픈소스 전략

- MIT License를 기본 라이선스로 검토한다.
- Core, SDK, Plugin, 문서를 공개한다.
- API Key와 사용자 인증 정보는 Repository에 포함하지 않는다.
- 사용자는 자신의 환경 변수 또는 Secret Store에 API Key를 직접 저장한다.
- 증권사별 공식 Open API 정책과 약관을 준수한다.

## 17. 개발 로드맵

### Phase 1: Foundation

- BrokerAPIX SDK 표준 인터페이스 정의
- Core Engine 구현
- Broker Adapter 1개 구현
- Dashboard 기본 화면
- Portfolio 기본 화면
- Chart 기본 화면

### Phase 2: Realtime Platform

- Broker Adapter 추가
- Event Bus 구현
- AI Engine 초기 버전
- Realtime Data 처리
- Notification 시스템

### Phase 3: Extension Platform

- Widget SDK
- Strategy SDK
- Marketplace 초안
- Community Plugin 구조
- Plugin 개발 문서

### Phase 4: Multi Platform

- PWA
- Android
- iOS
- Desktop

### Phase 5: Ecosystem

- BrokerAPIX 생태계 확장
- 외부 개발자용 문서와 예제 강화
- Marketplace 운영 정책 수립
- 기업용 확장 기능 검토

## 18. 성공 기준

BrokerAPIX의 성공은 좋은 투자 앱 하나를 만드는 것이 아니라, BrokerAPIX 위에서 여러 투자 제품이 만들어질 수 있는 기반을 제공하는 것이다.

성공 기준:

- 하나의 SDK로 여러 Broker를 교체하거나 추가할 수 있다.
- Core에 특정 Broker 분기 코드가 없다.
- Adapter를 추가해도 Application 코드 변경이 최소화된다.
- 실시간 시세, 주문, 계좌 이벤트가 공통 이벤트 모델로 흐른다.
- 외부 개발자가 Broker Plugin, Widget, Strategy, AI 모듈을 기여할 수 있다.
- 투자 앱, 자동매매 플랫폼, AI 투자 서비스, 포트폴리오 분석 서비스, 퀀트 연구 도구, 기업용 트레이딩 시스템을 만들 수 있다.

## 19. 최종 비전

> BrokerAPIX는 모든 증권사의 Open API를 하나의 표준 인터페이스로 통합하는 오픈소스 플랫폼이다.

BrokerAPIX는 특정 증권사를 위한 애플리케이션이 아니라, 미래의 투자 애플리케이션 생태계를 위한 기반 플랫폼을 만드는 것을 목표로 한다.

**Write Once. Connect Every Broker.**
