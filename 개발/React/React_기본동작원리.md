# ⭐React⭐
**`React`**는 UI를 구성하기 위한 JavaScript 라이브러리
공식 홈페이지에도 다음과 같이 소개되어 있다
>**“React is a JavaScript library for building user interfaces”**

<br>

## 🍅React의 구조
>[1] React Core
   ├── React.createElement() : JSX 파싱 결과
   ├── Component (Function or Class)
   └── Hooks API (useState, useEffect 등)
>
[2] ReactDOM (or React Native)
   └── 실제 플랫폼 별 DOM 트리와 연결/렌더링
>
[3] Reconciler
   └── Virtual DOM 비교 & 실제 DOM 업데이트 지시
>
[4] Renderer (Fiber 엔진)
   └── 작업 스케줄링 및 비동기 렌더링

<br>

### 🔍 설명
#### 1-1. React Core
>가장 기본적인 로직이 모인 부분
**`JSX`**는 이 레벨에서 `React.createElement()`로 변환됨
컴포넌트는 실제 트리의 최소 단위로 관리됨
상태와 생명주기는 `Hook`이나 `클래스 컴포넌트`가 관리

<br>

#### 1-2. ReactDOM / React Native
>플랫폼에 따라 다름
**웹**이면 **`ReactDOM`**이 `document.createElement` 등으로 실제 DOM 조작
**모바일**이면 **`React Native`**가 `native 컴포넌트`로 변환

<br>

#### 1-3. Reconciler
>**이전 Virtual DOM** vs **새 Virtual DOM **비교
어떤 DOM만 바꿔야 할지 결정하는 **<span style=color:red>“차이 계산 엔진”</span>**
실제로는 **트리 순회 + key 비교** 등의 알고리즘 사용

<br>

#### 1-4. Renderer (Fiber)
>React 16부터 도입된 렌더링 아키텍처
React가 각 컴포넌트를 추상화해서 관리하는 **데이터 구조(객체)**이자, React의 렌더링 엔진에서 사용하는 **작업 단위(Task Unit)**
**작업 단위(Task Unit)**로 분할 → 렌더링 중 UI 멈춤 방지
시간 소모 작업은 나눠서 처리함 (일종의 cooperative multitasking)


<br>

### 🦾Babel이란?
📌 **`Babel`**은 **JavaScript 컴파일러(Compiler)**
최신 `JavaScript` 문법이나 `JSX`, `TypeScript` 등을 → 브라우저가 이해할 수 있는 일반 `JavaScript` 코드로 변환해주는 도구
Babel은 **`@babel/preset-react`** 플러그인을 사용해 JSX 문법을 변환함

📍 왜 필요한가?
브라우저는 `JSX`, `TypeScript`, `ES6+` 문법 같은 걸 직접 이해하지 못 함


```
const element = <h1>Hello</h1>;
```
→ 그래서 Babel이 필요함
Babel은 위 코드를 일반 JavaScript로 변환 해줌

<br>

### 💧React의 전체 동작 흐름
#### 1. React에서 코드 작성
```
// 1. 컴포넌트 작성
function App() {
  return <h1>Hello</h1>;
}

// 2. ReactDOM 렌더링
ReactDOM.createRoot(document.getElementById('root')).render(<App />);
```

<br>

#### 2. 내부에서 일어나는 일
1️⃣ **JSX → Babel이 React.createElement로 변환**
```
React.createElement("h1", null, "Hello")
```

2️⃣ createElement → Virtual DOM 노드를 생성 (객체)
```
{
  type: 'h1',
  props: { children: 'Hello' },
}
```


3️⃣ Fiber가 이 객체를 작업 단위로 분할하고 큐에 저장

4️⃣ Reconciler가 이전 렌더링 결과와 **비교(diff)**

5️⃣ 변화된 부분만 **실제 DOM**으로 전달 (`ReactDOM` or` Native`)

6️⃣ 최종적으로 `document.createElement` 등으로 DOM 반영

<br>

### 🔑JSX란 정확히 무엇이고, 왜 쓰나?
**`JSX`**는 JavaScript의 확장 문법 
`HTML-like` 문법을 JS 안에 넣는 **<span style=color:red>문법적 설탕(Syntactic Sugar)</span>**

<br>

#### 왜 필요한가?
기존 방식은 UI를 JavaScript 객체로 표현해야 했음
```
React.createElement("h1", null, "Hello")
```
→ 직관성 떨어짐

<br>

#### JSX는 더 직관적
```
<h1>Hello</h1>
```

<br>

#### JSX 작동 과정
JSX는 브라우저에서 바로 작동하지 않음 
JS 문법이 아니기 때문에 Babel이 이를 JS로 변환 해줘야 함

>기본 변환 결과
```
<h1>Hello</h1>
// ↓ Babel 변환
React.createElement('h1', null, 'Hello')
```
결과적으로 **JS 객체(=Virtual DOM 노드)**가 반환됨

<br>

