# HTTP(Hypertext Transfer Protocol)란?

> **하이퍼텍스트(HyperText)를 빠르게 교환하기 위한 프로토콜의 일종**으로, 서버와 클라이언트 간 **메시지 교환을 어떻게 이룰지에 대해 정해 놓은 규칙**이다.
>

## 사전 지식(OSI 7 Layer)

![Image](https://github.com/user-attachments/assets/e9505b6c-7598-46c0-a38a-19ea49c922b5)

![Image](https://github.com/user-attachments/assets/7aec519f-eebf-4359-9b5b-aa16d1d0f242)

HTTP프로토콜은 OSI 7계층에 7계층에 속하며, 위와 같이 5, 6, 7계층(응용 계층)에서는 AH(Application Header), PH(Presentation Header), SH(Session Header)와 Data를 합쳐서 보내야 한다.

즉, 7계층에 속한 HTTP프로토콜은 위의 이미지와 같이 AH, PH, SH와 DATA를 전송하기 위한 **Header의 형식과 DATA의 형식을 지정하고, 데이터를 교환할 수 있게 해준다**.

## HTTP 형식(HTTP Message)

> **`HTTP Message = Http Header + Http Body`**
>

```
  HTTP-message   = start-line (request-line OR status-line) CRLF // Http Header
                   *( field-line CRLF )
                   CRLF
                   [ message-body ] // Http Body
```

공식 문서에 따르면, HTTP프로토콜에서 전송하는 단위는 Message이며 해당 Message의 형식은 위와 같다.

CRLF를 구분자로 활용하며, Header는 `start-line` , `field-line` 으로 이루어지며, Body는 `message-body` 로 이루어져있다.

위와 같은 형식으로 데이터를 교환하는데 HTTP Message의 목적에 따라(Request 혹은 Response) `start-line` 의 형식은 `request-line` 혹은 `status-line` 의 형식을 사용한다.

### Request Line(Request Header)

> **`request-line   = method SP request-target SP HTTP-version`**
>

아래와 같은 형식으로 Request Header가 전송된다.

```
// 아래는 Request Line(HTTP Method + Request Target + HTTP Version)
GET /home.html HTTP/1.1
```

**HTTP Method** : GET, POST, UPDATE, PATCH, DELETE 등의 HTTP Method지정

**Request Target**: 요구하는 Resource를 나타낸다.

**HTTP Version**: 요청 시 사용되는 HTTP Version을 명시한다.

**HTTP Method의 종류**

1. `GET`
    1. 특정 리소스를 받아오기 위한 요청으로, 데이터를 받아오기만 한다.
2. `POST`
    1. 특정 Resource에 Entity를 제출할 때 쓰인다. 이에 서버에 데이터를 추가해 서버의 상태를 변경할 수 있다.
3. `PUT`
    1. Target리소스의 현재 데이터 상태를 요청된 payload로 변환한다.
4. `PATCH`
    1. Target리소스의 일부분만 수정하는데 사용된다.
5. `DELETE`
    1. 특정 리소스를 삭제한다.
6. `HEAD`
    1. 응답 본문을 포함하지 않는 응답을 요청한다.

`PUT`과 `PATCH`의 차이

`PUT` 의 경우

```
PUT /posts/123 HTTP/1.1
Host: example.com
Content-Type: application/json
Content-Length: 64

{
  "title": "새 제목",
  "content": "새 내용으로 전체 수정"
}
```

→ 수정 할 모든 데이터를 명시해 Body에 담는다.

`PATCH` 의 경우

```
PATCH /posts/123 HTTP/1.1
Host: example.com
Content-Type: application/json
Content-Length: 33

{
  "title": "제목만 수정할게요"
}

```

→ 수정할 데이터만 전달한다.(나머지는 그대로 유지)

### Status Line(Response Header)

> **`status-line = HTTP-version SP status-code SP [ reason-phrase ]`**
>

```
// Status Line(HTTP Version + Status Code + [Reason-Phrase])
HTTP/1.1 200 OK
```

**HTTP Version** : 응답 시 사용할 HTTP Version을 명시한다.

**Status Code** : 요청의 처리가 어떻게 되었는지 상태 코드로 표기해 전달한다.

**Reason-Phrase** : Status Code와 관련된 설명을 위한 목적으로 제공(Optional)

### Field Line

> **`<Header-Field-Name>: <Field-Value>\r\n`**
>

각 Field Line은 **대문자와 소문자를 구분하지 않는 필드 name 뒤에 `:` 을 붙이고, value를 지정한다**.

**역할)**

- 서버와 클라이언트 간 정보 전달(요청, 응답 시 필요한 정보 제공)
- 요청 및 응답의 동작 방식 조정(캐싱, 인증, 보안 설정)
- HTTP 메시지 흐름 제어(조건부 요청, 리다이렉트 가능)
- 컨텐츠 특성 전달(인코딩, 언어 정보)

Field Line에는 요청의 목적에 따라 종류가 나뉠 수 있다.

### General Header

> **Request와 Response메시지에서 범용적으로 적용 가능한 header Field들**이다. 하지만, Entity(Body)에 대한 메타데이터를 담지 않는다.
>

```
general-header = Cache-Control            
                | Connection               
                | Date                    
                | Pragma                  
                | Trailer                 
                | Transfer-Encoding        
                | Upgrade                  
                | Via                     
                | Warning   
```

**`Cache-Control`** → 캐시 동작을 제어하기 위한 역할

- `no-cache`: 캐시를 사용하지 않고, 항상 서버에서 새로운 응답을 받는다.
- `no-store`: 요청과 응답을 어디에도 저장하지 않는다.
- `max-age=<seconds>`: 응답을 지정된 시간(초) 동안 캐시
- `public`: 모든 캐시(중간 프록시 포함)가 저장 가능
- `private`: 브라우저 캐시만 허용, 프록시 캐시는 금지

**`Connection`**→ 클라이언트와 서버 간 연결 유지 여부 결정

- `keep-alive` : 연결을 유지하며, 여러 요청을 하나의 TCP연결에서 처리
- `close`: 요청 후 연결을 종료

**`Transfer-Encoding`** → 데이터 전송 시 인코딩 방식 지정

- `chunked`: 데이터를 조각(chunk)단위로 전송(스트리밍 응답 가능)

**`Upgrade`** → HTTP에서 다른 프로토콜로 업그레이드 시 사용

- `websocket`: 웹 소켓으로 전환

### Request Header

> 클라이언트가 **Request에 대한 추가적인 정보 혹은 클라이언트 자신에 대한 정보를 서버에 전달하기 위해서 사용**한다. 주로,
>

```
request-header = Accept                                         
                | Accept-Charset           
                | Accept-Encoding         
                | Accept-Language          
                | Authorization           
                | Expect                  
                | From                   
                | Host                   
                | If-Match                
                | If-Modified-Since        
                | If-None-Match           
                | If-Range                
                | If-Unmodified-Since      
                | Max-Forwards           
                | Proxy-Authorization     
                | Range                   
                | Referer                 
                | TE                     
                | User-Agent
```

**`Accept`**  → 응답으로 받을 수 있는 media type을 명시한다.

- `Accept: <MIME_type>/<MIME_subtype>` 형식으로 이루어지며, `*` 이 있는 경우 모든 종류를 포함한다.
- `text/html` , `application/xml` 등 MIME에서 정의하는 type과 subtype들을 명시한다.

**`Accept-Charset`**  → 응답으로 부터 어떤 Character Set(문자 집합)을 받을 수 있는지 명시

- MIME에 명시되어 있으며, `utf-8` , `iso-8859-5` , `unicode-1-1` 등이 존재한다.
- 만일 해당 Field를 정의하지 않으면 기본적으로 모든 Charset을 수용할 수 있다.
- 만약, 클라이언트에서 정의한 Charset을 Server에서 해당 Charset으로 응답할 수 없다면 406(Not Acceptable)응답 에러를 보낸다.

**`Accept-Encoding`**  → 응답으로 받을 수 있는 content-codings(Encoding)를 제한한다.

- 인코딩은 **데이터를 압축하기 위해 사용**된다.
- `gzip(GNU zip)` , `compress`  등의 인코딩 존재

**`Authorization`**  → Server에 인증하기 위한 경우 사용

- 인증정보를 담는 헤더로, `Bearer` , `Basic` 을 활용한다.

**`User-Agent`**  → 클라이언트(웹 브라우저 OR 앱)의 정보(버전, OS 등)

- 형식 : `<product> / <product version> <comment>`

**`Host`**  → 요구되는 자원의 인터넷 host와 port번호를 명시한다.

- **HTTP/1.1에서는 모든 Request header에 해당 헤더를 추가해야 한다**.(헤더가 없다면, 서버에서 `400`에러를 반환해야 함)
- 만일, Port가 명시되어있지 않는 경우 80 Port로 처리된다.

**`Referer`**  → 클라이언트가 어느 페이지로부터 요청을 보냈는지 나타낸다.

예시)

