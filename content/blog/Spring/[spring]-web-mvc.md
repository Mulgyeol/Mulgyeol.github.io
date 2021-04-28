---
title: [Spring] Web MVC
date: 2021-04-28 18:04:29
category: Spring
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/Spring.png)

# [Spring] Web MVC

## Spring MVC란?

- 스프링이 제공하는 서블릿 시반의 MVC 프레임워크이다.
- 프론트 컨트롤러 역할을 하는 DispatcherServlet을 사용한다.
- 스프링이 제공하는 AOP, 트랜잭션 처리, DI 등의 기능을 그대로 사용하면서 MVC패턴에 기반하여 웹 어플리케이션을 개발할 수 있다.

## Spring MVC 구성요소

1. DispatcherServlet : 모든 클라이언트의 요청을 전달받는다. Controller에게 클라이언트의 요청을 전달하고, Controller가 리턴한 결과값을 View에 전달하여 알맞은 응답을 생성하도록 한다.
2. HandlerMapping : 클라이언트의 요청 URL을 어떤 컨트롤러가 처리할지 결정한다.
3. Controller : 클라이언트의 요청을 처리한 뒤, 결과를 DispatcherServlet에게 알려준다.
4. ModelAndView : Controller가가 처리한 결과 정보 및 뷰 선택에 필요한 정보들을 담는다.
5. ViewResolver : Controller가의 처리 결과를 생성 할 View를 결정한다.
6. View : Controller의 처리 결과 화면을 생성한다. (JSP Page)

## Spring MVC 흐름도

![](https://images.velog.io/images/mulgyeol/post/13ddc218-6edd-45f8-98c0-dbaf588f1e28/image.png)
[출처](https://grokonez.com/spring-framework/spring-mvc-rest-difference-between-controller-and-restcontroller)

1. 클라이언트의 처리 요청(URL) (Client-> DispatcherServlet(Client의 모든 요청을 받는 FrontController))
2. 요청 URL과 매핑되는 Controller를 검색해서 FC에 알려준다. (DispatcherServlet -> HandlerMapping -> DispatcherServlet)
3. `2`에서 알아낸 Controller에게 처리 요청을 한다.(DispatcherServlet -> Controller -> Model)
4. DispatcherServlet에게 ModelAndView(작업결과)를 리턴한다.(Model -> Controller -> DispatcherServlet)
   - Model : DB에서 꺼내온 데이터
   - View : 화면 정보, 어디에 데이터를 보여줄 것인가.
     - 이때의 화면 정보는 `논리적인 화면 정보`이다.
     - `초기화면에 뿌려주세요`, `목록 화면에 뿌려주세요` 를 의미하는 것이고
     - 파일 명이나 경로를 의미하지는 앟는다.
5. Controller의 실행 결과를 보여줄 View를 검색해서 돌려준다. (DispatcherServlet -> ViewResolver -> DispatcherServlet)
   - 이 때의 View는 경로, 파일명, 파일 종류 등 View와 관련된 모든 처리를 한다.
   - 즉, View의 물리적 정보가 결정된다.
6. 응답을 출력한다. (DispatcherServlet -> View(Jsp) -> Client)

   - JSP안에 데이터가 들어가고, 클라이언트에게 보여진다.

## SpringMVC의 Container

SpringMVC의 Continer는 2개가 있다.

- Root Spring Container
  - 프로젝트 안의 모든 자원들이 공유할 객체들을 담아 놓는다.
  - The definition of the Root Spring Container shared by all Servlets and Filters
  - `root-cntext.xml`을 읽어다가 객체를 만든다.
- DispatcherServlet Container(FrontController)
  - 이 안에 생성된 객체들은 DispatcherServlet만 사용할 수 있다.
  - 주로 웹 관련해서 처리할 내용
  - `servlet-context.xml`을 읽어다가 객체를 만든다.
  - `servlet-mapping`과 쌍을 이룬다.
