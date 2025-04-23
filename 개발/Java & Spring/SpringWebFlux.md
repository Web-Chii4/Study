# Spring WebFlux

> 적은 스레드로 **동시적 처리를 위한 Non-Blocking 기술을 제공**한다.
>

## Spring WebFlux의 개요

### 등장 배경

- 함수형 프로그래밍이 가능하다.
- Java8에서 lambda표현식이 추가된 것은 Java에서 함수형 API를 만들기 위한 기회가 만들어졌다.
- 이를 통해 비동기 로직의 선언적 구성이 가능하게 되었으며 Java8은 Spring WebFlux를 통해 함수형, annotated형(@Controller) Web endpoint를 함께 사용할 수 있게 되었다.
- Reactive 프로그래밍이 가능하다.(Event → 마우스 클릭, I/O)

### 목적

결과적으로는 **요청에 대한 응답 시간을 줄이고, 적은 수의 스레드와 메모리를 활용해 처리하기 위한 목적**이다. Spring WebFlux를 활용한다고 해서 application이 더 빨라지게 만들진 않는다.

### Spring MVC와 Spring WebFlux의 비교

![Image](https://github.com/user-attachments/assets/24cdeef4-9407-48c4-9091-6e62865b9722)

| **항목** | **Spring MVC** | **Spring WebFlux** |
| --- | --- | --- |
| **웹 서버** | Tomcat, Jetty, Undertow | Tomcat, Jetty, Undertow, **Netty** |
| **프로그래밍 관점** | 명령형 로직 (imperative) → 작성/디버깅 쉬움 | 선언형 로직 (declarative) → 흐름 관리 어려울 수 있음, **스레드 효율적 사용 가능** |
| **Controller 방식** | 주석 기반 Controller (`@Controller`, `@RestController`) | 주석 기반 Controller + **함수형 엔드포인트** (`RouterFunction`, `HandlerFunction`) |
| **모듈 의존성** | **블로킹 API** 사용 가능 (JDBC, JPA 등) | **논블로킹 API만** 사용해야 함 (R2DBC, WebClient 등) |
| **API 요청 클라이언트** | `RestTemplate` (Blocking), `WebClient` (Non-blocking) 모두 사용 가능 | `WebClient`만 사용 권장 (**RestTemplate은 리액티브 모델과 호환되지 않음**) |

**주의)**

Spring WebFlux는 **데이터 흐름 제어를 위해 Reactive Streams 사양을 따르는 라이브러리(RxJava, Reactor 등)를 사용한다**. 주로 Reactor 라이브러리를 사용하며, 그 구현체인 `Mono` (0 ~ 1개) `Flux` (0개 ~ N개) 클래스가 자주 활용된다.

Reactive Streams 사양은 Publisher가 데이터를 얼마나 빠르게 또는 느리게 생산할지를 Subscriber가 제어할 수 있도록 설계된 표준이다. 이는, Publisher(데이터 공급자)가 Subcriber(데이터 소비자)가 감당할 수 없는 수준의 데이터를 무작정 공급하는 경우를 방지하기 위한 BackPressure를 위해 사용한다.

**BackPressure**: "생산자가 너무 빠르게 데이터를 보내지 않도록, 소비자가 속도를 조절하는 기능”

## Spring WebFlux의 구조

Spring WebFlux는 HTTP요청을 받고 처리하기 위해 지원하는 2개의 level이 필요하다.

### 1. HttpHandler

> Http의 **Request와 Response를 처리하기 위한 한 하나의 메서드 계약(인터페이스)**이다.
>

해당 인터페이스는 의도적으로 최소화되어 있으며, 다른 HTTP Server API에 대한 최소한의 추상화에 목적이 있다.

**지원 Server API)**

| **Server이름** | **Server API** | **Reactive Stream** |
| --- | --- | --- |
| Netty | Netty API | Reactor Netty(Mono, Flux) |
| Undertow | Undertow API | spring-web |
| Tomcat | Servlet기반 Non-Blocking | spring-web |
| Jetty | Servlet기반 Non-Blocking | spring-web |
| Servlet Container | Servlet기반 Non-Blocking | spring-web |

### 2. WebHandler API

> HttpHandler보다 약간 높은 수준의 web API로써 **Request 처리와 구체적인 프로그래밍 모델(주석형 Controller, 함수형 Endpoint)이 빌드될 수 있도록 해준다**.
>

`org.springframework.web.server` 패키지는 다수의 `WebExceptionHandler` , 다수의 `WebFilter` , 단일 `WebHandler` 컴포넌트를 chainging을 통해 Http Request를 처리하기 위한 `HttpHandler` 를 빌드한다.

`WebHttpHandlerBuilder`는 Spring의 `ApplicationContext`를 가리키는 것만으로도, 자동으로 컴포넌트들을 감지하고 등록해서 체인을 구성할 수 있다.”

**Web Handler의 목표**

웹 애플리케이션에서 흔히 사용되는 기능들을 더 넓게 제공하기 위한 목적이다.

- User Session
- Request Attribute
- `Locale`  or `Principal` 을 해결
- form data의 캐시 및 parsing
- multipart data의 추상화
- etc…

### 3. Dispatcher Handler

> `WebHandler` 의 구현체로, Request를 처리하기 위한 공통된 알고리즘을 제공한다.
>

`DispatcherHandler`는 설정(Configuration)에 의해 동작하는 위임 기반 컴포넌트로, 구성 요소들을 조합하여 유연하고 다양한 요청 처리 흐름(workflow)을 지원한다.

또한, `DispatcherHandler` 는 Spring Configuration으로 부터 필요한 위임 컴포넌트들을 발견한다. 또한, Spring Bean으로 등록될 수 있으며 `ApplicationContextAware` 을 구현하고 있기 때문에 실행하기 위한 context에 접근이 가능하다. 만일 `DispatcherHandler` 의 이름이 `webHandler` 로 선언되어 있다면, `WebHttpHandlerBuilder` 에 의해 자동으로 발견될 수 있다.

WebFlux를 위해 사용될 수 있는 Spring Config

- 이름이 `webHandler` 인 `DispatcherHandler`
- `WebFilter` 와 `WebExceptionHandler` Bean
- `DispatcherHandler` Bean
- etc…

단순히, `WebHttpHandlerBuilder` 를 통해 Configuration이 제공되기 때문에 아래와 같이 context가 `WebHttpHandlerBuilder` 를 가리키도록 하면 된다.

```java
ApplicationContext context = ...
HttpHandler handler = WebHttpHandlerBuilder.applicationContext(context).build();
```

### 구조도 정리

![Image](https://github.com/user-attachments/assets/76181f27-7645-412b-b555-7b86752229c2)

### 참고

[Overview :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webflux/new-framework.html)

[Reactive Core :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-codecs)