# Spring Framework란?

> Spring Framework는 Java 애플리케이션 개발을 위한 **종합적인 기반을 지원한다**. **Spring은 구조를 다루기 때문에 개발자는 애플리케이션에만 집중하면 된다는 이점**이 있다.
>

## Spring Framework의 이점

1. Transaction API처리 없이 **데이터베이스 트랜잭션**을 실행하는 Java메소드를 만들 수 있다.
    1. @Transactional 애너테이션을 사용해 해당 메서드가 자동으로 트랜잭션 내에서 실행되기 할 수 있다. 원래는 JTA(Java Transaction API)를 사용해야 함
2. 복잡한 원격 API를 다룰 필요 없이 **원격 프로시저 호출(RPC-Remote Method Call)을 할 수 있는 Java local메소드를 만들 수 있다**.
    1. @RestController 또는 @Remote와 같은 애너테이션을 활용해 로컬 메소드를 원격 호출 엔드포인트로 노출할 수 있다.
3. **JMX(Java Management Extension)을 사용할 필요 없이** **관리 명령을 이행**하는 자바 로컬 메소드를 만들 수 있다.
    1. @ManagedResource, @ManagedAttribute, @ManagedOperation애너테이션을 사용해 JMX관리 기능을 선언적으로 노출할 수 있다.
4. **JMS(Java Message Service) API를 다룰 필요 없이 메시지를 처리**하는 지역 자바 메소드를 만들 수 있다.
    1. @JmsListener애너테이션을 사용해 메서드를 메시지 핸들러로 쉽게 등록 가능하다.

### 정리

- **복잡한 API 추상화**: JDBC, JMS, JMX, RMI같은 복잡한 Java API를 다룰 필요 없이 애너테이션 만으로 구현 가능하다.
- **선언적 프로그래밍**: 애너테이션을 활용해 가독성 및 유지보수성 향상
- **유연성**: 다양한 프로토콜 및 라이브러리 지원해 확장 가능

<aside>
💡

기본적으로 사용해야 하는 Java라이브러리를 활용할 필요 없이 애너테이션 만으로 API를 활용할 수 있다.

</aside>

## Spring Framework의 특징

![image](https://github.com/user-attachments/assets/cc0322ed-3c23-4476-8fa6-e4c59af26ca0)

### 1. IoC/DI

- **IoC(Inversion Of Control = 제어의 역전)** : 개발자가 객체의 생성과 생명주기를 관리하지 않고, IoC컨테이너가 관리하도록 한다)를 활용해 **DI(Dependency Injection = 의존성 주입)**를 이루며, IoC컨테이너는 Bean생성 시 종석성을 주입한다.
- 객체의 생성 및 생명주기를 관리하지 않기 때문에 편리하지만, Spring에서 관리하는 방법을 알아야 더 효율적으로 활용할 수 있다.

**IoC(Inversion of Control): 제어 역전 원칙은 제어의 주체를 다른 대상으로 역할을 위임 한다는 것 Spring에서는 의존성 주입을 통해 개발자가 해야 할 객체 관리 및 생성의 제어권을 Spring에 위임하는 것**

### 2. AOP(Aspect Oriented Programming)

- AOP(Axpect Oriented Programming)을 통해 흩어진 관심사를 한번에 모아 모듈화 하여 여러 곳에서 사용되는 기술을 하나의 annotation만으로 사용할 수 있도록 한다.
- 공통적으로 사용되는 로직을 클래스 밖으로 빼내서 별도의 모듈로 만드는 것

### 3. PSA(Portable Service Abstraction)

- Spring프레임 워크의 핵심 철학 중 하나로, 개발자가 다양한 환경에서 일관된 방식으로 스프링을 개발할 수 있도록 **추상화** 한 것이다.
- 개발자에게 **일관된 프로그래밍 경험을 제공**한다.
- 추상화를 제공해 다양한 기술(DB로 치면 MySQL, Oracle)로 옮기는 것이 가능하다.
    - 추상화를 제공하기 위해 인터페이스(JPARepository), 추상 클래스(AbstractRoutingDataSource), 템플릿 클래스(JdbcTemplate), 애너테이션(Transactional) 등을 제공하고 일관되게 서버를 개발할 수 있도록 한다.

