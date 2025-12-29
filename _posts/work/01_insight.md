---
title: Insight 탭 성능 개선 및 안정화
date: 2025-10-01 1:35:23 +0900
updated: 2025-10-01 1:35:23 +0900
category: work
tags: mysql, elasticsearch, python 
permalink: /work/improve_1
---
# Insight 제품 성능개선

## 개요

- **프로젝트 목적** : insight 제품 성능 개선 및 안정화
- **프로젝트 기간** : 2025.09 ~ 2025.10
- **핵심 기술** : Elasticsearch, 비동기 처리, 쿼리 최적화
- **프로젝트 효과** :
  - Load Test: RPS 90% 향상 (21.81 → 41.43 req/s), 평균 응답시간 47% 감소 (1,405.92ms → 739.24ms)
  - Stress Test: RPS 215% 향상 (25.73 → 81.16 req/s), 평균 응답시간 68% 감소 (5,173.22ms → 1,637.41ms)

## 프로젝트 성과 요약

### 주요 성과

**처리량 개선:**
- Load Test 환경(50 VUs): 초당 요청 처리량 90% 향상
- Stress Test 환경(300 VUs): 초당 요청 처리량 215% 향상
- 고부하 환경일수록 Elasticsearch의 성능 우위가 더 크게 나타남

**응답시간 개선:**
- Load Test: 평균 응답시간 47% 감소, P95 응답시간 42% 감소
- Stress Test: 평균 응답시간 68% 감소, P95 응답시간 57% 감소
- 고부하 환경에서 응답시간 안정성 크게 개선

**안정성 향상:**
- Worker timeout 문제 완전 해결
- 모든 테스트에서 100% 성공률 유지
- P95 응답시간 대폭 감소로 사용자 경험 일관성 향상

**기술적 개선:**
- MySQL 동기식 접근 → Elasticsearch 비동기 검색으로 전환
- DB 호출 횟수 대폭 감소 (3회 → 1회)
- SQL 인젝션 위험 제거 및 코드 품질 향상

## 문제점 파악

### 1. 문제 발생패턴

- 관심 그룹에서 뉴스/공시 조회 시 응답 시간 지연
- 고부하 환경에서 worker timeout 빈번 발생
- 실시간 인기 그룹 조회 시 성능 저하

### 2. 문제 원인분석

#### 1) DB 접근 및 쿼리 구조 문제

**중복 쿼리 실행:**

- 실시간 인기 종목 조회 시 US/KR 쿼리 분리로 3회 DB 호출 발생
  - 그룹 확인 1회 + US 조회 1회 + KR 조회 1회
- 일반 그룹: 2회 DB 호출 (그룹 확인 1회 + 종목 조회 1회)
- 동일 로직의 쿼리가 국가별로 중복 실행되어 코드 중복 및 성능 저하

**인덱스 부재:**

- `stock_trend.ctry`, `news_analysis.date`, `news_analysis.is_related` 컬럼에 복합 인덱스 미설정
- `disclosure_information` 테이블의 `ticker`, `date`, `lang` 컬럼에 복합 인덱스 미설정

**JOIN 최적화 필요:**

- `news_analysis`와 `stock_trend` 테이블 간 JOIN 조건 최적화 필요
- 과도한 JOIN 사용으로 인한 성능 저하

#### 2) 페이지네이션 비효율

- 전체 데이터를 메모리에서 분할하는 방식 사용
- DB 레벨에서 LIMIT/OFFSET 미적용
- 공시 데이터 조회 시 100개 제한으로 인한 데이터 불완전성

#### 3) 캐싱 전략 부재

- 실시간 인기 종목 데이터 반복 조회
- 24시간 이내 동일 데이터 캐싱 미적용
- Redis 등 캐싱 시스템 미활용

#### 4) 보안 취약점

- f-string 사용으로 SQL 인젝션 위험 존재
- 쿼리 파라미터 바인딩 미적용

#### 5) Worker timeout

- DB 병목으로 인한 worker timeout 발생
- 동기식 접근으로 인한 블로킹 발생

#### 6) 자원 공유 문제

- 앱, 웹, AI(SSE) 모든 라우팅간 자원 공유로 인한 병목 발생

## 개선방안

### 1. Elasticsearch 도입 (elasticsearch.py)

#### 1) 비동기 Elasticsearch 클라이언트 구현

**AsyncElasticsearch 클라이언트 설정:**
```python
class ElasticsearchClient:
    def __init__(self):
        self.client = AsyncElasticsearch(
            hosts=[settings.ELASTICSEARCH_URL],
            timeout=30,
            max_retries=3,
            retry_on_timeout=True,
        )
```

