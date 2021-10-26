> 학습한 내용을 정리한 포스트입니다. 혹시 잘못된 내용이 있다면 피드백 언제든 환영합니다 😀



## 웹서버 혼자서 할 수 없는 일

1. 동적인 컨텐츠 생성
2. 서버상 데이터 저장하기

> 도와줄 무언가가 필요하다!

## 웹 애플리케이션

- 웹서버가 실행 시키는 프로그램
- 인터넷을 기반으로 구성된 웹상에서 웹브라우저를 이용한 클라이언트와 HTML, JSP/Servlet 같은 자원을 가진 웹 서버 간에 **동적으로** 요청/응답 처리하는 프로세싱

## CGI(Common Gateway Interface)

- 웹서버와 웹 애플리케이션 사이에 데이터를 주고받기 위한 규칙
- 동적으로 실행되는 웹 프로그래밍의 작성 및 요청/응답 방법 등을 정해놓은 명세

## CGI 프로그램

- 다양한 언어로 작성이 가능하기 때문에 구현이 쉽다.
- 비 객체지향적이고 플랫폼에 의존적인 CGI도 있기 때문에 확장성이 떨어짐
- 여러 클라이언트가 요청 시 **동일한 프로세스**가 서버에 로드되기 때문에 사용자 요청이 많아질 수록 서버 부하가 심하게 된다.
- 주요기술
  - ASP, PHP

## 서블릿(Servlet)

- Server + Applet의 합성어
  - 클라이언트에게 서비스를 제공하는 작은 단위의 서버 프로그램
- 자바에서 동적인 웹페이지를 만들기 위한 기술
- 자바로 만든 웹 애플리케이션(CGI 프로그램)
- 다른 CGI 프로그램과 차이점
  - 웹서버와 직접 데이터를 주고 받지 않고, 서블릿 컨테이너에 의해 관리 됨
  - 여러 클라이언트가 요청해서 Thread로 처리 되기 떄문에 CGI와 비교해서 서버 부하가 크지 않다.

## 서블릿 컨테이너(= 웹 컨테이너)

- 정의
  - Java EE 사양 중에서 Servlet과 JSP 등 웹 관련 부분만 구현한 서버
- 역할
  - 통신 지원
    - 컨테이너는 서블릿과 웹서버가 서로 통신할 수 있는 손쉬운 방법 제공
    - 개발자가 소켓을 만드는 복잡한 작업을 안해도됨
    - 서블릿을 대신하여 CGI 규칙에 따라 웹 서버와 데이터를 주고 받음
      - 서블릿 개발자는 CGI 규칙에 대해 알 필요가 없음
      - 서블릿 컨테이너와 서블릿 사이의 규칙만 알면됨
  - 서블릿의 생명주기 관리
  - 멀티스레딩 지원
  - JSP 지원
- 제품
  - Tomcat, Jetty, Resin

## 서블릿 컨테이너 구동 절차

1. 클라이언트의 서블릿에 대한 요청이 들어오면, 웹 서버는 서블릿 컨테이너에게 요청을 전달합니다.

