---
title:  8장 웹사이트에 접속하기 위한 네트워크 흐름
date: 2024-12-06 1:35:23 +0900
category: [network]
tags: network, sc기초
permalink: /blog/network08/
---
# 웹사이트에 접속하기 위한 네트워크 흐름
## OSI 7 계층 구조 & 네트워크의 흐름
- OSI 7 계층 구조는 다음과 같다.
![image](https://github.com/user-attachments/assets/b486b894-c86b-421c-8679-80b8a2d18a7d)

- 기본적인 통신의 흐름은 다음과 같다. 
  > LAN에서의 통신에는 스위치 장비가 MAC주소로 통신한다. <br>
  > WAL에서는 라우터를 이용해 통신을 하며 IP주소로 통신한다. <br>
  > 송신자는 7계층에서 1계층으로 이동하면서 데이터를 만든다. <br>
  > 수신자는 1계층에서 7계층으로 이동하면서 데이터를 출력한다. 

  ![image](https://github.com/user-attachments/assets/2ba252ea-95e9-4ba0-b0e2-9b140c79ca1d)


## 웹브라우저에서 데이터 검색하기 
- 사용자 입장에서 https://www.google.com을 입력하는 예제 

### 데이터를 보내는 송신자 관점에서 네트워크 이해하기 
#### 1. 응용계층
- 웹브라우저 : 사용자가 서버와 3방향 핸드셰이크로 연결이 확립된 상태 
- http 프로토콜을 사용한다는 것은 80번 포트를 사용한다는 것이다.
- 데이터(index.html)을 L4로 보내야한다. 
- 페이지를 요청하기 위해 http 요청(메서드, 경로/파일명, HTTP버전)을 L7에서 정의한다.

  ![image](https://github.com/user-attachments/assets/1d16f7db-3daf-482b-b270-92c2bc81243b)

### 2. 전송계층
- 정의한 데이터를 헤더와 포트번호를 추가하여 전송 계층에 전달한다. 
- TCP 헤더를 추가하고 데이터를 합쳐서 세그먼트로 캡슐화를 진행한다.
- 수신자 포트 서버의 80번 포트를 사용하고 송신자는 데이터를 받을대 임의의 포트로 데이터를 받는다.

  ![image](https://github.com/user-attachments/assets/8d20f467-9821-406b-a6f3-c9fa17a404c3)

### 3. 네트워크 계층
- 네트워크에서 IP주소에 대한 헤더를 붙인다. 
- 라우터를 통해서 상대 IP주소를 알아낸다. 
- 나의 IP주소와 상대방의 IP주소를 헤더에 추가하고 이렇게 추가된 데이터를 패킷이라고 부른다.

  ![image](https://github.com/user-attachments/assets/49f417df-29cc-4b9a-81bf-b79bd192ed2a)

### 4. 데이터 링크 계층
- 패킷을 데이터 링크 계층으로 전달을 한다. 
- 헤더에는 송신자와 수진자의 MAC주소가 포함되어 있어야한다. 
- 다른 네트워크간의 통신의 경우 송신자의 컴퓨터는 송신자가 속한 라우터 MAC주소로 기입하여, 라우터에서 수신자 IP를 찾아 가게 해야한다.
- MAC주소까지 추가한 데이터를 프레임이라고 한다. 

  ![image](https://github.com/user-attachments/assets/2ef58137-e6d1-456f-9b72-45639ec6195f)

### 5. 물리 계층
 - 랜카드를 통해 생성된 프레임을 전기신호로 변환 한다.
  ![image](https://github.com/user-attachments/assets/5bc9cb16-e8da-40d9-b7ae-1e59f45356f4)

## 장비 관점에서의 데이터 흐름 이해하기 
- 송신자에서 보낸 데이터 프레임은 스위치와 라우터를 거쳐 전송 된다. 

  ![image](https://github.com/user-attachments/assets/f3f8ba8d-ad37-4902-91c1-21a4a0cb2b7c)

### 1. 스위치
- 컴퓨터에서 받은 전기신호를 프레임으로 전환한다. 
- 스위치도 수신자의 MAC주소를 알수 없기 때문에 송신자의 라우터 MAC주소를 사용한다. 
- 전기 신호를 사용하여 라우터에게 전달한다. 

  ![image](https://github.com/user-attachments/assets/3f6f7035-2476-44b4-b355-5f42336e3aef)

### 2. 라우터
- 넘어온 데이터에서 수신자의 IP를 확인하기 위해 헤더를 뜯어보게 된다.
- 데이터 링크계층에서 수신자의 MAC주소 부분이 자신의 MAC주소와 같으면이더넷 헤더를 떼어내고 데이터를 네트워크 계층에 전달합니다.
- WAN 통신을 위해 라우터 간의 사용 가능한 IP 주소로 바꾼다. 

  ![image](https://github.com/user-attachments/assets/973860eb-2ffe-46c7-8a92-f75acda58da6)

- 네트워크 계층에서 데이터 링크로 패킷을 보내며, 송신자의 MAC 주소는 송신자 라운터의 MAC주소로 변경하고, 수신자의 MAC 주소는 수신자의 MAC주소로 변경한다.
  ![image](https://github.com/user-attachments/assets/ebc9a3bf-fb18-4109-a2a2-62a45ab5d737)

### 3. 라우터(수신자측)
- 수신자는 다음과 같은 과정으로 데이털 받는다.

  ![image](https://github.com/user-attachments/assets/1803a036-018e-42d7-864a-724f89b23638)

- 수신자의 MAC주소 부분이 라운터 MAC주소와 동일하면, 이더넷 헤더를 분리후 패킷을 네트워크 계층으로 보낸다.
- 수신자가 데이터를 반환 할때 라우터 B가 받아야 하므로 송신자의 IP를 라우터 B의 내부 통신 IP로 변경한다.

  ![image](https://github.com/user-attachments/assets/1636e698-ee8d-4c15-82b8-d532b017d705)
- 라우터가 데이터를 받음므로 송신자 MAC주소는 라우터의 주소를 사용하고 수신자 주소는 라우터에 저장된 수신자의 MAC주소를 사용한다. 

  ![image](https://github.com/user-attachments/assets/db169d15-b3d3-4a7c-9b24-87fd994d2e5b)

### 4. 스위치(수신자측)
- 송신자측과 동일 한 방법으로 데이터를 변환하고 수신자에 컴퓨터에게 전기 신호를 보낸다.

## 데이터를 받는 수신자 관점에서 네트워크 이해하기 
### 1. 물리 계층
- 전기 신호를 데이터 링크 계층으로 보낸다. 

### 2. 데이터링크 계층
- 프레일에서 이더넷 헤더의 수신자 MAC주소가 맞다면 이더넷 헤더를 분리하여 네트워크 계층으로 보낸다.

### 3. 네트워크 계층
- 수신자의 IP주소를 확인하고 동일하면 IP헤더를 분리하여 전송계층으로 전달한다. 

### 4. 전송계층
- TCP 헤더를 확인하여 수신자 포트를 추출하고 해당 포트에 데이터를 전달한다. 

### 5. 응용계층
- 해당 포트로 들어온 데이터를 확인하고 다시 반환한다. 
