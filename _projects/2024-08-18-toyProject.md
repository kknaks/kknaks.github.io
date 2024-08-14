---
layout: project
title:  "123412341"
summary: "12341324"
author: 이건학
date: '2021-02-28 1:35:23 +0530'
category: []
tags: jekyllsdfdf
thumbnail: /assets/img/posts/code2.jpeg
keywords: devlopr jekyll, how to use devlopr, devlopr, how to use devlopr-jekyll, devlopr-jekyll tutorial,best jekyll themes, multi categories and tags
usemathjax: false
permalink: /project/adding-categories-tags-in-posts/
---
## 1\. 네이버 클라우드 부트캠프란 ?

  - 네이버 클라우드가 주관하고 비트캠프에서 진행하는 부트캠프

[##_Image|kage@tvCwP/btsHCtMfqZW/sb9C83lz6HiC7cs9cVhQj0/img.png|CDM|1.3|{"originWidth":800,"originHeight":832,"style":"alignCenter","width":580,"height":603,"caption":"출처 : https://bitcamp.co.kr/class/227?sca=N%EC%BA%A0%ED%94%84_Devops"}_##]

## 2\. Web Application 이란?

###   2.1 컴퓨터의 구성 

     - 컴퓨터는 크게 2가지 HardWare(H/W)와 SoftWare(S/W)로 나누어져 있다.

     - 하드웨어 :  CPU, HDD, RAM과 같은 장치로 컴퓨터의 물리적 부분을 말한다.

    - 소프트웨어 :  컴퓨터 프로그램과 여기에 수반되는 문서 등으로 컴퓨터 시스템을 말한다.

[##_Image|kage@cTDc0c/btsHCT4V9De/fOAv1Sp0y1a7yvh6Y5r81K/img.png|CDM|1.3|{"originWidth":1211,"originHeight":1000,"style":"alignCenter","width":580,"height":479,"caption":"그림 2.1 컴퓨터의 구성요소"}_##]

###   2.2 S/W의 구성 

     - S/W는 System S/W와 Application S/W 로 구분할수 있다.

     - System S/W : H/W를 제어하는 소프트웨어를 말한다.(ex. Os, Driver, Embedded, IoT)

     - Application S/W : 응용프로그램으로 System S/W를 제외한 소프트웨어를 말한다.(ex. ms-office, LoL)

[##_Image|kage@caE1hS/btsHBY7ixMv/KtSYKe7syou3CxSJZp2JS0/img.png|CDM|1.3|{"originWidth":1440,"originHeight":765,"style":"alignCenter","width":580,"height":308,"caption":"그림 2.2 S/W의 구성요소"}_##]

###   2.3 Application의 구성 

     -  Application 은 Standalone와 Client/Sever로 나뉜다.

     - Standalone : 외부 S/W의 도움없이 설치 후 독자적으로 실행가능한 App (ex. MS-office)

     - Client/Sever : 외부 S/W와의 통신을 통해 실행되는 App(ex. LoL)

[##_Image|kage@vCxFU/btsHBlWfvNw/NK5hkpwIT4qWdCL5HcZ6Bk/img.png|CDM|1.3|{"originWidth":1440,"originHeight":724,"style":"alignCenter","width":580,"height":292,"caption":"그림 2.3 APP의 구성요소"}_##]

###   2.4 Client/Sever의 구성 

     - Client/Sever는 설치형과 비설치형으로 구분한다.

     - 설치형 : 프로그램을 다운 받아 실행하여 외부와 통신하는 방식이다.

     - 비설치형 : 프로그램 다운로드 없이 WebBrower을 통해 실행가능한 방식이다.(Web Application)

[##_Image|kage@clDE1k/btsHBpYHX1e/bJ967j4ojvZ6Nhs8k3JY90/img.png|CDM|1.3|{"originWidth":1440,"originHeight":603,"style":"alignCenter","width":580,"height":243,"caption":"그림 2.4 Client/Sever의 구성요"}_##]

###   2.5 결론 : Web Application 이란?

     - Web Application은 다운로드 받지않고, 통신을 통해 **웹 브라우저위에서 이용할 수 있는 소프트웨어**를 말한다.

[##_Image|kage@dkNuKl/btsHCxVqTCx/f3wV5J6SgQBnuzEOkK4xt0/img.png|CDM|1.3|{"originWidth":1440,"originHeight":841,"style":"alignCenter","width":580,"height":339,"caption":"그림 2.5 WebApplication 이란?"}_##]

## 3\. Git 설치하기(Window버젼)

###   3.1 Git 다운로드

     - git 홈페이지에 접속하여 윈도우 64bit 버젼으로 다운 후 설치(GUI버전이 아니라 CLI버전으로 다운로드)

[##_Image|kage@cTRcWR/btsHBVCHE4j/D9bO8cEP64T9DCWZ3rOo10/img.png|CDM|1.3|{"originWidth":984,"originHeight":537,"style":"alignCenter","width":580,"height":317,"caption":"그림 3.1 Git 홈페이지"}_##]

[https://git-scm.com/download/win](https://git-scm.com/download/win)

 [Git - Downloading Package

Download for Windows Click here to download the latest (2.45.1) 32-bit version of Git for Windows. This is the most recent maintained build. It was released 9 days ago, on 2024-05-14. Other Git for Windows downloads Standalone Installer 32-bit Git for Wind

git-scm.com](https://git-scm.com/download/win)

     - CLI란 : 명령줄 인터페이스(CLI)는 Command-Line Interface 또는 Character User Interface의 줄임말로 글자를 입력하여 컴퓨터에 명령을 내리는 방식이다.

     - Cli를 사용 :  자원을 적게 사용하여 안정성, 속도, 용량측면에서 서버 작업과 같은 원격작업 시에 효율성이 좋다.

[##_Image|kage@1oBMc/btsHCW1HMpW/eMv6FJ065uQAV6eWoOBcq1/img.png|CDM|1.3|{"originWidth":500,"originHeight":394,"style":"alignCenter","width":580,"height":457,"caption":"그림 3.2 git 설치 파일"}_##]

###   3.2 Git Bash실행 및 파일 생

     - 설치 완료 후 Git bash를 실행하면 다음과 같은 화면이 뜬다.

[##_Image|kage@bZFL4H/btsHBoS4bTV/C3FR9kw0LyDK5qXWZifRCk/img.png|CDM|1.3|{"originWidth":899,"originHeight":540,"style":"alignCenter","width":580,"height":348,"caption":"그림 3.3 git 실행화면"}_##]

     - 이후 pwd로 현재 디렉토리를 살펴보면 root에 위치하고 있는 것을 확인 할수 있다.

     - cd를 입력하여 사용자(여기서는 BIT) 폴더로 이동한다.

     - mkdir(폴더 생성 명령어)를 입력하여 git 폴더를 생성한다. 

     - ls를 입력하면 BIT폴더에 git폴더(여기서는 안보임;;)이 생성된것을 확인 할수 있다. 

[##_Image|kage@cEDUG9/btsHBQaEIHK/WKkctVJdg1K60ZjLXkFDYK/img.png|CDM|1.3|{"originWidth":899,"originHeight":541,"style":"alignCenter","width":580,"height":349,"caption":"그림 3.4 사용자폴더에 git폴더 생성하기"}_##]

###   3.3 Git Clone

     - git clone + 레포주소를 입력하여 내 컴퓨터에 git레포를 생성한다.

[##_Image|kage@oGI3T/btsHCQNZ4wJ/71X7kHQbko8CvabL00p0Z0/img.png|CDM|1.3|{"originWidth":897,"originHeight":538,"style":"alignCenter","width":580,"height":348,"caption":"그림 3.5 git clone을 통해 레포파일 생성","filename":"blob"}_##][##_Image|kage@bCsE6r/btsHB9tWryz/MEh3Fhq0CliHOpZ2vMAkFK/img.png|CDM|1.3|{"originWidth":1120,"originHeight":630,"style":"alignCenter","width":580,"height":326,"caption":"그림 3.6 내 컴퓨터에 레포가 생성된 폴더"}_##]

###   3.4 Git Pull

[##_Image|kage@cgy6Hs/btsHAPQ4GOf/DRo6goj0nzakQzFLSy3uD1/img.png|CDM|1.3|{"originWidth":900,"originHeight":537,"style":"alignCenter","width":580,"height":346,"caption":"그림 3.7 git pull를 사용하여 최신화","filename":"blob"}_##]

     - git폴더에서 bitcamp-study로 이동하여 git pull을 실행한다.(현재는 이미 pull이 된 상태)
