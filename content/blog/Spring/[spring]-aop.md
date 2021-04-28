---
title: '[Spring] AOP(Aspect-Oriented Programming)'
date: 2021-04-28 18:04:58
category: spring
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/Spring.png)

# AOP, Aspect-Oriented Programming

AOP의 관점에서는 전체 어플리케이션 구현 기능을 두 가지로 구분한다.

- 핵심업무(CoreConcern) : biz logic
- 공통업무(Cross-cutting Concern) : 핵심 업무를 도와주는 방복적이고 부가적인 업무

## Aspect 지향과 회단 관점 분리

- Aspect 지향의 중요 개념 : Separation of Cross-Cutting Concern
  - 공통 업무사항을 핵심 업무로 부터 떨어뜨려 놓고, 핵심 업무에 집중한다.
- OOP 객체지향에서의 관점의 분리 : 모듈로서 기능을 분리한다.

## AOP의 횡단 관점의 분리와 위빙

모듈들이 핵심기능을 수행하다가, 공통 기능을 만나면 AOP프레임워크를 호출한다. AOP프레임워크는 Logging을 호출하고 적절하게 공통 기능을 모듈에 동적으로 넣어준다. 이렇게 동적으로 넣어주는 과정을 `위빙(Weaving)` 이라고 한다.

핵심기능을 하는 모듈에는 공통 기능을 호출하는 메서드가 없다. 이 기능은 AOP 프레임워크가 동적으로 그냥 넣어주는 것이다.

## Spring AOP에서의 용어

- Joinpoint : `클래스의 인스턴스 생성 시점`, `메소드 호출 시점` 및 `예외 발생 시점`과 같이 애플리케이션을 실행할 때 특정 작업이 시작되는 시점
  - 공통기능이 들어갈 수 있는 시점으로, `~할 때`로 정의할 수 있다.
- Pointcut : 여러 개의 Joinpoint를 하나로 결합한(묶은) 것, 적용시켜야 하는 공통 기능이 같은 Joinpoint
- Advice : Joinpoint에 삽입되어져 동작할 수 있는 코드, 각각의 핵심기능을 하던 중 AOP프레임워크에 의해 끼워들어져 작동하는 `공통기능`.
- Weaving : Advice를 핵심 로직 코드에 삽입하는 것(삽입, 끼워들어져 가는 것)
- Target : 핵심 로직을 구현하는 클래스
- Aspect : 여러 객체에 공통으로 적용되는 공통 관점 사항(Advice를 가지고 있는 클래스)

![](https://images.velog.io/images/mulgyeol/post/95596810-0c3c-402d-aa8d-59edae1580c2/image.png)

- 위의 그림에서 글쓰기, 수정하기, 삭제하기는 로그인 상태에서 가능하게 구현해야 하기 때문에 Advice Y는 `로그인 체크` 기능이 적절할 것이다.
- Advice X는 로그를 남기는 것을 예로 들 수 있다.

## Spring AOP의 특징

스프링과는 상관없이 AOP를 지원해주는 여러 프레임워크들이 있지만, Spring에서의 AOP의 특징을 알아보고자 한다. Spring AOP는 AOPJ와 같은 프레임워크의 기능을 모두 제공해주지는 않지만 일부 기능을 지원한다.

Spring AOP의 특징이라 하면 Weaving이 어떤 방식으로 이루어지는지를 말한다.

- Proxy 기반의 AOP : Target 객체에 대한 proxy를 만들어 제공
- Proxy

  - `Target을 감싸는 구조`(혹은 Target 앞에 위치하는 구조)로 runtime에 생성
  - Advice를 Target 객체에 적용

![](https://images.velog.io/images/mulgyeol/post/1b54106f-9ace-4fe6-a459-9fdd141c4095/image.png)

### 구현의 예

위의 글쓰기 구조에서 클라이언트가 `글쓰기`를 요청한다고 했을 때, Target으로 넘어가기 전에 Proxy에서 Aspect의 Adivce(로그인 체크)가 실행되고, 로그인이 되어있을 경우 Target으로 연결한다. 그렇지 않다면 다른 로직을 수행하게끔 구현한다.

![](https://images.velog.io/images/mulgyeol/post/14e69ef9-4d2a-43da-a411-e3bf83656d44/image.png)

## Advice Type

| Advice type    | remark                                       |
| -------------- | -------------------------------------------- |
| Before         | Target의 핵심 메소드 실행 전                 |
| After          | Target의 핵심 메소드 실행 후                 |
| AfterReturning | Target의 핵심 메소드 실행하고 값을 리턴한 후 |
| Round          | Target의 핵심 메소드 실행 전/후              |
| AfterThrowing  | Target의 핵심 메소드 실행 중 예외 발생 시    |

### After 혹은 Round 구현의 예시

![](https://images.velog.io/images/mulgyeol/post/65182149-27b9-40f1-9abf-9b3e42c62896/image.png)

## Pointcut 표현식

위에서 Pointcut은 joinpoint 를 여러 개 모아놓은 것이라고 했다.
Pointcut을 표현식에 대해 알아보자.

![](https://images.velog.io/images/mulgyeol/post/1b9759a8-a221-4df8-88be-3f71dd4c229d/image.png)

`execution(* com.spring.aop.test1.*Service.find*(..))`

1. Designator : `execution`, 지정어
2. return type : `*`, 리턴타입
3. package : `com.spring.aop.test1`, 이 패키지 안에
4. type : `*Service`, 이 클래스가 있고 거기서
5. method : `find*`, 메소드 이름이 이거고, 리턴타입이 `2`면서 파라미터가 `6`인 메소드가 호출되면 끼어들어갈거야.
6. params : `(..)`, 파라미터
