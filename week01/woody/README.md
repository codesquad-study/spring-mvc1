



### 웹 서버(Web Server)

HTTP를 통해 웹 브라우저에서 요청하는 **정적 리소스**를 전송해주는 서비스 프로그램

- 예 ) NGINX, APACHE



### 웹 애플리케이션 서버(WAS)

HTTP를 통해 웹 브라우저에서 요청하는 정적 리소스와 동적 리소스를 전송해주는 서비스 프로그램

- 프로그램 코드를 실행해서 

  어플리케이션 로직 수행

  - 동적 HTML, HTTP API(JSON)
  - 서블릿, JSP, 스프링 MVC

- 예 ) 톰캣(Tomcat), Jetty, Undertow



### 웹 시스템 구성 - WAS, DB

- WAS와 DB로만 웹 시스템을 구성하게 된다면
  - WAS 가 너무 많은 역할을 담당
  - 서버 과부하 우려
  - 오류 화면조차 노출 불가능 (WAS 장애가 잘나서 더 위험)
- 정적 데이터 서빙은 단순한 반면 어플리케이션 로직은 비싸다
  - 정적 리소스때문에 어플리케이션 로직 수행에 영향이 갈 수 있다



### 웹 시스템 구성 - WEB, WAS, DB

- 웹 서버는 정적 리소스 담당
- 동적 처리가 필요한 업무는 WAS에게 요청 위임
- **WAS는 중요한 어플리케이션 로직 처리에 집중할 수 있다**
- 효율적인 리소스 관리 가능
  - 정적 리소스 많으면 웹서버 늘리기
  - 동적 리소스 많으면 WAS 늘리기

- WAS, DB 장애 시 WEB 서버가 오류 화면 제공할 수 있다





### 서블릿

