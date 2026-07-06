# BrokerAPIX

**The Open Platform for Investment Applications**

> Build Once. Connect Every Broker.

BrokerAPIX는 여러 증권사의 Open API를 하나의 표준 인터페이스로 통합하여, 누구나 투자 애플리케이션을 개발할 수 있도록 돕는 오픈소스 플랫폼입니다.

BrokerAPIX는 특정 증권사를 위한 앱이 아닙니다. 토스증권, 한국투자증권, 키움증권, 미래에셋증권 등 서로 다른 증권사 API를 공통 SDK와 Adapter 구조로 추상화하는 개발 플랫폼입니다.

## 핵심 방향

- **Broker Agnostic**: Core는 특정 증권사명을 알지 않습니다.
- **SDK First**: UI보다 SDK와 Core 계약을 먼저 정의합니다.
- **Plugin First**: Broker, AI, Strategy, Notification, Widget 기능은 플러그인으로 확장합니다.
- **Event Driven**: 시세, 주문, 계좌, 포트폴리오 이벤트를 공통 이벤트 버스로 연결합니다.
- **Open Source First**: Core, SDK, Plugin, 문서를 공개하고 API Key는 사용자 환경에만 보관합니다.

## 목표

BrokerAPIX 위에서 다음과 같은 제품을 만들 수 있어야 합니다.

- 투자 앱
- 자동매매 플랫폼
- AI 투자 서비스
- 포트폴리오 분석 서비스
- 퀀트 연구 도구
- 기업용 트레이딩 시스템

## 문서

- [프로젝트 계획](docs/PROJECT_PLAN.md)

## 최종 비전

> BrokerAPIX는 모든 증권사의 Open API를 하나의 표준 인터페이스로 통합하는 오픈소스 플랫폼입니다.

**Write Once. Connect Every Broker.**
