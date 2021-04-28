---
title: '[Spring] IOC, DI, DL'
date: 2021-04-28 17:04:89
category: Spring
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/Spring.png)

# [Spring] IoC, DI, DL

## IOC : Inversion of Control

IoC는 DI(Dependency Injection)과 DL(Dependency LookUp)으로 구분할 수 있다.

## DI : Dependency Injection

전통적으로는 A라는 클래스 안에서 B가 필요하다면 A 안에서 B를 new해서 생성하여 사용했다. 그러나 A가 만들지 않고, 누군가가 만들어서 A에게 공급해 주는 방식을 `DI(Dependency Injection)`라고 한다. 이 때 A에게 공급해 주는 누군가는 `Container`이다.

DI는 IoC의 개념을 설명한 것과 같이 A에 B를 공급해주며 의존성을 주입하는 과정이다.

## DL : Dependency LookUp

DL은 A가 필요한 객체를 이름으로 찾는 과정이다.

```Java
Coffee cof = context.getBean("coffee1", Coffee.class);//필요한 객체를 얻어오는 메소드(id, 받아오는 형식)
Coffee cof2 = (Coffee)context.getBean("coffee1");//이렇게도 가능
```

![](https://images.velog.io/images/mulgyeol/post/576fe24a-7960-42cd-a947-50a4a06aa345/image.png)