HTTP 요청과 응답에 관련된 비즈니스 로직 실행 이외의 작업을 다 자동화 -> 개발자는 **비즈니스 로직에만 집중**할 수 있다!

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
		
		@Override
		protected void service(HttpServletRequest request, HttpServletResponse response) {
				// 어플리케이션 로직 
    }
}
```

- 웹 브라우저에서 "/hello"라는 url 요청이 왔을 때 해당하는 HelloServlet 아래의 service() 메소드가 실행
- 개발자는 HTTP 스펙을 매우 편리하게 사용
  - HttpServletRequest  : HTTP 요청 정보를 편리하게 제공
  - HttpServletResponse : HTTP 응답 정보를 편리하게 제공
- 서블릿 객체를 생성할 필요 없다! HttpServlet를 상속받아서 service() 메소드만 잘 구현하면 된다



### WAS 작동 순서

1. 클라이언트가 localhost:8080/hello 로 요청
2. 요청 메시지를 기반으로 request, response 객체 생성
3. request, response 객체를 서블릿 메소드의 파라미터로 넘겨 실행
4. response 객체 정보로 HTTP 응답 메시지를 생성

구체적인 WAS 작동 순서는 이 [글](https://bamdule.tistory.com/14) 참고



### 서블릿 컨테이너

서블릿을 지원하는 WAS로 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기를 관리 (예를 들어, 톰캣)

- 서블릿 컨테이너가 생성, 호출을 자동으로 해준다
- 서블릿 객체는 **싱글톤**으로 관리
  - 최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용
    - 서블릿 컨테이너 종료 시 함께 종료
  - 모든 고객은 동일한 서블릿 객체 인스턴스에 접근
    - **공유 변수 사용에 주의**
    - 고객의 요청이 올 때마다 생성하면 비효율적이므로
- JSP도 서블릿으로 변환되어 사용
- 동시 요청을 위한 멀티 쓰레드 처리 지원



### 서블릿 호출 주체 : 쓰레드

### 쓰레드

어플리케이션 코드를 하나하나 순차적으로 실행하는 쓰레드

- 자바 메인 메소드를 처음 실행하면 main이라는 이름의 쓰레드 실행
- 쓰레드가 없다면 자바 어플리케이션 실행 불가능
- 쓰레드는 한 번에 하나의 코드 라인만 수행
- 동시 처리가 필요하면 쓰레드를 추가로 생성



### 쓰레드 풀 : 사용

- 스레드가 필요하면 이미 생성된 스레드를 스레드 풀에 꺼내서 사용
- 사용을 종료하면 스레드 풀에 해당 스레드를 반납한다

- 최대 쓰레드가 모두 사용중이어서 스레드 풀에 쓰레드가 없으면?
  - 기다리는 요청은 거절하거나 특정 숫자만큼 대기하도록 설정 가능
  - 200개보다 더 많은 요청이 오면 요청을 대기 혹은 거절시킨다
    - 안전하게 서버를 유지할 수 있다

### 쓰레드 풀 : 장점

- 스레드 생성, 종료 비용 감소
  - 스레드가 미리 생성되어 있으므로, 스레드를 생성하고 종료하는 비용(CPU)가 절약되고, 응답시간이 빠르다
- 많은 요청을 안전하게 처리 가능
  - 생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있다

### 실무 팁

WAS의 주요 튜닝 포인트는 **최대 쓰레드(max thread) 수**

- 만약 낮게 설정하면? WAS는 안전하지만, 전체 서비스는 폭발

  - 동시 요청이 많을 경우 서버 리소스는 여유, 클라이언트는 응답 지연

- 값을 낮게 설정하면?

  - 동시 요청이 많으면 CPU와 메모리 리소스 임계점 초과로 서버 다운

- 장애 발생시?

  - 클라우드면 서버부터 늘리고 이후 튜닝

  - 클라우드가 아니면 평소에 열심히 튜닝하자

    

### 그럼 쓰레드 풀의 적정 숫자는?

- 어플리케이션 로직 복잡도, CPU, 메모리, IO 리소스 상황에 따라 모두 다르다

- 성능 테스트

  - 최대한 실제 서비스와 유사하게 성능 테스트 시도

    - 테스트 툴을 가지고 병목 테스트를 수행
    - 예 ) 아파치 ab, 제이미터, nGrinder

    

### WAS 멀티 쓰레드 지원

- 멀티 쓰레드에 대한 부분은 **WAS가 처리**
- 개발자가 멀티 쓰레드 관련 코드를 신경쓰지 않아도 된다
- 개발자는 마치 싱글 쓰레드 프로그래밍을 하듯 편리하게 소스 코드 개발 → 개발 생산성 높높
- 멀티 스레드 환경이므로 **싱글톤 객체(서블릿, 스프링 빈)은 주의해서 사용**하자





### HTTP 요청 

**쿼리 파라미터**

- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달

- 쿼리 파라미터

  - URL ?가 시작

  - 추가 파라미터는 &로 구분하면 된다

**HTML Form**

- content-type: application/x-www-form-urlencoded

- 메시지 바디에 쿼리 파리미터 형식으로 전달

**HTTP message body에 데이터를 직접 담아**

- HTTP API에서 주로 사용, JSON, XML, TEXT
- 데이터 형식은 주로 JSON 사용



###  HttpServletRequest

- 서버에서는 HttpServletRequest 가 제공하는 메서드를 통해 쿼리 파라미터를 편리하게 조회할 수 있다

**전체 파라미터 조회 : req.getParameterNames()**

```java
req.getParameterNames().asIterator()
                .forEachRemaining(paramName -> System.out.println(paramName + " = " + req.getParameter(paramName)));
```

**단일 파라미터 조회 : req.getParameter()**

```java
String username = req.getParameter("username");
        String age = req.getParameter("age");
```

**이름이 같은 복수 파라미터 조회 : req.getParameterValues()**

```java
String[] usernames = req.getParameterValues("username");
        for (String name : usernames) {
            System.out.println("username = " + name);
        }
```

> ✔️ 파라미터 이름이 중복일 때 request.getParameter()를 사용하면 request.getParameterValues()의 첫번째 값을 반환



### HttpServletResponse

**HttpServletResponse 역할**

- HTTP 응답 메시지 생성 (개발자가 직접 파싱 안해도 된다)
  - HTTP 응답코드 지정
  - 헤더 생성
  - 바디 생성



**HTTP 응답 코드 지정** 

```java
response.setStatus(HttpServletResponse.SC_OK);
```



**헤더 생성**

```java

response.setHeader("my-header", "hello");
response.setCharacterEncoding("utf-8");

// content type 설정
response.setHeader("Content-Type", "text/plain;charset=utf-8");
response.setContentType("text/plain");

// 쿠키 설정
response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
        Cookie cookie = new Cookie("myCookie", "good");
cookie.setMaxAge(600); //600초
        response.addCookie(cookie);


// 리다이렉트 설정 -> Location 헤더에 추가됨 302 상태코드일 경우 해당 url로 리다이렉트
response.sendRedirect("/basic/hello-form.html");
```



**바디 생성** 

```java
// [message body]
PrintWriter writer = resp.getWriter();
writer.println("ok");
```