```
GET /article HTTP/1.1
Host: another.com
Referer: https://example.com/page1
```

### Response Header

> 서버가 Status-Line에서 전달되지 않은 추가적인 정보를 제공하기 위한 헤더로 **서버나 요청 URI에 의해 식별된 리소스 접근에 대한 정보를 제공한다**.
>

```
response-header = Accept-Ranges           
                 | Age                   
                 | ETag                   
                 | Location               
                 | Proxy-Authenticate      
                 | Retry-After             
                 | Server                 
                 | Vary                  
                 | WWW-Authenticate
```

**`Location`**

- **Redirection 혹은 201(Created)를 나타내기 위해 URL을 명시한다**.
- Redirection은 Redirect할 URL을 명시한다.
- 201은 Created된 Resource의 위치를 명시한다.
- 해당 URL은 **항상 절대경로**이어야 한다.

### Entity Header

> **Entity Body 혹은 Body가 없는 경우의 메타 정보를 정의**한다.
>

**Entity란?**

전송되는 실제 데이터와 그에 대한 메타데이터를 말한다. 즉, Entity Body는 실제 전송되는 Http Body에 담기는 데이터라는 것이다.

```
entity-header  = Allow                    
                | Content-Encoding        
                | Content-Language        
                | Content-Length          
                | Content-Location       
                | Content-MD5              
                | Content-Range         
                | Content-Type          
                | Expires                
                | Last-Modified           
                | extension-header
```

