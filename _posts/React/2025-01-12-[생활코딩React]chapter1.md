---
title: "(생활코딩 React Programing) 1장 리액트 기초"
date: 2025-01-12 1:35:23 +0900
updated: 2025-01-12 1:35:23 +0900
category: React
tags: [리액트, 독학]
permalink: /React/chapter1/
---

## 4. 컴포넌트 만들기
## 5. props
## 6. 이벤트
- 컴포넌트에 이벤트를 추가해야 한다.
- 이벤트에 콜백 함수로 들어간 함수가 호출 될때는 event 객체가 전달된다.
- Header에 props로 전달된 onChangMode가 가리키는 함수를 실행한다. 

  ```javascript
  function Header(props) {
    console.log(props);
    return (
      <header>
        <h1><a href='/' onClick={function (event){
          //onclick시 페이지가 새로고침 되는 것을 막는다.  
          event.preventDefault(); 
          props.onChangeMode();
        }}>{props.title}</a></h1>
      </header>
    )
  }
  ```

## 7. state
- state는 컴포넌트가 가지고 있는 값이다.
- 컴포넌트는 입력과 출력이 있는데, 입력은 prop이고 prop을 통해서 입력된 데이터를 return을 통해서 출력한다.
- state는 prop과 함께 컴포넌트 함수를 다시 실행해서 새로운 return을 만들어낸다.
- prop과 state 모두 값이 변경되면 새로운 리턴 값을 만들어서 UI를 업데이트 한다. 
  ![image](https://github.com/user-attachments/assets/3c1a1c8a-718a-4ea1-9d8e-fb52bfc03a70)

### state의 구조
- state를 사용하려면 useState라는 함수를 사용해야 한다.
  
  ```javascript
  import {useState} from "react";
  ```

- useState 함수는 배열을 리턴한다. 
  > 첫번째 원소는 현재 상태, 두번째 원소는 상태를 바꿔주는 함수이다.<br> 
  > 이 함수를 호출하면 컴포넌트를 다시 렌더링한다. <br>
  > useState 함수의 인자로 객체를 넣어주면 객체 형태로 상태를 관리할 수 있다.<br> 
  ```javascript
  function App() {
      const _mode = useState('WELCOME');
      console.log(_mode);
      // 생략
  }
  ```

### mode의 사용법
- useState의 인자는 그 state의 초기값이다.
- mode를 사용하려면 _mode[0]을 사용해야 한다.

  ```javascript
  const mode = _mode[0];
  ```
- mode를 바꾸려면 _mode[1]을 사용해야 한다.
  
  ```javascript
  const setMode = _mode[1];
  ```
  
- 이를 좀더 간편하게 다음과 같은 구조로 사용한다.
  
  ```javascript
  const [mode, setMode] = useState('WELCOME');
  ```
