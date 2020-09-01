---
title: 객체지향 프로그래밍 - 2
author: hkx2r0i
date: 2020-09-01 15:09:00 +0900
categories: [Programming, 객체지향 프로그래밍]
tags: [객체지향 프로그래밍]
math: true
---

# 객체지향 프로그래밍 - 2

## 캡슐화(Encapsulation)

> - 데이터 + 해당 데이터 관련 메서드 묶음
> - 구현에 사용된 데이터의 상세 내용으 감추기 때문에 정보 은닉의 역할을 함
> - 외부에 영향 없이 객체 내부 구현 및 수정 가능

**캡슐화 하기 전**
```java
if (acc.getMembership() == REGULAR && acc.getExpDate().isAfter(now())) {
    ...
}

// 5년 이상 사용자 정회원 혜택 제공
if (acc.getMembership() == REGULAR || 
    (acc.getServiceDate().isBefore(fiveYearAgo) && addMonth(acc.getExpDate()).isAfter(now()))){
    ...
}
```

> ※ 캡슐화를 하지 않으면 요구사항의 변화에 따라 데이터 구조/사용에 변화에 따라 수정 될 코드의 양이 많아진다.

**캡슐화**
```java
if (acc.hasRegularPermission()) {
    ...
}

public class Account {
    private Membership membership;
    private Date expDate;

    public boolean hasRegularPermission() {
        return membership == REGULAR &&
                expDate.isAfter(now());
    }
}

// 5년 이상 사용자 정회원 혜택 제공
public class Account {
    private Membership membership;
    private Date expDate;

    public boolean hasRegularPermission() {
        return membership == REGULAR &&
                expDate.isAfter(now()) ||
                (serviceDate.isBefore(fiveYearAgo()) && addMonth(expDate).isAfter(now()));
    }
}
```

> ※ 캡슐화 된 기능을 사용하는 코드 영향 최소화.

## 캡슐화를 위한 2가지 규칙

- 데이터를 달라고 하지 말고 기능을 수행해 달라고 하기

```java
if (acc.getMembership() == REGULAR) {
    ...
}
```

이런 코드가 아닌

```java
if (acc.hasRegularPermission()) {
    ...
}
```

- Demeter의 법칙
    + 메서드에서 생성한 객체의 메서드만 호출
    + 파라미터로 받은 객체의 메서드만 호출
    + 필드로 참조하는 객체의 메서드만 호출

```java
// 변경 전
acc.getExpDate().isAfter(now);

// 변경 후
acc.isExpired()
```

```java
// 변경 전
Date date = acc.getExpDate();
date.isAfter(now);

// 변경 후
acc.isValid(now);
```
---
> 참고 : [객체 지향 프로그래밍 입문](https://www.inflearn.com/course/%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%9E%85%EB%AC%B8)