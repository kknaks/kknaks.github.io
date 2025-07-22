---
title: "[모니터링] 프로메테우스 메트릭"
date: 2025-07-21 5:35:23 +0900
updated: 2025-07-21 5:35:23 +0900
category: codingTest
tags: CS지식, 메트릭 
permalink: /study/prometheus/
---
# Prometheus 기본 메트릭 종류와 역할

## 메트릭 타입별 분류

### Counter (카운터)
- **특징**: 단조 증가하는 누적 값을 측정 (0부터 시작하여 증가만 가능)
- **용도**: HTTP 요청 수, 에러 발생 횟수, 완료된 작업 수 등
- **예시**: 
  - `http_requests_total`
  - `errors_total`

### Gauge (게이지)
- **특징**: 현재 상태나 값을 측정 (증가/감소 모두 가능)
- **용도**: CPU 사용률, 메모리 사용량, 현재 연결 수, 온도 등
- **예시**: 
  - `cpu_usage_percent`
  - `memory_bytes`
  - `active_connections`

### Histogram (히스토그램)
- **특징**: 관측값들을 구간별로 분포를 측정하고 합계/개수 제공
- **용도**: 응답 시간 분포, 요청 크기 분포 등
- **예시**: 
  - `http_request_duration_seconds`
  - `request_size_bytes`

### Summary (요약)
- **특징**: 관측값들의 분위수(quantile)와 합계/개수 제공
- **용도**: 응답 시간의 95%, 99% 분위수 등
- **예시**: 
  - `http_request_duration_seconds{quantile="0.95"}`

---

## 인프라 기본 메트릭

### 시스템 리소스
| 메트릭명 | 설명 |
|---------|------|
| `node_cpu_seconds_total` | CPU 사용 시간 |
| `node_memory_MemAvailable_bytes` | 사용 가능한 메모리 |
| `node_memory_MemTotal_bytes` | 전체 메모리 |
| `node_filesystem_avail_bytes` | 디스크 사용 가능 공간 |
| `node_load1`, `node_load5`, `node_load15` | 시스템 로드 (1분, 5분, 15분) |

### 네트워크
| 메트릭명 | 설명 |
|---------|------|
| `node_network_receive_bytes_total` | 네트워크 수신 바이트 |
| `node_network_transmit_bytes_total` | 네트워크 송신 바이트 |
| `node_network_up` | 네트워크 인터페이스 상태 |

---

## 애플리케이션 기본 메트릭

### HTTP 서비스
| 메트릭명 | 설명 |
|---------|------|
| `http_requests_total` | 총 HTTP 요청 수 |
| `http_request_duration_seconds` | 요청 처리 시간 |
| `http_requests_in_flight` | 현재 처리 중인 요청 수 |
| `http_response_size_bytes` | 응답 크기 |

### 데이터베이스 (MySQL)
| 메트릭명 | 설명 |
|---------|------|
| `mysql_up` | MySQL 서버 상태 |
| `mysql_global_status_connections` | 총 연결 수 |
| `mysql_global_status_slow_queries` | 느린 쿼리 수 |
| `mysql_global_variables_max_connections` | 최대 연결 수 |

### JVM (Java 애플리케이션)
| 메트릭명 | 설명 |
|---------|------|
| `jvm_memory_bytes_used` | 사용 중인 JVM 메모리 |
| `jvm_gc_collection_seconds` | GC 수행 시간 |
| `jvm_threads_current` | 현재 스레드 수 |

---

## 메트릭의 역할과 활용

### 성능 모니터링
- 응답 시간, 처리량, 에러율을 통한 서비스 품질 측정
- 리소스 사용률을 통한 용량 계획 수립

### 장애 감지
- 임계값 기반 알림 설정으로 장애 조기 발견
- 트렌드 분석을 통한 잠재적 문제 예측

### 용량 계획
- 리소스 사용 패턴 분석으로 스케일링 시점 결정
- 비용 최적화를 위한 리소스 적정 사이즈 도출

### 비즈니스 인사이트
- 사용자 행동 패턴 분석
- 서비스별 성능 비교 및 개선점 도출

---

## 주요 포인트

> **Pull 방식의 장점**
> - 각 서비스의 성능 지표(CPU, 메모리, 응답시간)를 실시간으로 수집
> - 중앙 집중식 모니터링으로 일관된 메트릭 관리
> - Grafana 대시보드와 연동하여 직관적인 시각화 제공