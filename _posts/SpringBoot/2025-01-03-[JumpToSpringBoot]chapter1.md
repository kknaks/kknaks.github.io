---
title: "(점프 투 스프링부트) 1장 스프링부트 개발준비하기"
date: 2025-01-03 1:35:23 +0900
updated: 2025-01-03 1:35:23 +0900
category: SpringBoot
tags: [스프링부트, 독학]
permalink: /springboot/chapter1/
---

## 1.1 스프링부트란
- 스프링 부트(Spring Boot) 는 웹 프로그램(웹 애플리케이션)을 쉽고 빠르게 만들 수 있도록 도와주는 자바의 웹 프레임워크이다.

### 웹 프레임워크
- 정의 : 웹 프로그램을 개발할 때 필요한 기능들을 미리 만들어 놓은 것
- 장점 : 개발자가 필요한 기능을 직접 만들지 않아도 되기 때문에 개발 시간을 단축할 수 있다.
- 단점 : 프레임워크가 제공하는 기능만 사용할 수 있기 때문에 개발자가 원하는 기능을 추가하기 어렵다.

## 1.2 개발환경 준비하기(IntelliJ IDEA)
- intelliJ IDEA에서 스프링 부트 프로젝트 만들기
  - File > New > Project
  - Spring Initializr 선택
  - Group, Artifact, Name, Description 입력
  - Project SDK 선택
  - Spring Boot Version 선택
  - Dependencies 선택
  - Finish 클릭
    <img width="1298" alt="image" src="https://github.com/user-attachments/assets/c283514a-66f1-492d-adc3-1cbcd959e0ce" />

## 1.3 스프링부트 기초
- GET http://localhost:8080/hello 요청 시 ‘Hello World’라는 문구를 출력하기

### 웹서비스 동작원리
1. 클라이언트와 서버 구조
- 클라이언튼 URL로 IP를 요청한다.
- 웹서버는 클라이언트에게 해당 IP에 대한 html 파일을 전송한다.
  ![image](https://github.com/user-attachments/assets/2a4abc82-ac90-43ef-8da2-ba46822565fb)

2. Port
- port : 하나의 컴퓨터에 여러 개의 서버가 동작할 수 있도록 구분해주는 번호
- 하나의 IP주소를 가지고 여러개의 port를 통해 각각 서비스에 접속할 수 있다.
- ex) naver.com은 naver.com:80, naver.com:443 등으로 접속할 수 있다.

| 프로토콜     | 서비스내용                 | 포트  |
|----------|-----------------------|-----|
| HTTP     | 웹서비스                  | 80  |
| HTTPS    | SSL을 적용한 웹서비스         | 443 |
| FTP      | 파일 전송 서비스             | 21  |
| SSH,SFTP | 보안이 강화된 TELNET,FTP서비스 | 20  |
| Telnet   | 원격 서버 접속 서비스          | 23  |
| SMTP     | 메일전송 서비스              | 25  |


### 스프링 부트 컨트롤러 만들기 
- Controller의 기능 :
  > 웹 요청을 받아서 응답을 보내주는 역할 <br>
    코드에 적용된 GetMapping은 Get요청으로 들어오는 요청을 받는다. 
-  [코드](https://github.com/kknaks/likeLion_mystudy/commit/86fb158)
