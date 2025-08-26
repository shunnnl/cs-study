> **📅 업로드 날짜**  
> 2025-08-26
> 
> **🗂 분류**  
> web  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/Spring-MVC-Framework-250a654e658a802ab946e1391754e3f7?source=copy_link)
> 


# MVC Framework

- **MVC**는 소프트웨어 아키텍처 패턴 (model , view , controller)
- **Spring MVC Framework** 는 Spring Framework가 제공하는 **MVC 패턴 기반 웹 프레임워크**
- 어플리케이션의 역할의 세 구간으로 나누어 설계함으로써 서로 간의 의존성 낮아짐
- 한 영역을 업데이트 하더라도 다른 곳에 영향을 주지 않음

<br>

## **MVC 진행 과정 (전통적인 서버 사이드 렌더링 SSR)**
<img width="512" height="391" alt="image (23)" src="https://github.com/user-attachments/assets/bb809a46-1d94-4dd7-a3d1-b418040ff2d4" />


- **클라이언트 요청**
    - 사용자가 웹 브라우저에서 특정 URL을 요청하면 HTTP Request가 스프링 서버로 전달한다.
- **DispatcherServlet 진입**
    - 스프링의 Front Controller인 `DispatcherServlet`이 요청을 가장 먼저 받는다.
- **Handler Mapping 탐색**
    - `DispatcherServlet`은 Handler Mapping을 사용해 해당 URL을 처리할 Controller(정확히는 Handler 메서드)를 찾아낸다
- **Controller 실행**
    - 찾은 Controller가 호출되어 비즈니스 로직을 수행한다.
    - 이때 필요한 데이터는 Service → Repository (DB 쿼리) 흐름을 통해 가져오며,결과 데이터를 Model 객체에 담는다.
- **Model과 View 정보 반환**
    - Controller는 데이터를 담은 Model과 보여줄 화면 정보를 `ModelAndView` 형태로 `DispatcherServlet`에 반환한다.
- **View Resolver 처리**
    - `DispatcherServlet`은 View Resolver에게 뷰 이름을 전달하고,
    - View Resolver는 해당 뷰 이름을 실제 뷰 파일 경로(JSP, Thymeleaf 등)로 변환해 View 객체를 반환한다
- **View 렌더링**
    - `DispatcherServlet`은 View에게 Model 데이터를 전달한다.
    - View는 이 데이터를 이용해 최종 HTML을 생성한다.
- **클라이언트 응답**
    - 완성된 HTML이 HTTP Response로 클라이언트(브라우저)에 전송되어 화면에 출력한다.
<br>
<br>

## Spring + React (프론트/백 분리, 클라이언트 사이드 렌더링, CSR)

- DispatcherServlet → HandlerMapping → Controller 실행 이 부분까지는 완전히 동일
- `View Resolver`는 쓰이지 않음 (더 이상 JSP/Thymeleaf 같은 서버 뷰 안 씀)
- Spring은 백엔드(API 서버) 역할만 하고 JSON 응답으로 끝남


<img width="800" height="800" alt="image (24)" src="https://github.com/user-attachments/assets/8ec29530-9b22-40cb-9003-1112ecf28e7d" />

## 용어 정리

**DispatcherServlet** 

- 모든 request를 처리하는 중심 컨트롤러로 생각,  서블릿 컨테이너에서 http 프로토콜을 통해 들어오는 모든 request에 대해 제일 앞단에서 중앙집중식으로 처리해주는 핵심적인 역할을 한다.

**Handler Mapping**

- 클라이언트의 request url을 어떤 컨트롤러가 처리해야 할 지 찾아서 Dispatcher Servlet에게 전달해주는 역할을 담당한다.
- 주로 `@RequestMapping`을 사용하는데, 핸들러가 이를 찾아주는 역할을 한다.

**Controller**

- 실질적인 요청을 처리하는 곳, Dispatcher Servlet이 프론트 컨트롤러라면, 이 곳은 백엔드 컨트롤러라고 볼 수 있다.

**View Resolver**

- 컨트롤러의 처리 결과를 만들 view를 결정해주는 역할을 담당한다. 다양한 종류가 있기 때문에 상황에 맞게 활용하면 된다.


```

- ModelAndView란?
    
    → 말 그대로 Model + View 정보를 담고 있음 (데이터 + 뷰이름)
    

- model 란?
    
    → 데이터를 생각하면 쉬움.
    
- ViewResolver의 역할?
    
    → viewname을 받아서 실제 렌더링 가능한 View 객체로 변환하는 역할

 ```
