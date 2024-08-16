---
layout: post
title:  "오목프로그램"
summary: "토이프로젝트4"
author: 이건학
date: '2021-02-28 1:35:23 +0530'
category: ["ToyProject"]
tags: 토이프로젝트4
thumbnail: /assets/img/posts/project_thumbnail/project_toy.png
keywords: 오목프로그램
usemathjax: false
permalink: /project/project4/
---
# Toy_Project

- Java 기초프로그래밍 중 4번째 프로젝트
- 오목프로그램 만들기

## 프로젝트 소개

- 서버와 클라이언트 상호 통신을 기반한 온라인 오목프로그램
- 쓰레드를 적용하여 독립적인 게임생성 및 상호게임 진행

## 개발환경 및 적용기술

- Language & IDE</li>

  ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white) ![IntelliJ IDEA](https://img.shields.io/badge/IntelliJIDEA-000000.svg?style=for-the-badge&logo=intellij-idea&logoColor=white)
- 적용기술 및 패턴
    1) **Network** : Sever Class와 Client Class를 각각 생성하여 Socket을 data 통한 통신
    2) **Thread** : 게임 생성 및 게임 진행을 Thread을 통한 실행으로 독립적인 게임 기능 구현
    3) **Observer 패턴** : 앱 실행 및 종료 시 객체에게 알림 기능 추가

## 목차

- [아키텍처](#아키텍처)
- [기능](#기능)
    - [서버](#서버)
    - [클라이언트](#클라이언트)
- [결과](#결과)
- [보완사항](#보완사항)

## 아키텍처

### 실행 흐름도

![flow](https://github.com/user-attachments/assets/8df66959-f984-458d-b6cf-e2b56b4b5905)

### 서버 UML

![serverUml](https://github.com/user-attachments/assets/e4db89f5-6f28-42a8-9842-a71b6347ee07)

### 클라이언트 UML

![ClientUml](https://github.com/user-attachments/assets/eb51d57a-c1aa-4a03-ba36-26104c6266f5)

## 기능

### 서버

- **방 생성 및 관리** : 클라이언트의 요청을 받아 새로운 방을 생성하고 관리
    - `ServerApp.execute()` : `RoomServerRunnable`스레드를 실행
    - `RoomServerRunnable`  : 스레드를 통해 독립적으로 방 관리 실행
        - 호스트의 (포트8888)로 방 생성 및 제거를 ArrayList<Room>형태로 관리
        - 클라이언트가 방을 생성 하면 `GameServerRunnable`스레드를 실행

- **게임 시작**: 각 방에서 게임을 진행하고 클라이언트 간의 통신을 관리
    - `GameServerRunnable` : 클라이언트가 생성한 방을 독립적으로 실행
        - 소켓에 클라이언트가 접속하면 `ClientHandler` 스레드를 실행
        - 한 소켓에 2개의 클라이언트가 접속할 때 까지 대기
        - 2개의 클라이언 접속 후 2개의 스레드와 통신을 하며 게임 진행

- **게임 진행**: 각 방에서 게임을 진행하고 클라이언트 간의 통신을 관리
    - `ClientHandler` : 게임에 직접적인 연결을 하는 스레드, 클라이언트의 요청내용을 수행
        - 스레드를 생성하면서 클라이언트과 데이터 소켓 연결
        - sendMessage, broadcastMessage, handlePlayerInput을 통해 통신
        -
            - 턴제 게임 특성상 서버와 통신시 한쪽은 대기, 한쪽은 실행하는 스레드로 구성
        - syncronized로 사용하여 동기화 진행
    - `Game` : 직접적으로 게임을 진행하고 결과를 리턴
        - DFS를 통해 게임의 승리 조건 판별

### 클라이언트

- **클라이언트 초기화**: 클라이언트 애플리케이션을 초기화하고 메뉴들을 연결
    - `ClientApp.main()` : 메뉴그룹과 메뉴아이템을 통해 메뉴 관리

- **방 생성**: 새로운 방을 생성하고 서버에 요청
    - `MakeRoomCommand`
        - `RoomServerRunnable`연결하는 소켓(포트8888) 생성
        - 기본적인 Room을 세팅하여 서버에 송신
        - Room의 포트번호는 9000번~ 시작하여 생성 시 static 변수로 증가
        - 방 생성 후 자동으로 `MultiGameCommand`로 이동

- **방 목록 요청 및 참여**: 서버로부터 방 목록을 요청하고, 특정 방에 참여
    - `JoinGameCommand`
        - `RoomServerRunnable`연결하는 소켓(포트8888) 생성
        - 서버와 연결 후 기존에 생성 된 방 목록을 받아 입장 할 방을 선택
        - Room class의 소켓 필드값을 받아 `GameServerRunnable`스레드에 접속
        - 자동으로 `MultiGameCommand`로 이동


- **게임 진행**: 서버와의 통신을 통해 게임을 진행
    - `MultiGameCommand` : 실제 클라이언트들이 게임을 진행
        - 턴제 게임 특성상 서버와 통신시 한쪽은 대기, 한쪽은 실행하는 스레드로 구성
        - syncronized로 사용하여 동기화 진행
        - 서버와 데이터 통신을 통해 진행 사항을 송신하고, 결과를 수신

## 결과

![화면 기록 2024-08-03 오전 12 48 32](https://github.com/user-attachments/assets/a605a9ea-237a-4389-aa3b-56e7959ee7cb)

## 보완사항

- 서버와 통신 할때 