**`Content-Encoding`**

- Body의 내용이 어떤 방식으로 인코딩 되었는지 명시한다.

**`Content-Language`**

- Body의 내용이 어떤 언어로 이루어져 있는지 명시한다.

**`Content-Length`**

- Body의 크기를 나타낸다.
- ex) `Content-Length: 3495`

**`Content-Type`**

- Body의 MIME타입 정보를 명시한다.

**`Content-Location`**

- 해당 Body에 있는 데이터가 어디로부터 가져와 졌는지 위치를 명시한다.

요청

```
GET /latest-news HTTP/1.1
Host: example.com
```

응답

```
HTTP/1.1 200 OK
Content-Location: /news/2025/04/04
Content-Type: text/html

내용들...
```

## Status Code

### 2XX(Successful)

> Client의 요청이 **성공적**으로 받아들여졌음을 나타낸다.
>

`200 OK`

- 요청은 성공되었으며, 응답으로 반환된 정보가 Method에 의존하는 경우(GET, HEAD, POST, TRACE)

`201 Created`

- 새로운 Resource를 생성을 이행한 경우 반환되는 코드이다.

`202 Accepted`

- 요청이 처리되기 위해 받아들여졌지만, 아직 처리되지 않은 상황임을 뜻한다.

`204 No Content`

