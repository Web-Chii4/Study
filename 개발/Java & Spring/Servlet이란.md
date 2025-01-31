# Servlet이란?

<aside>
💡

HttpServlet을 상속받아 만든 하나의 클래스로, 웹 서버(WAS)와 연동되어 HTTP요청을 **동적 웹 페이지(요청에 따라 다른 형태의 응답)를 만들기 위한 Java기술**이다.

</aside>

동적 웹 페이지??

→ HTTP요청에 따라 다른 형태의 응답(페이지의 내용이 달라짐, JSON등의 데이터 형식으로 반환 가능)

## Servlet활용 과정

![image](https://github.com/user-attachments/assets/d9adcbdc-79c7-4753-9613-dae0262448d5)

### 1. 개발자가 Servlet을 위한 코드를 작성한다.

- 클래스 단위로 작성하며 jakarta에서 제공하는 `HttpServlet` 클래스를 상속받아 구현한다.
- `doGet` , `doPost` 와 같은 Http메서드에 해당하는 메서드를 작성한다.

### 2. 해당 서블릿 소스를 .class파일로 컴파일한다.

- `HttpServlet` 을 상속 받아 구현된 Servlet클래스 파일을 .class파일로 컴파일한다.(바이트 코드)

### 3. 해당 바이트 코드 파일을 서블릿 컨테이너에 등록한다.

- 서블릿 컨테이너에서 해당 서블릿을 저장 및 관리한다.

### 4. 클라이언트 요청

- 클라이언트는 웹 서버에 HTTP요청을 보낸다.

### 5. 서블릿 실행

- **웹 서버**는 클라이언트에서 보낸 요청을 **서블릿 컨테이너에게 전달**한다.
- 서블릿 컨테이너가 해당 HTTP요청의 **URL과 매칭되는 서블릿을 찾아 실행**한다.
- 서블릿 객체가 생성되거나, 기존 서블릿이 있다면 재사용된다.

### 6. 서블릿 메서드 실행

- 서블릿은 서블릿 컨테이너에게 받은 요청을 `doPost` 혹은 `doGet` 과 같은 메소드를 통해 실행한다.

### 7. DB작업이 필요한 경우 DB작업을 처리한다.

- 서블릿 동작 메서드에서 DB작업을 처리한다.

### 8. 응답

- 서블릿이 처리한 결과(HTML, JSON, XML 등)를 클라이언트에게 응답한다.
- 브라우저는 응답 데이터를 해석해 웹 페이지를 렌더링한다.

## 서블릿 컨테이너(Servlet Container = Web Container)

<aside>
💡

웹 서버의 컴포넌트 중 하나로 **자바 서블릿과 상호작용** 하며 **서블릿의 생명주기를 관리**하며 URL과 특정 서블릿을 맵핑하며 URL요청이 올바른 접근 권한을 갖도록 보장한다.
즉, 서블릿 컨테이너는 서블릿의 실행 환경을 제공하는 소프트웨어이다.

</aside>

### 서블릿 컨테이너의 역할

1. **서블릿의 생명주기 관리**
    - 생성 → 초기화 → 실행 → 소멸 의 생명주기를 자동으로 관리한다.
2. **HTTP요청과 응답 처리**
    - 서블릿 컨테이너는 클라이언트로부터 들어오는 HTTP요청을 서블릿으로 전달하고 서블릿이 생성한 응답을 다시 클라이언트에게 전달하는 매개 역할을 한다.
    - 요청에 대해서는 Filter를 걸어 요청 처리 전에 **인증, 로깅, 데이터 압축 등 로직 처리를 위해 사전 동작을 수행할 수 있다**.
3. **멀티 스레드 처리(Concurrency)**
    - 서블릿 컨테이너는 하나의 서블릿 인스턴스를 공유하고, 여러 클라이언트 요청을 처리한다. 따라서, 요청마다 새로운 스레드를 생성해 동시 처리가 가능하다.
4. **서블릿 매핑 및 URL관리**
    - 서블릿 컨테이너는 **URL과 서블릿 클래스를 매핑해 특정 요청을 적절한 서블릿으로 연결**해준다.
      ex) `web.xml` 또는 `@WebServlet` 어노테이션을 통해 설정하는 경우가 있다.
5. **서버 동작을 위한 인스턴스 혹은 데이터들을 관리할 수 있다.**
    - Session, Cookie, DB Connection과 같이 Servlet동작 중 필요한 인스턴스들을 관리할 수 있다.

### 서블릿 생명 주기

1. `init()`
    - 서블릿이 처음 로드될 때 실행된다.
2. `service()`
    - 요청이 들어올 때마다 실행된다.
    - **service메소드를 실행**하고, Http요청의 메소드에 따라 `doGet` 혹은 `doPost`메소드 실행 여부를 따져 실행한다.
3. `destroy()`
    - 서블릿 컨테이너가 종료되거나 서블릿이 없어질 때 실행된다.

## Servlet 컨테이너에 대한 Job지식

### 1. 서블릿과 URL매핑은 어떻게?

서블릿 컨테이너에서 서블릿과 URL을 매핑하기 위해서는 **개발자가 URL과 서블릿을 매핑한 정보를 코드 상 명시해주어야 한다** 명시하기 위한 **기법은 XML과 어노테이션을 통해 가능하다**.

기존의 방식(옛날 방식)에는 `Web.xml` 을 통해 Servlet을 등록하고 관리했다. 하지만, 이는 `.xml` 을 활용한 하나의 관리 기법으로 Servlet을 등록 및 관리하기 위한 방법은 달라질 수 있다.

```xml
<web-app>
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.example.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

현재는 어노테이션 기반으로 서블릿을 등록한다.

```java
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/hello") // web.xml 없이 URL 매핑
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        resp.getWriter().write("Hello, Servlet!");
    }
}
```

### 2. 멀티 스레드 동작으로 인한 동시성 제어

멀티 스레드를 활용하면 항상 유념해야 하는 동시성 제어에 대한 문제인 race condition(경쟁 상태)를 생각해야 한다.

서블릿 컨테이너는 Request를 받고, 해당 Request에 대한 동작을 처리해야 하는데 만일 하나의 스레드로 해당 동작을 모두 실행하게 되면, 실행 중 또 다른 클라이언트가 Request를 보냈을 때 해당 요청을 처리할 수 없게 된다.

이를 해결하기 위해 서블릿 컨테이너에서는 요청을 받는 스레드 외 요청의 동작을 처리하기 위한 스레드를 하나 더 생성하여 병렬적으로 처리되도록 한다.

스레드가 여러 개 생겨남에 따라 공유 자원이 있다면, 해당 공유 자원에 대해 동시성 제어(동기화 작업)이 필요하다.

```java
@WebServlet("/counter")
public class CounterServlet extends HttpServlet {
    private int count = 0; // 공유 자원 (Thread-Safe하지 않음!)

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        count++; // 여러 스레드가 동시에 접근하면 문제 발생 가능!
        resp.getWriter().write("Count: " + count);
    }
}
```

위와 같은 예시를 들 수 있다.

Java에서는 이를 해결하기 위해 `synchronized(상호 배제)` , `AtomicInteger` , `ThreadLocal` 등의 키워드 및 클래스를 활용할 수 있다.

단, AtomicInteger과 synchronized는 하나의 공유 자원에 대한 동기화이지만, `ThreadLocal` 의 경우 스레드 하나에 독립된 변수를 생성하는 클래스이다.

### 예시

Tomcat, Jetty, WildFly 등

### 참고

[[Web] 서블릿(Servlet)이란 무엇인가? 서블릿 총정리](https://coding-factory.tistory.com/742)