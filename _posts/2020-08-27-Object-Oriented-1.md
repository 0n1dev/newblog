---
title: 객체지향 프로그래밍 - 1
author: hkx2r0i
date: 2020-08-27 21:08:00 +0800
categories: [Programming, 객체지향 프로그래밍]
tags: [객체지향 프로그래밍]
math: true
---

# 객체지향 프로그래밍 - 1

**절차 지향 vs 객체 지향 : 절차 지향**

> 절차 지향은 데이터를 여러 프로시저가 공유하는 방식.

절차 지향 방식은 시간이 흐를수록 코드를 수정하기가 어렵고 복잡해지는 단점이 있다.

```java
// 이메일 인증 추가 전
Account account = findOne(id);
if (account.getState() == DELETED || account.getBlockCount() > 0) {
    ...
}

// 이메일 인증 추가 후
Account account = findOne(id);
if (account.getState() == DELETED || account.getBlockCount() > 0
    || account.getEmailVerifyStatus() == 0) {
    ...
}
```

**절차 지향 vs 객체 지향 : 객체 지향**

> 객체 지향은 데이터와 프로시저를 객체라는 단위로 묶어서 특정 객체의 데이터는 해당 객체의 프로시저만 접근이 가능 하도록 하는 방식.

설계가 어렵다는 단점이 있다. 시간이 흐를수록 코드를 수정하기가 쉽다는 장점이 있다.

## 객체

객체는 물리적으로 데이터와 프로시저를 가지고 있지만, 객체의 핵심은 객체가 제공하는 기능이다.

> ex) 리모컨
> - 볼륨 증가하기 기능
> - 볼륨 감소하기 기능

```java
public class RemoteController {
    public void increaseVolume(int vol) {
        ...
    }

    public void decreaseVolume(int vol) {
        ...
    }

    public int volume() {
        ...
    }
}
```

※ 객체지향 프로그래밍은 객체들끼리 기능을 사용(메서드 호출)해서 연결.

---
> 참고 : [객체 지향 프로그래밍 입문](https://www.inflearn.com/course/%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%9E%85%EB%AC%B8)