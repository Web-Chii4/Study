## 🍅 React에서 렌더링이 실행되는 조건
#### React는 다음 세 가지 중 하나라도 변경되면 해당 컴포넌트를 다시 렌더링
>**`props`** 부모 컴포넌트가 전달하는 외부 데이터. 값이 바뀌면 자식 컴포넌트가 다시 렌더링됨
>
**`state`** 컴포넌트 내부에서 관리하는 상태. 값이 바뀌면 해당 컴포넌트가 다시 렌더링됨
>
**`context`** 전역적으로 공유된 상태. 구독 중인 컴포넌트는 값이 바뀌면 모두 다시 렌더링됨

<br>

### 🔹 props (부모 → 자식 전달값)
- 읽기 전용 데이터
- 컴포넌트 간 데이터 전달 시 사용
- 바뀌면 해당 **자식 컴포넌트가 리렌더링**

```
function Parent() {
  const [count, setCount] = useState(0);
  return <Child value={count} />;
}

function Child({ value }) {
  return <div>{value}</div>; // count가 바뀌면 Child도 다시 렌더링됨
}
```

<br>

### 🔹 state (컴포넌트 내부 상태)
- 컴포넌트가 직접 가지고 있는 동적인 값
- useState 또는 useReducer로 생성
- 값이 바뀌면 해당 컴포넌트는 **무조건 리렌더링**


```
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

<br>

### 🔹 context (전역 공유 상태)
- 여러 컴포넌트에서 공통으로 필요한 데이터를 공유할 때 사용
- useContext로 구독한 컴포넌트는 값이 바뀌면 **전부 리렌더링**


```
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Header />
    </ThemeContext.Provider>
  );
}

function Header() {
  const theme = useContext(ThemeContext); // "dark"로 바뀌면 이 컴포넌트 리렌더링
  return <h1>Theme: {theme}</h1>;
}
```

<br>

## 🍅 UX 개선을 위한 렌더링 최적화 방법
- 렌더링이 너무 자주 발생하면 UI가 버벅이고 느려짐
→ 다음과 같은 방식으로 불필요한 렌더링을 줄여야 UX가 개선됨

<br>

### 🔹 React.memo(Component)
>props가 바뀌지 않으면 렌더링을 막아줌 (함수형 컴포넌트용)


```
const Child = React.memo(({ value }) => {
  return <div>{value}</div>;
});

```

<br>

### 🔹 useCallback(fn, deps)
>매 렌더링마다 새로운 함수가 생기는 걸 방지
→ 자식 컴포넌트에 props로 함수를 넘길 때 사용

```
const handleClick = useCallback(() => {
  doSomething();
}, []);
```

<br>


### 🔹 useMemo(fn, deps)
>연산 비용이 큰 계산 결과를 캐싱
→ 값이 바뀌지 않으면 이전 결과 재사용

```
const result = useMemo(() => heavyCalc(data), [data]);
```

<br>


### 🔹 상태는 필요한 곳에만 위치시키기
>상태를 위로 올릴수록 더 많은 컴포넌트가 리렌더링됨
→ 최대한 가까운 컴포넌트에 두기
>
❌ 상위 컴포넌트에 모든 상태 몰아두기
✅ 필요한 컴포넌트 내부에서만 관리


<br>

## 🍅 불필요한 렌더링이란?
UI에 변화가 없는데도, **컴포넌트가 다시 실행되는 것**

즉, 사용자에게 보여지는 화면은 똑같은데
내부적으로 컴포넌트가 다시 실행되고, 상태나 `DOM`을 다시 계산하는 낭비가 발생하는 상황

<br>

### 🔹 불필요한 렌더링 예제
```
function Parent() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(count + 1)}>+</button>
      <Child name="John" />
    </>
  );
}

function Child({ name }) {
  console.log("Child 렌더링됨");
  return <div>Hello, {name}</div>;
}
```

위 예제에서
- **Child**는 `props`가 전혀 안 바뀌었지만,
- 부모인 **Parent**가 렌더링되면 **Child**도 함께 렌더링됨 → 불필요한 렌더링

<br>

## 🍅 React는 내부적으로 어떤 방식으로 렌더링을 최적화할까?
### 🔹 Reconciliation (조정) 알고리즘
>Virtual DOM을 기반으로, **어떤 부분만 변경되었는지**를 비교하고, 실제 DOM 변경은 최소화

- 변경된 Virtual DOM과 이전 DOM을 **비교**
- key 값으로 **리스트 요소의 변화**도 추적
- → 최소한의 DOM 조작만 수행 (ex. 재삽입 대신 텍스트만 바꾸기)

<br>

#### Diffing의 기본 규칙
- 태그 타입이 바뀌면 → 전체 DOM 재생성
- key가 없으면 → index 기준으로 비교 → 비효율
- 같은 컴포넌트라도 props가 바뀌면 → 다시 렌더링

<br>

### 🔹 메모이제이션 패턴 (Memoization Pattern)
>값이나 함수가 **같은 조건이면 재사용**해서 계산 비용 절감

<br>

#### React가 제공하는 도구
**`React.memo`**	컴포넌트의 props가 바뀌지 않으면 렌더링 건너뜀
**`useMemo`**	계산된 값을 캐싱해서 성능 최적화
**`useCallback`**	함수를 캐싱해서 자식에게 같은 참조값 전달

<br>

### 🔹사용자가 쓸 수 있는 렌더링 최적화 패턴
#### 1. Presentational + Container Pattern
- UI 컴포넌트와 로직 컴포넌트를 분리
- 상태 변화는 container에서만 일어나게 하여 UI 컴포넌트의 렌더링 최소화

<br>

#### 2. Component Split & Key 관리
- 리스트 렌더링 시 key를 명확하게 설정해서 재사용 가능하게 유도
- 변화가 잦은 컴포넌트와 그렇지 않은 컴포넌트를 **분리**

<br>

#### 3. Lazy Rendering / Code Splitting
- React.lazy, Suspense 등을 활용해서 **필요할 때만 로딩**
- UX와 초기 렌더링 성능 개선

<br>

<br>

## ✅ 한 줄 요약
>불필요한 렌더링은 화면엔 변화 없는데 내부에서 컴포넌트가 불필요하게 실행되는 현상이고,
React는 **`Reconciliation 알고리즘`** + **`메모이제이션 패턴`**으로 이를 최적화한다


