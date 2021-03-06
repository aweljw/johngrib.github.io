---
layout  : wiki
title   : Java의 예외 처리
summary :
date    : 2020-05-31 21:45:47 +0900
updated : 2020-05-31 23:06:47 +0900
tag     : java
toc     : true
public  : true
parent  : [[Java]]
latex   : false
---
* TOC
{:toc}

## 예외의 종류

Java Language Specification 11의 11.1.1. 절에 잘 나와 있다.

[11.1.1. The Kinds of Exceptions]( https://docs.oracle.com/javase/specs/jls/se11/html/jls-11.html#jls-11.1.1 )

> The classes Exception and Error are direct subclasses of Throwable:

- `Exception` 과 `Error` 클래스는 `Throwable`의 직접적인(direct) 서브 클래스이다.

### Exception: 복구가 가능한 예외

> Exception is the superclass of all the exceptions from which ordinary programs may wish to recover.
>
> The class RuntimeException is a direct subclass of Exception. RuntimeException is the superclass of all the exceptions which may be thrown for many reasons during expression evaluation, but from which recovery may still be possible.
>
> RuntimeException and all its subclasses are, collectively, the run-time exception classes.

`Exception` 클래스는 일반적인 프로그램에서 복구하려 하는 모든 예외의 슈퍼클래스이다.

`RuntimeException`은

- `Exception`의 direct 서브클래스이다.
- 모든 예외의 슈퍼클래스이다.
- 표현식 평가 중에 다양한 이유로 발생할 수 있으며, 복구가 가능하다.

`RuntimeException`과 그것의 모든 서브클래스를 런타임 예외 클래스 집합이라 한다.

### Error: 복구가 불가능한 예외

[java.lang.Error]( https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Error.html )

> Error is the superclass of all the exceptions from which ordinary programs are not ordinarily expected to recover.
>
> Error and all its subclasses are, collectively, the error classes.

`Error`는 일반적인 프로그램에서 복구를 기대할 수 없는 예외들의 슈퍼클래스다.

`Error`와 그것의 모든 서브클래스를 에러 클래스 집합이라 한다.

`java.lang` 패키지에 있는 서브클래스들을 살펴보면 다음과 같다.

- [java.lang.AssertionError]( https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/AssertionError.html )
- [java.lang.LinkageError]( https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/LinkageError.html )
- [java.lang.ThreadDeath]( https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/ThreadDeath.html )
- [java.lang.VirtualMachineError]( https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/VirtualMachineError.html )

### checked 와 unchecked exception

> The unchecked exception classes are the run-time exception classes and the error classes.
>
> The checked exception classes are all exception classes other than the unchecked exception classes. That is, the checked exception classes are Throwable and all its subclasses other than RuntimeException and its subclasses and Error and its subclasses.


- unchecked exception은 run-time exception 클래스와 error 클래스를 말한다.
    - 그 외의 다른 모든 예외 클래스는 checked exception이다.

## From: 토비의 스프링

### 예외 처리 핵심 원칙

> 예외를 처리할 때 반드시 지켜야 할 핵심 원칙은 한 가지다.
모든 예외는 적절하게 복구되든지 아니면 작업을 중단시키고 운영자 또는 개발자에게 분명하게 통보돼야 한다.
[^toby-1-281]

### 예외처리 방법

#### 예외 복구

예외 상황을 파악하고 문제를 해결해서 정상 상태로 돌려놓는 방법이다.

> 예외로 인해 기본 작업 흐름이 불가능하면 다른 작업 흐름으로 자연스럽게 유도해주는 것이다.
이런 경우 예외상황은 다시 정상으로 돌아오고 예외를 복구했다고 볼 수 있다.
단, `IOException` 에러 메시지가 사용자에게 그냥 던져지는 것은 예외 복구라고 볼 수 없다.
예외가 처리됐으면 비록 기능적으로는 사용자에게 예외상황으로 비쳐도
애플리케이션에서는 정상적으로 설계된 흐름을 따라 진행돼야 한다.
[^toby-1-285]

#### 예외처리 회피

예외처리를 자신이 처리하지 않고 호출한 쪽에 던지는 것으로, 두 가지 방법이 있다.

- `throws`를 선언하여 예외가 발생하면 알아서 던지게 하는 방법.

```java
// 방법 1
public void add() throws SQLException {
    // JDBC API
}
```

- `catch`로 일단 예외를 잡은 다음 다시 예외를 던지는 방법.

```java
// 방법 2
public void add() throws SQLException {
  try {
    // JDBC API
  } catch(SQLException e) {
    // 로그 출력
    throw e;
  }
}
```

> 예외를 회피하는 것은 예외를 복구하는 것처럼 의도가 분명해야 한다.
콜백/템플릿처럼 긴밀한 관계에 있는 다른 오브젝트에게 예외처리 책임을 분명히 지게 하거나,
자신을 사용하는 쪽에서 예외를 다루는 게 최선의 방법이라는 분명한 확신이 있어야 한다.
[^toby-1-288]

#### 예외 전환

exception translation 이라고도 한다.

발생한 예외를 상황에 맞는 적절한 예외로 전환해 던지는 방법이다.

> 예외 전환은 보통 두 가지 목적으로 사용된다.  
첫째는 내부에서 발생한 예외를 그대로 던지는 것이 그 예외상황에 대한 적절한 의미를 부여해주지 못하는 경우에,
의미를 분명하게 해줄 수 있는 예외로 바꿔주기 위해서다.
API가 발생하는 기술적인 로우레벨을 상황에 적합한 의미를 가진 예외로 변경하는 것이다.
>
> (중략)
>
> 보통 전환하는 예외에 원래 발생한 예외를 담아서 중첩 예외(nested exception)로 만드는 것이 좋다.
중첩 예외는 `getCause()` 메소드를 이용해서 처음 발생한 예외가 무엇인지 확인할 수 있다.
>
> (중략)
>
> 두 번째 전환 방법은 예외를 처리하기 쉽고 단순하게 만들기 위해 포장(wrap)하는 것이다.
중첩 예외를 이용해 새로운 예외를 만들고 원인(cause)이 되는 예외를 내부에 담아서 던지는 방식은 같다.
하지만 의미를 명확하게 하려고 다른 예외로 전환하는 것이 아니다.
주로 예외처리를 강제하는 **체크 예외를 언체크 예외인 런타임 예외로 바꾸는 경우에 사용**한다.
>
> (중략)
>
> 어차피 복구가 불가능한 예외라면 가능한 한 빨리 런타임 예외로 포장해 던지게 해서 다른 계층의 메소드를 작성할 때 불필요한 `throws` 선언이 들어가지 않도록 해줘야 한다.
[^toby-1-288]

## 참고문헌

- [Java Language Specification 11]( https://docs.oracle.com/javase/specs/jls/se11/html/jls-11.html )
- 토비의 스프링 3.1 vol 1 / 이일민 저 / 에이콘출판사 / 초판 4쇄 2013년 06월 10일

## 주석

[^toby-1-281]: 토비의 스프링 3.1 vol 1. 4.1.1장. 281쪽.
[^toby-1-285]: 토비의 스프링 3.1 vol 1. 4.1.3장. 285쪽.
[^toby-1-288]: 토비의 스프링 3.1 vol 1. 4.1.3장. 288쪽.