### ✅ 요약 (구조 → 동작 원리 흐름으로)
>1. 컴포넌트를 JSX로 작성
   ↓
2. Babel이 JSX → React.createElement로 변환
   ↓
3. React.createElement → Virtual DOM 노드 구성
   ↓
4. React Fiber 엔진이 작업을 분할해 Reconciler로 전달
   ↓
5. 이전 Virtual DOM과 비교 → 변화만 실제 DOM에 반영
   ↓
6. 최종적으로 ReactDOM이 브라우저 DOM에 반영

<br>

-----

## 🍅useState vs 일반 변수
🔁 일반 변수 (function 내부 변수)
```
function Counter() {
  let count = 0;

  function handleClick() {
    count += 1;
    console.log(count);
  }

  return <button onClick={handleClick}>Click</button>;
}
```
❌ 여기서 문제
count는 렌더링될 때마다 다시 0으로 초기화됨
React는 해당 값이 바뀐 걸 추적하지 못함
UI도 업데이트 안 됨

<br>

✅ useState 사용
```
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return <button onClick={handleClick}>Count: {count}</button>;
}
```
여기서 핵심은 useState가 단순한 값 저장이 아니라는 것

### 🧬 구조적인 차이
#### React가 상태를 “추적 가능한 메모리 슬롯”으로 관리함
React에서는 우리가 컴포넌트를 작성하고, 그 안에서 `useState()`를 사용하면 단순히 변수에 값을 저장하는 게 아님
React는 내부적으로 해당 상태를 **<span style=color:red>엄격하게 추적</span>**하고, 필요할 때 자동으로 UI를 다시 그릴 수 있도록 **<span style=color:red>구조화된 방식으로 상태를 관리</span>**

<br>

#### 💡 React가 어떻게 관리하는가?
1. 컴포넌트가 렌더링되면, React는 해당 컴포넌트에 대한 `Fiber Node`를 생성함
>React는 전체 UI 트리를 구성할 때, 각 컴포넌트를 `Fiber Node`라는 객체로 표현
이 `Fiber Node`는 해당 컴포넌트의 **타입**, **자식**, **부모**, **상태** 등을 모두 담고 있는 핵심 데이터 구조
**컴포넌트마다 고유한 Fiber**가 있고, 이 안에 상태와 관련된 정보도 들어 있음
>
쉽게 말하면 **_컴포넌트의 정보와 상태를 통으로 담고 있는 객체_**라는 의미

<br>

2. `useState()`를 호출하면 React는 이 `Fiber Node`에 `state 저장 슬롯`을 할당
>이 슬롯은 컴포넌트 내에서 선언된 `useState()`마다 하나씩 생성
각각의 `useState()`는 내부적으로는 `Linked List`의 노드처럼 연결된 구조로 저장
이 슬롯 안에는 현재 상태 **`값(state)`**, **`업데이트 큐(queue)`** 등의 정보가 담겨 있음
>
즉, `useState`는 단순한 변수 저장이 아니라, React 내부의 관리 가능한 **구조체**에 상태를 등록하는 것


<br>


3. `setCount()`가 호출되면, React는 다음을 수행
>✅ 1) 먼저 상태를 바꾸기 위한 “업데이트 객체”를 만듦
이 객체는 `queue`에 추가돼서 **<span style=color:red>다음 렌더링 때 적용</span>**됨
즉시 적용되지 않기 때문에 React는 **스케줄링**을 통해 최적의 타이밍에 렌더링을 수행함
>
✅ 2) 그리고 해당 컴포넌트를 `렌더링 대상`으로 표시한다
React는 상태가 바뀌었다는 것을 인식한 후, 해당 컴포넌트를 다시 실행해야 하는 대상으로 등록함
이걸 흔히 `mark`한다고 표현
이는 내부적으로는 해당 Fiber Node에 업데이트 플래그(flags)를 설정하는 것
>
`fiber.flags |= Update;`
쉽게 말해 **<span style=color:blue>이 컴포넌트는 다음에 다시 그려야 하니까 기억해놔!</span>**라는 뜻

<br>

4. React는 다음 렌더 사이클이 올 때 이 컴포넌트를 다시 실행
이것을 `재렌더링`이라 함
다시 `App()` 함수가 실행되고, `useState()` 호출도 반복됨
React는 이전에 저장해둔 슬롯을 **순서대로 재사용**해서 상태 값을 꺼내고, 새로운 UI 트리를 구성

<br>

### 💧전체 흐름 정리
>1. 컴포넌트가 처음 실행되면 → React는 컴포넌트를 위한 `Fiber Node` 생성
↓
2. `useState()`를 호출하면 → 그 Fiber 안에 `상태 저장 슬롯`이 생김
↓
3. `setCount()`를 호출하면 → 새로운 값이 저장되고, 해당 컴포넌트에 `업데이트 마크`
↓
4. React는 이 컴포넌트를 `재렌더링 스케줄`에 등록
↓
5. 다음 `렌더링 사이클`에서 → 컴포넌트를 다시 실행해서 UI를 업데이트


