---
layout: post
title: 코루틴(Coroutine) 에 대하여
description: Coroutine 공부
date:   2020-04-30
categories: android, coroutine
---
### [코루틴]

**코루틴의 개념과 쓰레드와의 차이점**

아래주소에 설명이 굉장히 잘되어있다.

참조: https://aaronryu.github.io/2019/05/27/coroutine-and-thread/



더 나아가서 알아보자면

즉, 코루틴이 가능하려면 (suspend함수는 어떻게 작동하는 것일까?)

- 코루틴에 진입할 때와 코루틴에서 나갈 때 코루틴이 실행 중이던 상태를 저장하고 복구하는 등의 작업을 할 수 있어야 한다
- 현재 실행 중이던 위치를 저장하고 다시 코루틴이 재개될 때 해당 위치부터 실행을 재개할 수 있어야 한다
- **다음에 어떤 코루틴을 실행할지 결정한다**



이 세 가지 중 **마지막 동작은** 코루틴 컨텍스트에 있는 **디스패처**에 의해 수행된다.

suspend함수를 컴파일하는 컴파일러는 앞의 두 가지 작업을 할 수 있는 코드를 생성해 내야 한다.

이때 코틀린은 컨티뉴에이션 패싱스타일(**CPS**, continuation passing style)변환과 상태기계(**state machine**)을 활용해 코드를 생성해낸다.



**CPS변환은** 프로그램의 실행 중 특정 시점 이후에 진행해야 하는 내용을 별도의 함수로 뽑고(이런 함수를 컨티뉴에이션(**Continuation**)이라 부른다), 그 함수에게 현재 시점까지 실행한 결과를 넘겨서 처리하게 만드는 소스코드 변환 기술이다.



CPS를 사용하는 경우 프로그램이 다음에 해야 할 일이 항상 컨티뉴에이션이라는 함수 형태로 전달되므로, 나중에 할 일을 명확히 알 수 있고, 그 컨티뉴에이션에 넘겨야 할 값이 무엇인지도 명확하게 알 수 있기 때문에 프로그램이 실행 중이던 특정 시점의 맥락을 잘 저장했다가 필요할 때 다시 재개할 수 있다.



CPS를 사용하면 코루틴을 만들기 위해 필수적인 일시 중단 함수를 만드는 문제가 쉽게 해결될 수 있다.

다만 모든 코드를 전부 CPS로만 변환하면 지나치게 많은 중간 함수들이 생길 수 있으므로, 상태 기계를 적당히 사용해서 코루틴이 제어를 다른 함수에 넘겨야 하는 시점에서만 컨티뉴에이션이 생기도록 만들 수 있다.



**코루틴빌더란?**

주어진 suspending function을 실행하는 새로운 Coroutine을 작성하는 간단한 함수



**디스패처란?**

**![img](https://lh4.googleusercontent.com/mNq9koxHr5_YWnJIpR8CgkxoEugyKetArONFCs8QCL-tMlMZKo8gbxP29kEpvByAi3ledrrwxY_DKDkBCP6viAvjHurdKVbNuHWBpBvlRFvbgZ1_tkCyedLCWkgqVsXSJ3H7DLYu)**

사용자가 프로그램을 실행하면 프로세스가 생성되고 Ready상태가 된다.(Ready Queue) 그 후 스케줄러가 Ready Queue에 있는 프로세스 중 하나를 프로세서(CPU)가 사용가능한 상태가 될 때 CPU를 할당해준다. 

이를 준비상태(Ready)에서 실행상태(Running)로 상태전이(State Transition)된다고 한다. 이 과정을 디스패칭(dispatching)이라고 하고 디스패처(dispatcher)가 이 일을 수행한다.

출처: https://www.crocus.co.kr/1406 [Crocus]



**스레드풀 이란?**

참조: https://limkydev.tistory.com/55



**코루틴 사용법**

아래주소에 설명이 굉장히 잘되어있다.

[https://medium.com/@limgyumin/%EC%BD%94%ED%8B%80%EB%A6%B0-%EC%BD%94%EB%A3%A8%ED%8B%B4%EC%9D%98-%EA%B8%B0%EC%B4%88-cac60d4d621b](https://medium.com/@limgyumin/코틀린-코루틴의-기초-cac60d4d621b)