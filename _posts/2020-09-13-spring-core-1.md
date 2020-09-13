---
title: 스프링 프레임워크(Spring Framework)란?
author: hkx2r0i
date: 2020-09-13 17:39:00 +0900
categories: [Spring, Core]
tags: [Spring, Core]
math: true
---

# 스프링 프레임워크(Spring Framework)란?

> 스프링 프레임워크는 자바에서 주로 웹 서버 애플리케이션을 만들기 위해 사용되는 프레임워크로서 스프링(Spring)이라고 불림.

## 스프링 프레임워크의 특징

- DI(Dependency Injection) - 의존성 주입
- AOP(Aspect-Oriented Programming) - 관심지향 프로그래밍
- PSA(Portable Service Abstraction) - 일관된 추상화

### DI(Dependency Injection)

DI는 말 그대로 "의존성 주입"을 뜻하며, 스프링 프레임워크는 Framework 레벨에서 DI를 제공.<br />

스프링 프레임워크의 Container들은 Bean 객체들을 관리하는데 있어서 DI를 이용하며 이를 통해 라이프 사이클을 용이하게 관리.<br />

프레임워크 레벨의 관리를 통해 개발자들은 객체들간의 의존성에 신경을 덜 쓰고 Coupling을 줄일 수 있으며, 높은 재사용성과 가독성 있는 코드를 만들수 있으며 이것을 **제어의 역전(IoC)**이라 부름.<br />

## AOP(Aspect-Oriented Programming)

AOP는 공통된 관심사(Aspect)로 구분된 로직을 필요한 위치에 동적으로 삽입가능하도록 해주는 기술.<br />

반복되는 코드를 분리하여 중복된 코드를 줄여주고 핵심 로직에 대한 가독성을 높여주는 역할.<br />

## PSA(Portable Service Abstraction)

PSA는 환경의 변화와 관계없이 일관된 방식의 기술로의 접근 환경을 제공하려는 추상화 구조.<br />

PetClinic 예제를 보면 서블릿 애플리케이션임에도 불구하고 서블릿이 전혀 존재하지 않음.<br />
@Controller 애노테이션이 붙어있는 @GetMapping, @PostMapping과 같은 @RequestMapping 애노테이션을 통해 매핑함.<br />
내부적으로는 서블릿 기반으로 코드가 동작하지만 서블릿 기술은 추상화 계층에 숨겨져 있음.<br />

추상화 계층을 사용해 특정 기술을 내부에 숨기고 개발자에게 편의성을 제공해주는 것을 Service Abstraction이라 하며,<br />
해당 제공되는 기술을 다른 기술 스택으로 간편하게 바꿀 수 있는 확장성을 가지고 있는것을 PSA라 함.<br />

---
> 참고 : [예제로 배우는 스프링 입문 (개정판)](https://www.inflearn.com/course/spring_revised_edition#)