### 4. POJO(Plane Old Java Object) - 순수 자바 객체

- 특별한 규칙이나 **프레임워크 종속성을 가지지 않는 순수한 자바 객체**를 말한다.
- 이런 순수 자바 객체를 활용해 애플리케이션을 만들 수 있도록 해준다.



## Spring Framework Runtime

![image](https://github.com/user-attachments/assets/6dd25117-b0b2-47c8-862d-aa493808a33c)
### Core Container

Core, Bean, Context, Expression Language모듈로 구성되어있다.

1. **Core와 Bean**
    - IoC(Inversion of Control)과 DI(Dependency Injection)을 포함하는 프레임 워크의 기반을 제공하는 모듈이다.
    - Factory 패턴으로 구현된 `BeanFactory` 를 활용하면 직접 싱글톤 객체를 만들 필요는 없으며, 실제 프로그램 로직과 객체 설정(의존성 주입)을 분리할 수 있다.
2. **Context**
    - Core와 Bean모듈을 기반으로 기능을 제공한다.
    - JNDI(Java Naming and Directory Interface)와 유사한 방식으로 객체에 접근한다.
    - Context모듈이 Bean모듈을 상속하며 이벤트 전파(event propagation), 리소스 로딩(resource-loading), 컨텍스트 일시적 생성(`ServletContext` 등을 이용해)
    - Java EE관련 기능을 지원한다.(EJB, JMX 등)
    - `ApplicationContext` 는 Spring에서 Bean을 관리하고 제공하는 핵심 인터페이스로 `BeanFactory` 보다 더 강력한 기능을 제공한다.
3. **Expression Language**
    - 런타임 시 객체를 querying하고 조작할 수 있는 표현 언어를 제공한다.
    - JSP2.1에 명세된 통합된 표현 언어의 확장이다.
    - 속성 값 설정 및 가져오기, 속성 할당, 메서드 호출, 배열 컬렉션 및 인덱싱기의 컨텍스트 접근, 논리 연산자 및 산술 연산자, 명명된 변수, Spring의 IoC컨테이너에서 이름으로 객체를 검색하는 것을 지원한다.
    - 목록 투영 및 선택 뿐만 아니라 목록 집계도 지원한다.

### Data Access/Integration

1. **JDBC**
    1. JDBC모듈의 추상 계층을 제공해 JDBC활용을 위한 기본적인 코딩과 DB에러 코드 parsing을 할 필요 없게 해준다.
2. **ORM(Object Relation Mapping)**
    1. **객체 관계 매핑 API들(JPA, JDO, Hibernate, Ibatis 등)을 위한 통합 계층을 제공**한다.
3. **OXM(Object XML Mapping)**
    1. Object/XML mapping을 구현하는 JAXB, Castor, XMLBeans, JiBX, XStream과 같은 모듈을 지원할 수 있는 추상 계층을 제공한다.
4. **JMS(Java Message Service)**
    1. 메시지를 생산하고 소비하는 기능을 포함한 모듈이다.
5. **Transaction**
    1. 특수 인트페이스를 구현하는 클래스와 모든 POJO(Plain Old Java Object)에 대해 프로그래밍 및 선언적 트랜잭션 관리를 지원한다.
    2. DB의 Transaction뿐만 아니라, POJO 즉, Java 동작에 대해서도 적용 가능하다.

### Web

1. **Web**
    1. multipart 파일 업로드 기능(`CommonsMultipartResolver`)
    2.  survlet listener를 활용한 IoC컨테이너 초기화 및 웹 지향 애플리케이션 컨텍스트와 같은 기본적인 웹 지향 통합 기능을 제공한다.
2. **Web-Servlet**
    1. 웹 애플리케이션을 위해 Spring의 모델인 MVC를 구현한다.
    2. Spring MVC는 도메인 모델 코드와 웹 form을 명확히 분리하고 Spring프레임 워크의 기능들을 통합한다.
3. **Web-Struts(3.0부터 deprecated)**
    1. Spring애플리케이션에서 웹 계층을 통합하기 위한 지원 클래스가 포함되어 있다.
    2. 해당 지원은  현재 Spring 3.0에 deprecated되었다.
4. **Web-Portlet**
    1. portlet환경에서 사용되기 위한 MVC구현을 제공한다.
    2. Web-Servlet모듈의 기능의 복사본이다.


### 5. MVC패턴

**Model (Service + Repository)**

- 데이터베이스와 상호작용하며 데이터의 가공, 조회를 담당한다.
- View와 Controller에 대해 알 수 없어야 한다.(참조하면 안된다.)
- 재사용 가능해야 하며 다른 인터페이스에서도 변하지 않아야 한다.
- 비즈니스 로직을 포함한다.

**View**

- 사용자의 화면에 보여지는 결과물로 단순히 사용자가 애플리케이션을 화면을 통해 볼 수 있도록 해주는 계층이다.(UI제공)
- 사용자의 화면은 데이터가 변경되어 다시 보여질 수 있다.

**Controller**

- 사용자가 보낸 HTTP요청을 받아 요청에 맞는 동작을 처리한다.
- 요청에 맞는 동작을 처리할 때 Model 혹은 View와 상호작용을 하여 사용자의 요청을 처리하고 응답한다.
- 사용자 요청 처리 후 적절한 데이터를 View에 전달하는 역할

## Spring MVC모듈 동작 원리

![image](https://github.com/user-attachments/assets/967ed78a-400a-476d-a98e-bd6f3d8252d3)

### 구성 요소

1. **DispatcherServlet(요청 전달)**
- Spring MVC의 핵심 역할로 모든 HTTP요청이 가장 먼저 전달된다( Front 컨트롤러이다.).
- 가장 앞 단에서 클라이언트의 **요청 및 응답까지 전반적인 처리 과정을 통제**한다.

1. **HandlerMapping(맵핑의 역할)**
- 요청된 URL에 매핑된 컨트롤러(Handler)를 찾는 역할
- 어떤 컨트롤러가 특정 요청을 처리할지 결정(Web.xml 혹은 어노테이션 활용)

1. **HandlerAdapter(컨트롤러를 실행)**
- HandlerMapping에서 찾아낸 컨트롤러를 실행하는 어댑터

1. **ViewResolver(View파일 찾기)**
- 컨트롤러에서 반환한 View이름을 기반으로 실제 View파일(HTML, JSP등)을 찾아주는 역할

### 중요!

**Handler Mapping, Handler Adapter, View Resolver**는 Servlet Container의 역할을 분리한 듯 보인다.

**역할을 분리하면 좋은 점?**

역할을 분리하면, 독립적으로 실행되게 되며 **유지보수 및 테스트에 용이**하다. 또한, 분리를 통해 크기를 작게 만들어 **구현체를 다양하게 적용할 수 있다**.

### 동작 순서

1. 사용자가 HTTP요청을 Spring서버에 보내면 Tomcat에 의해 받아지며 요청을 **`DispatcherServlet`에 전달한다.**
2. `DispatcherServlet`에서는 해당 **요청을 분석한 후 적절한 컴포넌트(`HandlerMapping` 혹은 `HandlerAdapter`)에 전달**한다.
    1. `Handler Adapter`에게 전달하는 경우는 이전의 요청에 대해 동일한 URL경로와 매핑된 선례가 있다면, 바로  `HandlerAdapter`에게 전달된다.
3. `HandlerMapping`에서는 **요청 URL을 확인하여 매핑된 컨트롤러를 찾아 `DispathcerServlet`에 반환**한다.
4. `Dispatcher Servlet`은 찾아진 컨트롤러 정보를 `Handler Adapter`에 전달하고 **`HandlerAdapter`에서는 찾아진 컨트롤러를 실행**한다.
5. Controller에서는 등록된 **Handler함수를 실행하고 결과를 `HandlerAdapter`에게 반환**한다.
6. 만약 View를 반환했다면, **`ViewResolver`가 HTML혹은 JSP파일을 반환**해준다.
7. **View가 아닌 경우 데이터만 사용자에게 전달**한다.

## 참고

[Spring Boot](https://spring.io/projects/spring-boot)

[Spring Framework](https://spring.io/projects/spring-framework)

[1. Introduction to Spring Framework](https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/overview.html)