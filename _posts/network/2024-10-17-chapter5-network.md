---
title:  5장 네트워크 계층, 목적지를 찾는 단계
date: 2024-10-17 1:35:23 +0900
category: [network]
tags: network, sc기초
permalink: /blog/network05/
---

# 네트워크 계층의 역할
## 1. 라이터
- **라우터** : 데이터가 어떤 경로로 전달되어야 하는 지 내비게이션과 같은 역할을 하는 네트워크 장비
- 라우팅 : 최적경로를 찾는 과정

  ![image](https://github.com/user-attachments/assets/d1d9c8af-7af0-4e0d-a8db-7660b6c1f459)

# IP주소
## 1. IP주소
- **IP(Internet Protocol)주소** : 주소는 인터넷 상에 있는 컴퓨터의 고유한 주소이다. 
- ISP(Internet Service Provider)이 제공하는 공인 IP주소를 사용한다. 
  - 공인IP : ISP가 제공하는 유동적인 IP주소로 고유하게 할당된다.
  - 사설IP : 내부망에 사용되는 아이피 주소로 밖에 네트워크와 통신할 수 없지만, 보안성이 좋다.

## 2. 네트워크주소
- 마지막 1바이트의 값의 0인 주소를 네트워크 주소라고 한다. 
- 같은 네트워크를 주소를 사용하는 컴퓨터들을 같은 IP대역을 사용한다라고 표현한다. 

![image](https://github.com/user-attachments/assets/330a5b93-5bb1-4500-bd6c-f76f31f1181e)

## 3. 브로트캐스트주소
- 마지막 1바이트의 값이 255인 주소를 브로드캐스트 주소라고 한다. 
- **브로드캐스트주소** : 네트워크에 연결된 컴퓨터에 데이터를 한번에 일고라적으로 전송 할때 사용하는 주소이다.
