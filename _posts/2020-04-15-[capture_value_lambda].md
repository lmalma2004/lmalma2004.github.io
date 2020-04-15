---
layout: post
title: Kotlin_람다가 포획한 변수
description: 람다가 포획한 변수
date:   2020-04-15
categories:
---
### [람다가 포획한 변수]

람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언도니 로컬 변수까지 람다에서 모두 사용할 수 있다. 자바와 다른 점 중 중요한 한 가지는 코틀린 람다 안에서는 파이널 변수가 아닌 변수에 접근할 수 있다는 점이다. 또한 람다 안에서 바깥의 변수를 변경해도 된다. 

```kotlin
//함수 파라미터를 람다 안에서 사용하기
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String){
    messages.forEach{
        println("$prefix $it")
    }
}
>>> val errors = listOf("403 Forbidden", "404 Not Found")
>>> printMessagesWithPrefix(errors, "Error:")
Error: 403 Forbidden
Error: 404 Not Found
```

```kotlin
fun printProblemCounts(responses: Collection<String>){
    var clientErrors = 0
    var serverErrors = 0
    responses.forEach{
        if(it.startsWith("4")){
            clientErrors++
        } else if(it.startsWith("5")){
            serverErrors++
        }
    }
    println("$clientErrors client errors, $serverErrors server errors")
}
>>> val responses = listOf("200 OK", "418 I'm a teapot", "500 Internal Server Error")
>>> printProblemCounts(responses)
1 client errors, 1 server errors
```



코틀린에서는 자바와 달리 람다에서 람다 밖 함수에 있는 파이널이 아닌 변수에 접근할 수 있고, 그 변수를 변경할 수도 있다. 이 예제의 prefix, clientErrors, serverErrors와 같이 람다 안에서 사용하는 외부 변수를 '람다가 포획한 변수' 라고 부른다.



기본적으로 함수 안에 정의된 로컬 변수의 생명주기는 함수가 반환되면 끝난다. 하지만 어떤 함수가 자신의 로컬 변수를 포획한 람다를 반환하거나 다른 변수에 저장한다면 로컬 변수의 생명주기와 함수의 생명주기가 달라질 수 있다. 포획한 변수가 있는 람다를 저장해서 함수가 끝난 뒤에 실행해도 람다의 본문 코드는 여전히 포획한 변수를 읽거나 쓸 수 있다.



어떻게 그런 동작이 가능할까?

파이널 변수를 포획한 경우에는 람다코드를 변수 값과 함께 저장한다. 파이널이 아닌 변수를 포획한 경우에는 변수를 특별한 래퍼로 감싸서 나중에 변경하거나 읽을 수 있게 한 다음, 래퍼에 대한 참조를 람다 코드와 함께 저장한다.