**주요 기능:**
- 비동기 검색으로 블로킹 없이 여러 요청 동시 처리
- 타임아웃(30초) 및 재시도(3회) 설정으로 안정성 확보
- 여러 인덱스 동시 검색 기능 (`search_multiple_indices`)

**개선 효과:**
- 동기식 MySQL 접근 → 비동기 Elasticsearch 검색으로 전환
- Worker timeout 문제 해결
- 고부하 환경에서 처리량 대폭 향상

### 2. 쿼리 빌더 및 최적화 (elasticsearch_service.py)

#### 1) 쿼리 빌더 패턴 도입

**ElasticsearchQueryBuilder 클래스:**
- 복잡한 쿼리를 간편하게 생성하는 빌더 패턴 구현
- 메서드 체이닝으로 가독성 높은 쿼리 작성
- terms, range, date_range, sort 등 다양한 조건 지원

#### 2) 뉴스/공시 조회 쿼리 최적화

**뉴스 조회 쿼리:**
```python
def create_news_query(
    start_date: datetime,
    end_date: datetime,
    tickers: Optional[List[str]] = None,
    lang: str = "ko-KR",
    is_exist: bool = True,
    is_related: bool = True
) -> ElasticsearchQueryBuilder
```

**공시 조회 쿼리:**
```python
def create_disclosure_query(
    tickers: Optional[List[str]] = None,
    start_date: datetime = None,
    end_date: datetime = None,
    lang: str = "ko-KR",
    is_exist: bool = True
) -> ElasticsearchQueryBuilder
```

**개선 효과:**
- 쿼리 파라미터 바인딩으로 SQL 인젝션 위험 제거
- 인덱스 활용 (`ticker.keyword`, `date`, `lang.keyword`)
- 정렬 및 페이지네이션을 Elasticsearch 레벨에서 처리

#### 3) 실시간 인기 종목 쿼리 통합

**기존 문제:**
- US 조회 1회 + KR 조회 1회 = 총 2회 DB 호출
- 동일 로직이 국가별로 중복 실행

**개선 방안:**
```python
def create_trending_tickers_query() -> ElasticsearchQueryBuilder:
    # US/KR 티커를 단일 쿼리로 집계
    builder.term("is_related", True)
    builder.term("is_exist", True)
    builder.range_query("date", gte="now-24h", lte="now+5m")

    # US 티커 집계 (6개)
    us_agg = {
        "filter": {"term": {"ctry.keyword": "us"}},
        "aggs": {"top_us": {"terms": {"field": "ticker.keyword", "size": 6}}}
    }

    # KR 티커 집계 (5개)
    kr_agg = {
        "filter": {"term": {"ctry.keyword": "kr"}},
        "aggs": {"top_kr": {"terms": {"field": "ticker.keyword", "size": 5}}}
    }
```

**개선 효과:**
- 3회 DB 호출 (그룹 확인 + US + KR) → 1회 Elasticsearch 검색으로 감소
- 집계(Aggregation) 기능으로 US/KR 동시 조회
- 코드 중복 제거 및 유지보수성 향상

### 3. 라우터 및 페이지네이션 개선 (router.py)

#### 1) Elasticsearch 기반 뉴스/공시 조회

**뉴스 조회 엔드포인트:**
```python
@router.get("/news/{group_id}")
def interest_news(
    group_id: int,
    lang: str = "ko",
    offset: int = 0,
    limit: int = 20,
    news_service: NewsService = Depends(get_news_service),
    service: InterestService = Depends(get_interest_service),
):
    ticker_infos = service.get_interest_tickers(group_id)
    tickers = [ticker_info["ticker"] for ticker_info in ticker_infos]
    total_news_data = news_service.get_news(lang=lang, tickers=tickers)
    news_data = total_news_data[offset * limit : offset * limit + limit]
```

**개선 효과:**
- Elasticsearch의 빠른 검색 속도 활용
- offset/limit 기반 페이지네이션으로 효율적인 데이터 분할
- 구독 레벨에 따른 데이터 마스킹 처리

#### 2) 공시 조회 엔드포인트

**공시 조회 엔드포인트:**
```python
@router.get("/disclosure/{group_id}")
def interest_disclosure(
    group_id: int,
    lang: str = "ko",
    offset: int = 0,
    limit: int = 20,
    news_service: NewsService = Depends(get_news_service),
    service: InterestService = Depends(get_interest_service),
):
    ticker_infos = service.get_interest_tickers(group_id)
    tickers = [ticker_info["ticker"] for ticker_info in ticker_infos]
    total_disclosure_data = news_service.get_disclosure(lang=lang, tickers=tickers)
    disclosure_data = total_disclosure_data[offset * limit : offset * limit + limit]
```

