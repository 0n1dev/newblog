---
title: Refactoring-1
author: hkx2r0i
date: 2020-08-26 21:26:00 +0800
categories: [Programming, Refactoring]
tags: [Refactoring]
math: true
---

# Refactoring - 1

::: tip
리팩터링 2판 책을 보며 공부 시작.
:::

## 서두

취업을 하기 위해 모집 공고를 보면 공통적으로 들어간 단어가 있다.

**"협업"**

이처럼 회사에서 개발자들에게 중요하게 요구하는 자세는 협업의 자세다.
자신이 혼자서 일하겠다면 문제가 없겠지만 직장인으로서 회사를 다니며 다른 개발자들과 프로젝트를 진행을한다면
자신이 설계한 프로젝트의 로직을 어느 누가 봤을때 어떠한 로직인지 쉽게 파악이 가능하도록 설계하는것이 중요하다고 생각한다.

## 예시 코드

### plays.json
 
```json
{
    "hamlet": {"name": "Hamlet", "type": "tragedy"},
    "as-like": {"name": "As You Like It", "type": "comedy"},
    "othello": {"name": "Othello", "type": "tragedy"}
}
```

### invoices.json

```json
[
    {
        "customer": "BigCo",
        "performances": [
            {
                "playId": "hamlet",
                "audience": 55
            },
            {
                "playId": "as-like",
                "audience": 35
            },
            {
                "playId": "othello",
                "audience": 40
            },
        ]
    }
]
```

### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    const format = new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", minimumFractionDigits: 2}).format;
    
    for (let perf of invoice.performances) {
        const play = plays[perf.playID];
        let thisAmount = 0;

        switch (play.type) {
        case "tragedy": // 비극
            thisAmount = 40000;
            if (perf.audience > 30) {
                thisAmount += 1000 * (perf.audience - 30);
            }
            break;
        case "comedy": // 희극
            thisAmount = 30000;
            if (perf.audience > 20) {
                thisAmount += 10000 + 500 * (perf.audience - 20);
            }
            break;
        default:
            throw new Error(`알 수 없는 장르: ${play.type}`);
        }

        // 포인트 적립
        volumeCredits += Math.max(perf.audience - 30, 0);
        // 희극 관객 5명마다 추가 포인트 제공
        if ("comedy" === play.type) volumeCredits += Math.floor(perf.audience / 5);

        // 청구 내역을 출력한다.
        result += ` ${play.name}: ${format(thisAmount/100)} (${perf.audience}석\n)`;
        totalAmount += thisAmount;
    }

    result += `총액: ${format(totalAmount/100)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

예시 코드를 보면 짧은 코드라 이해할 구조가 보이지 않는다. 하지만 이러한 코드가 수백 수천 줄짜리 코드라 생각해보면 간단한 인라인 함수여도 이해하기 쉽지 않을것이다.

## 함수 쪼개기

리팩터링을할 때 가장 먼저 전체 동작중 나눌 수 있는 지점을 찾는것이 중요하다.
statement() 함수를 보면 중간 쯤 switch문이 가장 눈에 뛴다.

> 별도 함수로 빼냈을 때 유효범위를 벗어나는 변수, 즉 새 함수에서 곧바로 사용할 수 없는 변수를 체크해야 한다.

### switch문 빼내기

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    const format = new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", minimumFractionDigits: 2}).format;
    
    for (let perf of invoice.performances) {
        const play = plays[perf.playID];
        let thisAmount = amountFor(pref, play);

        // 포인트 적립
        volumeCredits += Math.max(perf.audience - 30, 0);
        // 희극 관객 5명마다 추가 포인트 제공
        if ("comedy" === play.type) volumeCredits += Math.floor(perf.audience / 5);

        // 청구 내역을 출력한다.
        result += ` ${play.name}: ${format(thisAmount/100)} (${perf.audience}석\n)`;
        totalAmount += thisAmount;
    }

    result += `총액: ${format(totalAmount/100)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

#### amountFor Function

```js
function amountFor(perf, play) {
    let thisAmount = 0; // 변수 초기화

    switch (play.type) {
    case "tragedy": // 비극
        thisAmount = 40000;
        if (perf.audience > 30) {
            thisAmount += 1000 * (perf.audience - 30);
        }
        break;
    case "comedy": // 희극
        thisAmount = 30000;
        if (perf.audience > 20) {
            thisAmount += 10000 + 500 * (perf.audience - 20);
        }
        break;
    default:
        throw new Error(`알 수 없는 장르: ${play.type}`);
    }

    return thisAmount; // 함수 안에서 값이 바뀌는 변수 반환
}
```

> 리팩터링 후에는 항상 테스트하는 습관을 들이는 것이 중요하다. 너무 많이 수정하다 실수를 하면 디버깅하기가 어려워서 결과적으로 작업 시간이 늘어난다.

다음은 amounFor() 함수의 **변수 이름을 명확**하게 바꿔보자.

#### amountFor Function

```js
function amountFor(aPerformance, play) { // (명확한 이름으로 변경)
    let result = 0; // 변수 초기화 (명확한 이름으로 변경)

    switch (play.type) {
    case "tragedy": // 비극
        result = 40000;
        if (aPerformance.audience > 30) {
            result += 1000 * (aPerformance.audience - 30);
        }
        break;
    case "comedy": // 희극
        result = 30000;
        if (aPerformance.audience > 20) {
            result += 10000 + 500 * (aPerformance.audience - 20);
        }
        break;
    default:
        throw new Error(`알 수 없는 장르: ${play.type}`);
    }

    return result; // 함수 안에서 값이 바뀌는 변수 반환
}
```

자바스크립트 같은 동적 타입 언어를 사용할 때 매개변수 이름에 접두어로 타입 이름을 적는게 도움이 된다.
지금처럼 매개변수의 역할이 뚜렷하지 않을 때 부정 관사(a/an)을 붙인다.

> 컴퓨터가 이해하는 코드는 바보도 작성할 수 있다. 사람이 이해하도록 작성하는 프로그래머가 진정한 실력자다.

### play 변수 제거하기

amountFor() 함수의 매개변수를 보면 aPerformance는 루프 변수에서 오기 때문에 값이 변경되지만, play는 aPerformance에서 얻기 때문에 매개변수로 전달할 필요가 없다.

> 긴 함수를 쪼갤 때 play 같은 임시 변수들 때문에 로컬 범위에 존재하는 이름이 늘어나면 추출 작업이 복잡해지기 때문에 제거하는것이 좋다.

#### playFor Function

```js
function playFor(aPerformance) {
    return plays[aPerformance.playID];
}
```

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    const format = new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", minimumFractionDigits: 2}).format;
    
    for (let perf of invoice.performances) {
        const play = playFor(perf);
        let thisAmount = amountFor(pref, play);

        // 포인트 적립
        volumeCredits += Math.max(perf.audience - 30, 0);
        // 희극 관객 5명마다 추가 포인트 제공
        if ("comedy" === play.type) volumeCredits += Math.floor(perf.audience / 5);

        // 청구 내역을 출력한다.
        result += ` ${play.name}: ${format(thisAmount/100)} (${perf.audience}석\n)`;
        totalAmount += thisAmount;
    }

    result += `총액: ${format(totalAmount/100)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

테스트 후 **변수 인라인하기**를 적용한다.

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    const format = new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", minimumFractionDigits: 2}).format;
    
    for (let perf of invoice.performances) {
        // 포인트 적립
        volumeCredits += Math.max(perf.audience - 30, 0);
        // 희극 관객 5명마다 추가 포인트 제공
        if ("comedy" === playFor(perf).type) volumeCredits += Math.floor(perf.audience / 5);

        // 청구 내역을 출력한다.
        result += ` ${playFor(perf).name}: ${format(amountFor(pref)/100)} (${perf.audience}석\n)`;
        totalAmount += amountFor(pref);
    }

    result += `총액: ${format(totalAmount/100)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

#### amountFor Function

```js
function amountFor(aPerformance) { // (명확한 이름으로 변경)
    let result = 0; // 변수 초기화 (명확한 이름으로 변경)

    switch (playFor(aPerformance).type) {
    case "tragedy": // 비극
        result = 40000;
        if (aPerformance.audience > 30) {
            result += 1000 * (aPerformance.audience - 30);
        }
        break;
    case "comedy": // 희극
        result = 30000;
        if (aPerformance.audience > 20) {
            result += 10000 + 500 * (aPerformance.audience - 20);
        }
        break;
    default:
        throw new Error(`알 수 없는 장르: ${playFor(aPerformance).type}`);
    }

    return result; // 함수 안에서 값이 바뀌는 변수 반환
}
```

### 적립 포인트 계산 코드 추출하기

amountFor와 동일하게 pref만 전달하면 된다.

#### volumeCreditsFor Function

```js
function volumeCreditsFor(aPerformance) { // (명확한 이름으로 변경)
    let result = 0;    
    // 포인트 적립
    result += Math.max(aPerformance.audience - 30, 0);
    // 희극 관객 5명마다 추가 포인트 제공
    if ("comedy" === playFor(aPerformance).type)
        result += Math.floor(aPerformance.audience / 5);
    return result;
}
```

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    const format = new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", minimumFractionDigits: 2}).format;
    
    for (let perf of invoice.performances) {
        // 포인트 적립
        volumeCredits += volumeCreditsFor(perf);

        // 청구 내역을 출력한다.
        result += ` ${playFor(perf).name}: ${format(amountFor(pref)/100)} (${perf.audience}석\n)`;
        totalAmount += amountFor(pref);
    }

    result += `총액: ${format(totalAmount/100)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

### format 변수 제거하기

format은 임시 변수에 함수를 대입한 형태인데, 이러한 임시 변수를 함수를 직접 선언해 사용하도록 변경하겠다.

#### format Function

```js
function format(aNumber) {
    return new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", 
                        minimumFractionDigits: 2}).format(aNumber); 
}
```

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    
    for (let perf of invoice.performances) {
        // 포인트 적립
        volumeCredits += volumeCreditsFor(perf);

        // 청구 내역을 출력한다.
        result += ` ${playFor(perf).name}: ${format(amountFor(pref)/100)} (${perf.audience}석\n)`;
        totalAmount += amountFor(pref);
    }

    result += `총액: ${format(totalAmount/100)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

함수 이름 변경이 필요하다. format은 함수가 하는일에 대해 충분한 설명을 해주지 못한다. 또 템플릿 문자열 안에서 사용될 이름이라 장황하면 맞지 않다.
함수의 역할에 맞는 이름을 골라서 **함수 선언 바꾸기**를 적용하는것이 중요하다.

#### usd Function

```js
function usd(aNumber) {
    return new Intl.NumberFormat("en-US",
                    { style: "currency", currency: "USD", 
                        minimumFractionDigits: 2}).format(aNumber/100); // 단위 변환 로직도 함수에 추가.
}
```

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let volumeCredits = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    
    for (let perf of invoice.performances) {
        // 포인트 적립
        volumeCredits += volumeCreditsFor(perf);

        // 청구 내역을 출력한다.
        result += ` ${playFor(perf).name}: ${usd(amountFor(pref))} (${perf.audience}석\n)`;
        totalAmount += amountFor(pref);
    }

    result += `총액: ${usd(totalAmount)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

### volumeCredits 변수 제거하기
---

volumeCredits 변수는 반복문을 돌 때마다 값을 누적하기 때문에 리팩터링하기가 까다롭다. 따라서 먼저 **반복문 쪼개기**로 값이 누적되는 부분을 따로 빼냈다.

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    
    for (let perf of invoice.performances) {
        // 청구 내역을 출력한다.
        result += ` ${playFor(perf).name}: ${usd(amountFor(pref))} (${perf.audience}석\n)`;
        totalAmount += amountFor(pref);
    }

    let volumeCredits = 0; // 초기화를 반복문 바로 앞으로 이동
    for (let perf of invoice.performances) {
        // 포인트 적립
        volumeCredits += volumeCreditsFor(perf);
    }

    result += `총액: ${usd(totalAmount)}\n`;
    result += `적립 포인트: ${volumeCredits}점\n`;
    return result;
}
```

반복문을 쪼개면 성능 저하 이슈가 생기지 않을까라는 생각할 수 있다.<br>
이 정도는 성능에 미치는 영향이 대체로 미미할 때가 많다. <br>

하지만 "대체로 그렇다"와 "항상 그렇다"는 엄연히 다르다. 때로는 성능에 상당한 영향을 주기도한다.<br>
그래도 잘 다듬어진 코드가 성능 개선 작업도 수월하고 더 효과적으로 수행할 수 있기 때문에, 결과적으로 더 빠른 코드를 얻을수 있다.<br>

다음은 volumeCredits 값 갱신과 관련된 코드를 모아서 함수로 추출한다.

#### totalVolumeCredits Function

```js
function totalVolumeCredits(invoice) {
    let result = 0;
    for (let perf of invoice.performances) {
        // 포인트 적립
        result += volumeCreditsFor(perf);
    }
    
    return result;
}
```

#### statement Function

```js
function statement(invoice, plays) {
    let totalAmount = 0;
    let result = `청구 내역 (고객명: ${invoce.customer})\n`;
    
    for (let perf of invoice.performances) {
        // 청구 내역을 출력한다.
        result += ` ${playFor(perf).name}: ${usd(amountFor(pref))} (${perf.audience}석\n)`;
        totalAmount += amountFor(pref);
    }

    result += `총액: ${usd(totalAmount)}\n`;
    result += `적립 포인트: ${totalVolumeCredits(invoice)}점\n`; // 인라인
    return result;
}
```

## 마무리

해당 코드는 한빛미디어에서 출판한 리팩터링 2판을 보고 작성된 코드이다. <br>
아마 동작은 안될것이다. 그 이유는 playFor() 함수를 보면 plays를 받아와야 하는데 받아오는 부분이 없다 그리고 해당 부분을 받아오게 된다면 모든 함수가 plays.json의 데이터가 필요할텐데... <br>
책에는 이에대한 언급은 없다. <br>
동작을 떠나서 <font color="red">**마틴 파울러가 전달하고 싶은 내용**</font>이 무엇인지 요점을 파악하고 읽는것에 중점을 두고 정독을 목표로 가져야겠다. <br>