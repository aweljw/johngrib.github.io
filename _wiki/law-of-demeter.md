---
layout  : wiki
title   : 디미터 법칙 (The Law of Demeter)
summary : 객체는 그것이 내부적으로 보유하고 있거나 메시지를 통해 확보한 정보만 가지고 의사 결정을 내려야 한다
date    : 2020-03-22 12:13:49 +0900
updated : 2020-03-22 14:01:15 +0900
tag     : law oop
toc     : true
public  : true
parent  : [[proverb]]
latex   : false
---
* TOC
{:toc}

## 어원

- 실용주의 프로그래머(역자 주석)

> 디미터(Demeter)는 그리스 신화의 농업, 결혼, 사회 질서의 여신이다.
디미터라는 프로젝트에서 이 법칙을 발견했다고 해서 그렇게 이름 붙였다.
그 프로젝트는 소프트웨어를 '성장'시키는 방법을 사용한다(디미터가 농업의 여신이라는 점을 기억하라).[^andrew-227]

- 오브젝트

> 디미터 법칙은 "Object-Oriented Programming: An Objective Sense of Style"에서 처음으로 소개된 개념으로 이 글의 저자들은 디미터라는 이름의 프로젝트를 진행하던 도중 객체들의 협력 경로를 제한하면 결합도를 효과적으로 낮출 수 있다는 사실을 발견했다.[^cho-184]

## 의미

### From: 테스트 주도 개발로 배우는 객체 지향 설계와 실천

> 서로 메시지를 전달하는 객체가 있다면 객체는 서로 무슨 이야기를 할까?
경험상 객체를 호출할 땐 이웃 객체가 하는 역할 측면에서 해당 객체가 무엇을 원하는지 기술하고,
호출된 객체가 그러한 바를 어떻게 실현할지 결정하게 해야 한다.
이것은 흔히 '묻지 말고 말하라' 스타일이나 좀 더 형식적으로 말하면 디미터의 법칙(Law of Demeter)으로 알려져 있다.
객체는 그것이 내부적으로 보유하고 있거나 메시지를 통해 확보한 정보만 가지고 의사 결정을 내려야 한다.
객체는 다른 객체를 탐색해 뭔가를 일어나게 해서는 안 된다.[^steve-19]

**객체는 그것이 내부적으로 보유하고 있거나 메시지를 통해 확보한 정보만 가지고 의사 결정을 내려야 한다.**

### From: 실용주의 프로그래머

> 디미터 법칙은 객체의 모든 메서드는 다음에 해당하는 메서드만을 호출해야 한다고 말한다.
- 자신
- 메서드로 넘어온 인자
- 자신이 생성한 객체
- 직접 포함하고 있는 객체

> 디미터 법칙을 따르면 함수를 호출하는 클래스의 응답집합 크기를 줄일 수 있기 때문에 좀 더 에러가 적은 클래스들을 만들 수 있다.[^andrew-231]

즉, 객체의 협력 경로를 제한하라는 의미이다.

### From: 오브젝트

- 협력 경로를 제한하라.

> 이처럼 협력하는 객체의 내부 구조에 대한 결합으로 인해 발생하는 설계 문제를 해결하기 위해 제안된 원칙이 바로 디미터 법칙(Law of Demeter)이다.
디미터 법칙을 간단하게 요약하면 객체의 내부 구조에 강하게 결합되지 않도록 협력 경로를 제한하라는 것이다.[^cho-183]

- 객체의 내부 구조를 묻지 말고, 무언가를 시켜라.

> 이처럼 협력하는 객체의 내부 구조에 대한 결합으로 인해 발생하는 설계 문제를 해결하기 위해 제안된 원칙이 바로 디미터 법칙(Law of Demeter)이다.
> 디미터 법칙은 객체가 자기 자신을 책임지는 자율적인 존재여야 한다는 사실을 강조한다.
정보를 처리하는 데 필요한 책임을 정보를 알고 있는 객체에게 할당하기 때문에 응집도가 높은 객체가 만들어진다.
<br/><br/>
하지만 무비판적으로 디미터 법칙을 수용하면 퍼블릭 인터페이스 관점에서 객체의 응집도가 낮아질 수도 있다.
자세한 내용은 이번 장에 포함된 "원칙의 함정" 절을 읽어보기 바란다.
<br/><br/>
디미터 법칙은 객체의 내부 구조를 묻는 메시지가 아니라 수신자에게 무언가를 시키는 메시지가 더 좋은 메시지라고 속삭인다.
[^cho-186]

## 디미터 법칙을 잘 지킨 코드

디미터 법칙을 따르는 형태의 코드는 다음과 같이 매우 단순하다.

```java
object.method(parameter);
```

## 디미터 법칙을 위반한 코드 - 기차 놀이 코드

> 이 법칙을 따르지 않으면 메시지 체인(Message Chains)이란 악취가 나게 된다.[^andrew-227]

디미터 법칙을 위반한 전형적인 코드의 형태는 다음과 같다.

```java
object.getChild().getContent().getItem().getTitle();
```

`getter`가 줄줄이 이어진 모습이 기차와 닮아서 **열차 전복, 기차 충돌(train wreck)** 이라는 단어로 표현하기도 한다.

(아직 어느 책에서도 보지 못한 표현이긴 하지만 나는 **기차 놀이 코드**라고 부른다.)

> 이 스타일을 따르지 않으면 '열차 전복' 코드라고도 알려진 코드가 만들어진다.
바로 접근자(getter) 메서드가 기차 객차처럼 연이어 이어진 경우다.
다음은 인터넷에서 발견한 '열차 전복' 코드의 한 예다.
```java
((EditSaveCustomizer) master.getModelisable()
  .getDockablePanel()
    .getCustomizer())
      .getSaveItem().setEnabled(Boolean.FALSE.booleanValue());
```
잠깐 생각해보니 원래 코드가 다음과 같은 코드를 의미한다는 사실을 깨달았다.
```java
master.allowSavingOfCustomisations();
```
이렇게 하면 모든 구현 세부 사항이 메서드 호출 한 번으로 줄어든다.[^steve-19]


## 참고문헌

- 실용주의 프로그래머 / 앤드류 헌트,데이비드 토머스 공저 / 김창준,정지호 공역 / 인사이트(insight) / 초판 1쇄 2005년 08월 15일
- 오브젝트 / 조영호 저 / 위키북스 / 2쇄 2019년 07월 17일
- 테스트 주도 개발로 배우는 객체 지향 설계와 실천 / 스티브 프리먼, 냇 프라이스 공저 / 인사이트(insight) / 초판 3쇄 2016년 07월 05일 / 원제 : Growing object-oriented software, guided by tests

## 주석

[^andrew-227]: 실용주의 프로그래머. 5장. 227쪽.
[^andrew-231]: 실용주의 프로그래머. 5장. 231쪽.

[^cho-183]: 오브젝트. 6장. 183쪽.
[^cho-184]: 오브젝트. 6장. 184쪽.
[^cho-185]: 오브젝트. 6장. 185쪽.
[^cho-186]: 오브젝트. 6장. 184쪽.

[^steve-19]: 테스트 주도 개발로 배우는 객체 지향 설계와 실천. 2장. 19쪽.