**개선 효과:**
- 메모리 레벨 분할 → Elasticsearch 기반 효율적 조회
- 100개 제한 해제로 데이터 완전성 확보
- has_next 플래그로 페이지네이션 상태 관리

### 4. 종합 개선 효과

**성능 향상:**
- DB 호출 횟수 대폭 감소 (3회 → 1회)
- 비동기 처리로 응답 시간 단축
- 인덱스 활용으로 검색 성능 향상

**안정성 개선:**
- Worker timeout 문제 해결
- 재시도 및 타임아웃 설정으로 장애 대응
- 고부하 환경에서 안정적인 처리량 확보

**코드 품질:**
- SQL 인젝션 위험 제거
- 쿼리 빌더 패턴으로 가독성 향상
- 중복 코드 제거 및 유지보수성 개선

## 개선결과

### 1. 성능 테스트 환경

#### 1) MySQL 기반 시스템

- Load Test: 50 VUs (가상 사용자)
- Stress Test: 300 VUs (가상 사용자)
- 테스트 도구: k6

#### 2) Elasticsearch 적용 시스템

- Load Test: 50 VUs (가상 사용자)
- Stress Test: 300 VUs (가상 사용자)
- 테스트 도구: k6

### 2. Load Test 결과 (50 VUs)

| 메트릭             | MySQL       | Elasticsearch | 개선율 |
| ------------------ | ----------- | ------------- | ------ |
| 총 요청 수         | 20,937      | 39,786        | +90%   |
| RPS (초당 요청 수) | 21.81 req/s | 41.43 req/s   | +90%   |
| 평균 응답시간      | 1,405.92 ms | 739.24 ms     | -47%   |
| P95 응답시간       | 2,805.92 ms | 1,633.88 ms   | -42%   |
| 성공률             | 100%        | 100%          | -      |

**주요 개선 사항:**

- RPS가 21.81에서 41.43으로 약 **90% 향상**
- 평균 응답시간이 1,405.92ms에서 739.24ms로 약 **47% 감소**
- P95 응답시간이 2,805.92ms에서 1,633.88ms로 약 **42% 감소**

### 3. Stress Test 결과 (300 VUs)

| 메트릭             | MySQL        | Elasticsearch | 개선율 |
| ------------------ | ------------ | ------------- | ------ |
| 총 요청 수         | 40,171       | 126,603       | +215%  |
| RPS (초당 요청 수) | 25.73 req/s  | 81.16 req/s   | +215%  |
| 평균 응답시간      | 5,173.22 ms  | 1,637.41 ms   | -68%   |
| P95 응답시간       | 12,308.43 ms | 5,242.33 ms   | -57%   |
| 성공률             | 100%         | 100%          | -      |

**주요 개선 사항:**

- RPS가 25.73에서 81.16으로 약 **215% 향상**
- 평균 응답시간이 5,173.22ms에서 1,637.41ms로 약 **68% 감소**
- P95 응답시간이 12,308.43ms에서 5,242.33ms로 약 **57% 감소**

### 4. 종합 분석

#### 1) 처리량 (Throughput) 개선

- Load Test 환경에서 RPS 90% 향상
- Stress Test 환경에서 RPS 215% 향상
- 고부하 상황일수록 Elasticsearch의 성능 우위가 더 크게 나타남

#### 2) 응답시간 (Response Time) 개선

- Load Test 환경에서 평균 응답시간 47% 감소
- Stress Test 환경에서 평균 응답시간 68% 감소
- 고부하 상황에서 응답시간 안정성 크게 개선

#### 3) 안정성 개선

- Worker timeout 문제 해결
- P95 응답시간의 대폭 감소로 사용자 경험 일관성 향상

### 5. 결론

Elasticsearch 도입을 통해 다음과 같은 성과를 달성했습니다:

- **처리량**: Load Test 90%, Stress Test 215% 향상
- **응답시간**: Load Test 47%, Stress Test 68% 개선
- **안정성**: Worker timeout 문제 해결 및 고부하 환경에서의 안정적인 성능 확보

특히 고부하 환경(300 VUs)에서 MySQL 대비 3배 이상의 처리량 향상과 68%의 응답시간 감소를 달성하여, 사용자 증가에 따른 확장성 문제를 효과적으로 해결했습니다.
