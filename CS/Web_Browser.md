# ⭐웹 렌더링

## 🍅브라우저 구조
![](https://velog.velcdn.com/images/tlaals44/post/99e9babb-e3ab-48ae-b177-8f6c248e6809/image.png)

### 1. 사용자 인터페이스(User Interface)
주소창, 북마크, 뒤로가기/앞으로가기 버튼, 새로고침 버튼 등 브라우저 사용자와의 상호작용을 담당하는 부분

<br>

### 2. 브라우저 엔진
사용자 인터페이스와 렌더링 엔진 간의 통신을 중개하는 역할
요청을 전달하고 데이터를 받아 렌더링 엔진으로 넘김

<br>

### 3. 렌더링 엔진
`HTML`, `CSS`, 그리고 `JavaScript`를 처리해 화면에 표시
>**주요 렌더링 엔진**:
>**Blink** (크롬, 엣지)
**WebKit** (사파리)

<br>

### 4. UI 백엔드
버튼, 텍스트박스, 드롭다운 등 브라우저 `UI 요소`를 그리는 시스템

<br>

### 5. 자바스크립트 엔진
`JavaScript` 코드를 **파싱**하고 실행
>**주요 자바스크립트 엔진**:
**V8** (크롬, 엣지)
**SpiderMonkey** (파이어폭스)

<br>

### 6. 네트워크 모듈
서버와 통신하여 `HTML`, `CSS`, `JS`, 이미지 등의 리소스를 요청하고 다운로드

<br>

### 7. 데이터 저장소
`쿠키`, `캐시`, `로컬 스토리지`, `세션 스토리지` 등을 관리

<br>

####  UI 백엔드와 렌더링 엔진의 차이
>**렌더링 엔진**은 브라우저가 `웹 콘텐츠`를 렌더링하기 위한 엔진 
사용자가 방문한 웹 페이지를 읽고, 스타일을 적용하여 화면에 그림
>
**UI 백엔드**는 브라우저 자체의 기본 `UI 구성요소`를 처리하며, 렌더링 엔진과는 독립적으로 동작
![](https://velog.velcdn.com/images/tlaals44/post/1f897fa3-4524-4134-bd5a-0baa46fbd49d/image.png)


<br>

<br>

----

## 🍅UI에서 요청을 보낸 후 새로운 페이지가 렌더링되는 과정

### 1. URL 입력 및 요청 및 서버 응답
사용자가 브라우저의 주소창에 URL을 입력하거나 링크를 클릭
브라우저는 입력된 URL을 파싱해 도메인 이름, 경로, 쿼리 문자열 등을 분석
네트워크 요청이 발생하며 **DNS를 통해 도메인 이름을 IP 주소로 변환**
HTTP(S) 요청을 서버로 보냄

서버는 요청된 리소스(HTML, CSS, JS, 이미지 등)를 반환
HTTP 응답에는 `상태 코드`(예: 200 OK, 404 Not Found)와 함께 `데이터`가 포함

<br>

### 2. HTML 파싱 및 DOM 생성
브라우저의 `렌더링 엔진`이 HTML 문서를 한 줄씩 읽기 시작
HTML 파싱 중 요소들을 계층적으로 구성해 **<span style=color:red>DOM(Document Object Model)</span>**을 만든다
![](https://velog.velcdn.com/images/tlaals44/post/de77ab04-35a4-4c63-a23c-6b7396beb991/image.png)

<br>

### 3. CSS 파싱 및 CSSOM 생성
렌더링 엔진은 `<style>` 태그나 `<link>`태그를 만나면 CSS를 파싱
CSS 파싱 결과는 **<span style=color:red>CSSOM(CSS Object Model)</span>**으로 변환
**CSSOM**은 각 HTML 요소의 스타일 정보를 담고 있다

<br>

### 4. 렌더 트리 생성
브라우저는 `DOM`과 `CSSOM`을 결합하여 **렌더 트리(Render Tree)**를 생성
렌더 트리는 **실제로 화면에 그릴 요소**만 포함
>예: `display: none` 속성이 적용된 요소는 렌더 트리에서 제외

<br>

### 5. 자바스크립트 파싱 및 실행
HTML 파싱 중 `<script>` 태그를 만나면 브라우저는 `자바스크립트 엔진(V8 등)`을 통해 JS 코드를 파싱하고 실행
**<span style=color:red>JS가 DOM이나 CSSOM을 수정하면 렌더 트리가 다시 생성될 수 있다</span>**
>**자바스크립트 실행 방식**
**Blocking**: 기본적으로 HTML 파싱을 멈추고 JS를 실행.
**Async/Defer**: 비동기적으로 실행하여 HTML 파싱과 병렬 처리 가능.

<br>

### 6. 레이아웃 계산
렌더 트리가 완성되면 브라우저는 **<span style=color:red>각 요소의 크기(Width, Height)와 위치(x, y)를 계산</span>**
이를 **레이아웃(Layout)** 또는 **Reflow**라고 한다

<br>

### 7. 페인팅(Painting)
브라우저는 각 요소의 스타일(배경색, 텍스트, 그림자 등)을 기반으로 `픽셀 단위`로 화면에 그림
GPU 가속이 적용되는 경우, 페인팅된 결과물이 GPU에 전달

<br>

### 8. 컴포지팅(Compositing)
여러 레이어로 나뉜 요소들을 합쳐 최종적으로 화면에 표시
애니메이션, 전환 효과 등도 이 단계에서 처리

<br>

### 9. 요약
>**새로운 페이지로 이동했을 때의 주요 흐름**
1. URL 입력 → 네트워크 요청 → 서버 응답
2. HTML 파싱: DOM 생성
3. CSS 파싱: CSSOM 생성
4. JS 실행: DOM/CSSOM 수정 가능
5. 렌더 트리 생성: 화면에 표시될 요소만 포함
6. 레이아웃 계산: 각 요소의 위치와 크기 결정
7. 페인팅 및 컴포지팅: 요소를 화면에 그려 최종 렌더링

![](https://velog.velcdn.com/images/tlaals44/post/e8cc31d4-b39a-4cbb-bc3c-d804b57cafa7/image.png)

<br>

### 10. 렌더링 최적화
**CSS와 JS 로드 최적화**
CSS는 `<head>`에 배치, JS는 defer 또는 async 속성을 사용
  
**리소스 크기 줄이기**
이미지 최적화, 코드 압축(Minify)
  
**레이아웃 재계산(Reflow) 최소화**
DOM 변경은 최소화하고, 스타일 변경은 Batch로 처리
  
**캐싱 활용**
HTTP 캐시, 브라우저 캐시를 활용해 네트워크 요청 최소화

<br>

<br>

