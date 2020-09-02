---
title: Game Hacking - 치트엔진 튜토리얼 Part.2
author: hkx2r0i
date: 2020-09-02 19:02:00 +0900
categories: [Security, Game Hacking]
tags: [Game Hacking]
math: true
---

# Game Hacking - 치트엔진 튜토리얼 Part.2

> <font color="red">경고, 허가 받지 않은 공간에서 테스트를 금지합니다.</font> 악의적인 목적으로 이용할 시 발생할 수 있는 모든 법적 책임은 이용자 본인에게 있습니다.

## 1.2 치트엔진 튜토리얼

### 1.2.2 Unknown initial value

치트엔진 튜토리얼 3단계에서 풀어 볼 문제는 실제 게임 해킹에서 가장 많이 사용되는 방법이다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/1.png)

> 3단계 요약: 초기 값 0 ~ 500 사이에 알 수 없는 값을 가진 변수가 있고, "Hit me" 버튼을 누를 때마다 값이 감소한다. 이 변수의 값을 5000으로 바꿔라.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/2.png)

Scan Type을 Unknown initial value로 선택하고, "First Scan" 버튼을 클릭해준다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/3.png)

리스트에 스캔 결과가 안보일텐데 이유는 값이 많으면 해당 리스트를 보여주는데 오랜 시간이 걸리기 때문에 스캔 결과 리스트의 항목 개수만 보여준다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/5.png)

튜토리얼 창에서 "Hit me" 버튼을 클릭하면 값이 얼마나 감소했는지 보여준다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/4.png)

치트엔진에서 Scan Type을 클릭해 보면 

- Decreased value
- Decreased value by ...

두가지의 항목을 볼 수 있다. <br />
여기서 첫번째는 감소는 했는데 얼마나 감소했는지 알 수 없을때 사용하고,<br />
두번째는 감소한 수치를 알고 있을때 사용한다.<br />

우리는 2만큼 감소한 사실을 알고있기 때문에 두번째를 선택 후 Value에 2를 넣고 "Next Scan" 버튼을 클릭한다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/6.png)

값이 4개가 나온다. 만약에 값이 4개가 아니라 더 많이 나온다면 위 방법을 반복하여 값을 필터해보자.<br />

문제에서는 0 ~ 500 사이의 초기값을 가지고 있다고 했으니 "441"의 값을 가지고 있는 메모리가 우리가 바꿔야 할 메모리 주소라는것을 파악할 수 있다.

### 1.2.3 Floating points

치트엔진 튜토리얼 4단계는 Value Type만 Float, Double로 바꿔서 풀면되어 풀이를 생략하겠다.

> 4단계 요약: "Hit me" 버튼을 클릭하면 Health 값이, "Fire" 버튼을 클릭하면 Ammo 값이 0.5 감소한다.<br />
> 두가지 값을 5000 또는 더 높게 바꿔라.

### 1.2.4 Code finder

치트엔진 튜토리얼 5단계는 "Find out what writes to this address" 를 사용하는 방법에 대해 배워보는 단계이다.<br />
**Find out what writes to this address**는 해당 주소에 값을 변경하는 기능을 수행하는 어셈블리어를 찾아주는 기능이다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/7.png)

> 5단계 요약: "Change value" 버튼을 클릭하면 값이 바뀌는데, **Find out what writes to this address** 기능을 사용하여 해당 코드가 아무 동작 안하도록 변경해라.


![치트엔진 튜토리얼](/assets/img/game-hacking-3/8.png)

우선 초기값 "100"이 저장되어 있는 메모리 주소를 찾아준다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/9.png)

해당 주소를 우클릭후 **Find out what writes to this address** 를 클릭 후 다시 "Change value" 버튼을 클릭해보자.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/10.png)

"Change value" 버튼을 클릭할 때 마다 Count의 숫자가 계속해서 올라가는것을 알 수 있다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/11.png)

"Show disassembler" 버튼을 클릭하면 좀 더 자세하게 디버깅 할 수 있다.

```
mov edx, [ebp - 10]
mov [eax], edx
```

![치트엔진 튜토리얼](/assets/img/game-hacking-3/12.png)

해당 코드에 브레이크 포인트(F5)를 걸고 디버깅을 해보면 ebp - 10 주소의 값을 edx 레지스터에 넣어주고,<br />
다시 edx에 저장된 값을 eax 주소에 값을 넣어준다. <br />

> 인텔 기반 CPU는 대부분 리틀 엔디안(little endian) 방식으로 데이터를 저장하기 때문에 거꾸로 읽어줘야한다.

본론으로 돌아와 mov [eax], edx 부분을 동작안하도록 바꿔주면 이번 단계를 클리어 할 수 있다.

![치트엔진 튜토리얼](/assets/img/game-hacking-3/12.png)

해당 어셈블리어 코드를 더블 클릭하여 nop로 변경해주고, "Change value"를 클릭해주면 랜덤으로 값이 변경되는 기능이 사라진것을 알 수 있다.