![img](https://blog.kakaocdn.net/dn/bqgrhu/btriWLrlFHr/d5Dsu1vurjXKUvmh29kK80/img.png)

1. 서블릿 컨테이너는 요청정보를 통해 HttpServletRequest와 HttpServlerResponse 객체를 생성합니다.
   ![img](https://blog.kakaocdn.net/dn/3ZU9e/btriZrFqW9C/MwkEpGzNtMmGZY4bwxB7o1/img.png)
2. 서블릿 컨테이너는 요청 정보를 분석하여, 서블릿을 찾습니다.
   만약 서블릿이 없다면, 서블릿 클래스를 로딩하고 인스턴스를 준비한 후 생성자를 호출합니다. 그리고 서블릿 초기화 메서드인 init()을 호출합니다.
   이후 서블릿 컨테이너는 서블릿을 할당하는 스레드를 생성하여 Request/Resonse 객체를 인자로 넘겨줍니다.

![img](https://blog.kakaocdn.net/dn/cdwJpS/btriYGW7pfi/3YUDIeE4KHg147THe8ZK51/img.png)

1. 컨테이너는 클라이언트 요청을 처리하는 서블릿의 service()를 호출합니다.

![img](https://blog.kakaocdn.net/dn/bC0Lug/btri1dNsxo3/bOVAlMkDOQuNUsnm4YDRlk/img.png)

1. service() 메서드에서 만든 동적인 결과를, HttpServlerResponse 객체에 실어 컨테이너에게 전달한다.
2. 컨테이너는 HttpServlerResponse 객체를 HTTP 프로토콜에 맞추어 클라이언트에 응답하는 것으로 요청처리를 완료합니다.
3. 사용이 끝난 스레드와 HttpServletRequest와 HttpServlerResponse 객체는 소멸합니다.
4. 서블릿은 싱글톤으로 한번 생성이되면, 컨테이너가 종료될 때까지 유지됩니다.

## Java EE

- 기업용 애플리케이션 제작에 필요한 기술들의 사양을 정의한 것
  - Servlet이라는 규칙 외에 JSP를 만들고 실행하는 규칙
  - EJB(Enterprise JavaBeans)라는 분산 컴포넌트에 관한 규칙
  - 웹 서비스에 관한 규칙 등..
- 기능 확장이 쉽고, 이기종 간의 이식이 쉬우며, 신뢰성과 보안성이 높고, 트랜잭션 관리와 분산 기능을 쉽게 구현할 수 있는 기능 제공

## WAS(Web Application Server) (= Java EE 구현체)

- Java에서 말하는 WAS란, Java EE 사양에 따라 개발된 서버를 말함.
- 클라이언트 서버 시스템 구조에서 서버 쪽 애플리케이션의 생성과 실행, 소멸을 관리하는 프로그램
- 상용제품
  - JEUS, WebLogic, WebSphere(IBM), JBoss Enterprise(레드헷)
- 오픈소스
  - Geronimon(아파치 재단), JBoss Community

------

## 서블릿 단점

- 동적으로 html을 생성하는 건 좋은데, 자바 코드로 html을 작성하니까, escape 처리나 문자열 연결 떄문에 가독성이 너무 떨어지고 불편함

```java
@WebServlet(name = "memberSaveServlet", urlPatterns = "/servlet/members/save")
public class MemberSaveServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        PrintWriter w = response.getWriter();
        w.write("<html>\n" +
                "<head>\n" +
                " <meta charset =\"UTF-8\">\n" +
                "</head>\n" +
                "<body>\n" +
                "성공\n" +
                "<ul>\n" +
                " <li> id = " + member.getId() +
                " </li>\n " +
                " <li> username = " + member.getUsername() +
                " </li>\n " +
                " <li> age = " + member.getAge() + " </li >\n " +
                "</ul>\n" +
                "<a href=\"/index.html\">메인</a>\n" +
                "</body>\n" +
                "</html>");
    }

}
```

## 템플릿 엔진

- 서블릿 처럼 자바 코드가 아닌 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경
- 종류
  - JSP, Thymeleaf ... 등

```java
// members.jsp

<%@ page import="hello.servlet.domain.member.MemberRepository" %>
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page import="java.util.List" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%

    // jsp도 결국엔 sevlet으로 변환됨
    // 즉, request, response, out은 임포트 없이 사용 가능
    MemberRepository memberRepository = MemberRepository.getInstance();
    List<Member> members = memberRepository.findAll();
%>

<html>
<head>
    <title>Title</title>
</head>
<body>

<a href="/index.html">메인</a>
<table>
    <thead>
    <th>id</th>
    <th>username</th>
    <th>age</th>
    </thead>
    <tbody>
    <%
        for (Member member : members) {
            out.write("<tr>");
            out.write("     <td>" + member.getId() + "</td>");
            out.write("     <td>" + member.getUsername() + "</td>");
            out.write("     <td>" + member.getAge() + "</td>");
            out.write("</tr>");
        }
    %>
    </tbody>
</table>


</body>
</html>
```

## JSP 구동원리

- JSP를 실행하면 실제로는 JSP로부터 생성된 서블릿이 실행된다.

1. 개발자는 서버에 JSP 파일을 작성해둡니다. 클라이언트가 JSP를 실행해달라고 요청하면, 서블릿 컨테이너는 JSP 파일에 대응하는 자바 서블릿을 찾아서 실행합니다.
2. 만약 JSP에 대응하는 서블릿이 없거나 JSP 파일이 변경되었다면, JSP 엔진을 통해 JSP 파일을 해석하여 서블릿 자바 소스를 생성합니다.
3. 서블릿 자바 소스는 자바 컴파일러를 통해 서블릿 클래스 파일로 컴파일됩니다.
   JSP파일을 바꿀 때마다 이 과정을 반복합니다.
4. JSP로 부터 생성된 서블릿은 서블릿 구동 방식에 따라 실행됩니다. 즉 서블릿의 service() 메서드가 호출되고, 출력 메서드를 통해 서블릿이 생성한 HTML 화면을 웹 브라우저로 보냅니다.

## 서블릿과 JSP의 한계

- 너무 많은 역할
  - 비즈니스 로직과 뷰 로직이 합쳐져 있음
- ex) 올인원 PC
  - 특정 부품이 고장 나면 전체를 교환해야 하며, 일부 부품만 교체하거나 특정 기능만 업그레이드 할 수 없음

## MVC 패턴 등장

- 비즈니스 로직은 서블릿 처럼 다른곳에서 처리하고, JSP는 목적에 맞게 HTML로 화면(View)을 그리는 일에 집중

## MVC 패턴 한계

- 중복이 많고, 필요 없는 코드 존재
- ex) forward, viewPath 중복
- 각각 존재해서 공통 처리가 어렵다.

## 프론트 컨트롤러 패턴

- 웹 어플리케이션 개발 시 사용자의 요청을 처리하기 위한 최초 진입점을 정의하고 사용하는 패턴을 의미
- 모든 사용자의 요청을 집중화시키면 요청을 분산시켜 발생되는 중복된 코드를 제거할 수 있고, 사용자의 요청을 일관된 방법으로 관리할 수 있다는 장점이 있음

------

###  

### Reference.

- 자바 웹 개발 워크북 - 저자 엄진영
- 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
- [CGI](https://finerss.tistory.com/entry/CGICommon-Gateway-Interface?category=356457)
- [서블릿 컨테이너](https://finerss.tistory.com/entry/서블릿-컨테이너Servlet-Container?category=356457)