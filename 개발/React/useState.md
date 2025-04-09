
# ⭐React⭐
![](https://velog.velcdn.com/images/tlaals44/post/c82fe4ec-2095-4117-b3b9-3180baadaaa5/image.png)
**html, css js를 사용한 웹페이지와 리액트로 만든 SPA의 구조 차이**

<br>

## 🍅SPA란?
**Single Page Application**
**SPA**는 한 번 페이지를 로딩한 뒤, 전체 페이지를 다시 불러오지 않고도 필요한 부분만 동적으로 갱신하는 웹 애플리케이션

**React**는 이런 **SPA** 구조를 구현하기 위한 프레임워크 중 하나
이때 가장 핵심이 되는 게 바로 상태(state) 관리

➡️ 어떤 데이터를 기준으로 화면(UI)이 동적으로 바뀌는가?
➡️ 이 데이터를 **React**에 알려주는 도구가 바로 **`useState`**

<br>

## 🍅컴포넌트 
UI를 구성하는 최소 단위
React에서는 화면을 작고 독립적인 컴포넌트 단위로 쪼개서 개발

#### 특징
>자신만의 상태(state) 를 가질 수 있고
자신의 props를 받아서 렌더링되고
자기 자신의 변경사항에만 반응해서 리렌더링됨

➡️ 전체 SPA를 한 번에 갱신하는 게 아닌, 변경된 컴포넌트만 다시 렌더링해서 효율적인 UI 업데이트가 가능

<br>

#### 요약
>**상태(state)**나 **props**(부모 컴포넌트가 자식 컴포넌트에게 전달하는 값)가 변경되면, React는 변경된 컴포넌트를 다시 렌더링
[Virtual DOM](https://velog.io/@tlaals44/Dom)에서 이전과 비교 (diffing)
실제 DOM에는 변경된 부분만 최소한으로 반영

<br>

## 🍅useState란?
React에서 컴포넌트의 상태(state)를 관리할 수 있게 해주는 함수(Hook)
즉, 값이 바뀌면 그걸 자동으로 감지해서 화면(UI)을 다시 그려주는 기능

>
```
const [state, setState] = useState(초기값);
```
**state**: 현재 상태값
**setState**: 상태를 변경하는 함수
**useState(초기값)**: 상태의 초기값 설정
React는 이 상태값이 바뀌면 자동으로 컴포넌트를 다시 렌더링
>
<br>
>
**사용 예시**
>
```
import { useState } from 'react';
>
function Counter() {
  const [count, setCount] = useState(0);
>
  return (
    <div>
      <p>현재 숫자: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

버튼을 클릭하면 `setCount()`로 상태를 바꾸고, **React**가 해당 컴포넌트를 자동으로 리렌더링

<br>

### 1️⃣ useState 사용 이유
SPA에서는 화면(UI)이 **<span style=color:red>동적으로 바뀌는 게 필수</span>**
✅ 상태가 바뀌면 → 그 상태를 기반으로 UI가 달라져야 함
✅ 그런데 그냥 **`let`**, **`const`**로는 값이 바뀌어도 화면이 안 바뀜


```
let count = 0;
count += 1;
// UI는 안 바뀐다!
```
➡ 그래서 **React**가 “상태 변화 감지 + 자동 UI 갱신”을 가능하게 하는 도구로 `useState`를 제공

<br>

### 2️⃣ useState 사용의 장점
**자동 리렌더링**: 값이 바뀌면 React가 컴포넌트를 자동으로 다시 그림

**선언적 UI**: "상태가 이러면 이런 화면 보여줘" 식으로 직관적으로 설계

**상태 유지**: 컴포넌트마다 자기 상태를 유지함 (예: 버튼 누른 횟수)

**SPA와 찰떡궁합**: 필요한 부분만 변경되는 React의 핵심과 잘 맞음

<br>

### 3️⃣ useState 없이 하면 어떤 문제?
```
let count = 0;
function increase() {
  count++;
  // 값은 바뀌지만 화면은 그대로
}
```
>상태 바뀐 걸 React가 모름
화면을 강제로 갱신해야 함 (`forceUpdate()` 같은 불안정한 방식 필요)
SPA의 장점인 자연스러운 UI 변화가 사라짐
유지보수가 어려워짐

<br>

### 4️⃣ 비교
| 항목 | useState 사용 | 일반 변수 사용 | 
| :- : | : - : | :-: |
| 값 변경 후 UI 반영 | 자동 | 수동으로 다시 그려야 함 |
| React가 추적 가능? | 가능 (리렌더링됨) | 불가능 | 
| 코드 구조	 | 	선언적, 깔끔함 | 명령적, 복잡해짐 | 
| 버그 발생 가능성 | 적음 | 많음 (상태 안 맞음) | 

<br>

-------
## 🍅컴포넌트 간 상태 공유 문제
```
App
├── Header
├── Sidebar
└── Content
    ├── PostList
    └── PostDetail
```
예를 들어 `PostList`에서 게시글을 클릭하면, `PostDetail`에서 그 내용을 보여줘야 한다
하지만 이 두 컴포넌트는 형제 관계
➡ 즉, 여기서는 데이터를 **`부모(App)`**로 **<span style=color:red>올렸다가 내려주는 방식(Lifting state up)</span>** 을 써야 함

```
// App.js
const [selectedPost, setSelectedPost] = useState(null);
<PostList setSelectedPost={setSelectedPost} />
<PostDetail post={selectedPost} />
```
이렇게 하면 점점 `props` 지옥이 되기 시작함
컴포넌트가 많아질수록, 상태 흐름이 꼬이고 유지보수가 어려워짐
그래서 등장한 **`전역 상태 관리 도구`**들
<br>

### 1️⃣ 전역 상태 관리
모든 컴포넌트가 하나의 중앙 저장소에서 상태를 읽고 쓸 수 있게 만드는 시스템

<br>

### 2️⃣ 대표적인 전역 상태 관리 도구 비교
| 이름 | 특징 | 언제 사용하면 좋은가? | 
| :- : | : - : | :-: |
| Redux | 가장 널리 쓰임, 엄격한 구조 (action, reducer 등) | 대규모 앱, 상태 흐름을 명확히 통제하고 싶을 때 |
| Recoil | Facebook 개발, React의 동작 방식과 찰떡 | 컴포넌트 단위로 세밀하게 상태 쪼개고 싶을 때 | 
| Zustand | 가볍고 단순한 API, 훅 기반 | 빠르게 기능 붙이고 싶을 때, 러프한 상태 관리 | 
| Context API | React 내장, 소규모 앱용 | 간단한 글로벌 데이터 공유 (ex. 다크모드, 로그인 등) | 

<br>

### 3️⃣ Recoil 간단 예제 (컴포넌트 간 상태 공유)
#### 구조
```
App
├── UserForm
└── UserProfile
```

<br>


#### 1. 아톰(atom) 정의
**아톰(atom)**: `Recoil`에서 상태(state)의 최소 단위, 컴포넌트 사이에서 공용 변수처럼 사용
```
// state/userState.js
import { atom } from 'recoil';

export const userState = atom({
  key: 'userState',
  default: { name: '', age: 0 },
});
```

<br>


#### 2. 값 쓰기
```
// UserForm.jsx
import { useRecoilState } from 'recoil';
import { userState } from './state/userState';

function UserForm() {
  const [user, setUser] = useRecoilState(userState);

  return (
    <input
      value={user.name}
      onChange={(e) => setUser({ ...user, name: e.target.value })}
    />
  );
}
```

<br>

#### 3. 값 읽기
```
// UserProfile.jsx
import { useRecoilValue } from 'recoil';
import { userState } from './state/userState';

function UserProfile() {
  const user = useRecoilValue(userState);
  return <p>이름: {user.name}</p>;
}
```
➡ 둘이 형제 컴포넌트인데도, 전역 상태 하나로 자연스럽게 연결됨