- 서버의 요청이 이행되었으나, entity-body를 반환할 필요가 없는 경우의 상태 코드이다.
- 응답은 새로운 혹은 업데이트된 meta정보를 entity header에 포함하는 경우일 수 있다.

### 3XX(Redirection)

>
>

### 4XX(Client Error)

> **Client가 오류를 낸 경우의 상태 코드**이다
>
- `HEAD` 요청을 제외하고, 모든 에러 상황에 대한 설명 entity가 포함되어야 한다.
- User Agent(웹 브라우저)는 사용자에게 entity(에러 상황)를 포함해 보여야 한다.

`400 Bad Request`

- 잘못된 문법으로 인해 서버가 이해할 수 없는 요청인 경우
- Client의 수정 없이 요청을 다시 보내면 안된다.

`401 Unauthorized`

- Request는 사용자의 인증이 필요하며, `WWW-Authenticate` 헤더(인증 정보)를 포함해야한다.
- 만약, Request에 이미 인증 자격이 포함되었다면, `401` 응답은 권한이 거부된 것을 뜻한다.

`402 Payment Required`

- 아직 사용되지 않으나, 미래에 사용될 상태 코드

`403 Forbidden`

- 서버에 요청이 잘 갔으나, 권한이 낮거나 차단된 IP 혹은 관리자 전용 리소스에 접근하는 경우에 반환한다.
- 해당 요청에 대해 요청이 잘 도착했음을 알 수 있기도 하다.

`404 Not Found`

- Server에서 매칭되는 Request URI를 찾을 수 없는 경우 반환한다.

`405 Method Not Allowed`

- 명시된 Method가 허락되지 않은 경우 반환되는 상태 코드이다.

`408 Request Timeout`

- 클라이언트가 보내는 요청이 너무 느린 경우 발생하는 코드이다.

`409 Conflict`

- 리소스 충돌 시 발생하는 상태 코드로, 주로 `PUT` 요청에 대한 응답 시 발생한다.
- 로그인 후 재시도 하는 경우를 예로 들 수 있음

`415 Unsupported Media Type`

- 서버에서 Request Header에 명시된 Content-Type과 Entity의 불일치로 처리할 수 없는 경우 혹은 서버가 해당 타입을 지원하지 않는 경우 발생한다.
- Header와 Body를 Client에서 다시 살핀 후 재요청한다.

`429 Too Many Request`

- Client 측에서 너무 많은 요청을 보낸 경우
- 잠시 뒤 재시도 하도록 처리한다.

### 5XX (Server Error)

> 응답 상태 코드가 5로 시작되는 경우, 서버 측에 요청을 처리 중 오류가 발생함을 뜻한다.
>

`500 Internal Server Error`

- 서버 내부에서 예기치 못한 에러가 발생한 경우 반환되는 상태코드이다.
- 예로, NullPointerException, DB연결 실패 등

`501 Not Implemented`

- 클라이언트가 요청한 기능을 아직 구현하지 않는 경우

`502 Bad Gateway`

- 서버가 게이트웨이 혹은 프록시 역할을 하는 하는 도중 .뒤쪽 서버의 잘못된 응답을 받은 경우를 말함

`503 Service Unavailable`

- 서버가 과부하/점검/오류로 일시적으로 사용 불가한 경우

`504 Gateway Timeout`

- 서버가 프록시/게이트 웨이로 동작 중, 다른 서버의 응답이 너무 오래 걸리는 경우

### 참고

[](https://namu.wiki/w/HTTP)

[RFC 9112: HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc9112#name-request-line)

[RFC 2616: Hypertext Transfer Protocol -- HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc2616#section-5)

[RFC 6265: HTTP State Management Mechanism](https://datatracker.ietf.org/doc/html/rfc6265)