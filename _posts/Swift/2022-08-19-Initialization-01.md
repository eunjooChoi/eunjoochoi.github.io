---
title: "Initialization 01"
excerpt: "iOS 문서 훑어보기 - Initialization 기초"
  
toc: true
toc_sticky: true

categories:
  - Swift
tags:
  - [swift-docs, swift]
  
permalink: /swift/Initialization-01

---
도대체 swift는 init이 왜 이렇게 많을까..? 종류별로 하나씩 기억하기가 어려워서 정리하는 포스팅..!

우선 문서 링크는 [여기﻿](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)

## Initialization
문서에 적힌 글을 살펴보면 Initialization은 class나 struct, enum 타입의 instance를 사용할 수 있도록 준비하는 프로세스라고 정의되어있다. Initialization에는 stored property나 새 인스턴스를 사용할 준비가 되기 전에 필요한 기타 다른 설정 또는 초기화를 수행하는 작업이 포함된다. Objective-C와 다르게 init 메서드는 return 값이 없다.


class 유형의 인스턴스는 사용 이후에 소멸 전에 메모리로부터 직접 해제해야 하는 것들이 있는 경우 deinit을 사용해 해제 후 deinit 될 수 있다. (Deinit 링크는 [여기](https://docs.swift.org/swift-book/LanguageGuide/Deinitialization.html), Deinit 정리도 initialization 정리 끝나면 해야겠다.)



## Setting Initial Values for Stored Properties
클래스나 구조체의 stored property는 **무조건** 생성될 때 함께 초기화 되어야 한다. (optional value는 안 해도 된다. nil일 수 있기 때문에)

### (1) Initializers
가장 기본적인 initializer는 어떤 파라메터도 받지 않는 init()이 있다. 

![image](https://user-images.githubusercontent.com/22000470/185540539-22561e30-2921-409b-b929-94a972be7460.png)

JohnInfo라는 class가 있다. init() 메서드 내부에서 name과 age를 초기화 할 수 있다.

### (2) Default Property Values
만약에 stored property에 직접 default value를 설정해두게 되면 property observer 호출 없이 값이 바로 세팅된다. 

> (When you assign a default value to a stored property, or set its initial value within an initializer, the value of that property is set directly, without calling any property observers.)

위 JohnInfo를 Default Property Value를 사용해서 표현하면 다음과 같이 변경할 수 있다.

![image](https://user-images.githubusercontent.com/22000470/185540646-09056312-11ce-432f-993e-e7c074e689e6.png)

optional이 아닌 stored property에 default value를 지정해주면 init 메서드를 별도로 생성해주지 않아도 된다. (init 메서드 안에서 처리해 줄 동작이 필요한 경우에만 사용하면 된다.)

## Customizing Initialization
### (1) Initialization Parameters
만약, John의 정보 뿐 아니라 다른 학생들의 정보도 필요하게 된다면 모든 학생들의 정보를 클래스 하나하나에 매칭 시키기는 부담스러울 것이다. class Student Info로 변경해서 name과 age를 각각 설정할 수 있도록 변경해본다.

![image](https://user-images.githubusercontent.com/22000470/185540710-b8e43116-1fa9-4177-86ad-f97fbee02b36.png)

다음과 같이 init에 초기화 해야하는 stored property들의 값을 parameter로 받아서 class나 struct를 초기화 할 수 있다. paramater가 들어간 init은 여러 개 만들 수 있는데, StudentInfo의 age를 초기화 할 때 bitrhYear를 받는 경우를 생각해서 아래와 같이 init 메서드를 하나 더 추가해 보았다.

![image](https://user-images.githubusercontent.com/22000470/185540742-05a335cf-a5c5-4c5e-98c7-e9cf95c1f4e1.png)

### (2) Parameter Names and Argument Labels, Initializer Parameters Without Argument Labels

초기화 메서드에서도 아래 birthYear처럼 argument label이 없는 init을 사용할 수 있다고 한다. 하지만 init 메서드는 메서드 이름이 모두 init이기 때문에 나는 보통 사용할 때 prameter의 이름을 꼭 명시해주는 편이다. 그래야만 이 초기화 함수 내부에서 어떤 값들이 초기화되어야 하는지 바로 알 수 있기 때문이다. 

![image](https://user-images.githubusercontent.com/22000470/185540776-15d93526-c368-45e4-a81b-c4252340e387.png)

현재 StudentInfo에서 사용하고 있는 초기화 함수 두 개가 모두 하나의 String과 하나의 Int를 받고 있는데, 만약 age도 argument label을 사용하지 않도록 변경하면 어떻게 될까?

![image](https://user-images.githubusercontent.com/22000470/185540796-93c24d74-715d-4ce7-8ee1-cb3acbf0066b.png)

위와 같이 redeclaration 오류가 나게 된다. 두 종류의 메서드가 받는 값이 같기 때문에 동시에 생략은 할 수 없다. 역시 초기화 함수에서는 argument label을 명시해 주는 편이 낫다고 생각한다.

### (3) Optional Property Types
인스턴스가 초기화 될 때 당장 초기화 할 필요가 없는 값들은 어떻게 처리해야 할까? 답은 optional에 있다. property를 optional로 선언하게 되면 초기화 메서드에서 초기화를 하지 않아도 된다. 이 값은 nil로 초기화되어 var로 선언된 경우 나중에 값을 할당해 줄 수 있다.



위에서 계속 예시로 사용한 StudentInfo에 학생의 주소를 저장하는 address 프로퍼티를 추가한다고 생각해보자. 주소 입력은 선택적으로 가능하게 한다면 init 메서드 내부에서 당장 초기화 하지 않아도 되어야 한다.

```swift
class StudentInfo {
    var name: String
    var age: Int
    var address: String?
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    init(name: String, birthYear: Int) {
        self.name = name
        self.age = 2021 - birthYear
    }
}

let johnInfo = StudentInfo(name: "John", age: 25)
print(johnInfo.age)
print(johnInfo.address)

let janeInfo = StudentInfo(name: "Jane", birthYear: 1998)
print(janeInfo.age)
janeInfo.address = "KOREA"
print(janeInfo.address)
```

![image](https://user-images.githubusercontent.com/22000470/185540863-91d4190b-5e45-4b7b-916e-7dc6ffe9cfcd.png)

위와 같이 address를 String?으로 처리하게 되면 init 메서드에서 초기화 하지 않아도 nil로 출력되는 것을 확인할 수 있다.

### (4) Assigning Constant Properties During Initialization
stored property에는 var 프로퍼티 뿐만 아니라 let 프로퍼티도 존재한다. let은 한 번 값이 정해지면 변경할 수 없는 변수인데, 이는 초기화에서도 동일하게 동작한다. StudentInfo의 name 프로퍼티를 let으로 변경해보자. 초기화 이후에 다시 name을 변경하려고 한다면 아래와 같은 오류가 난다. 

![image](https://user-images.githubusercontent.com/22000470/185540903-9f07196c-aaba-466a-ae79-f0ea437004c9.png)

빨간 점을 클릭해보면 다음과 같은 방향을 제시해 준다.

![image](https://user-images.githubusercontent.com/22000470/185540921-bd98c695-7500-4608-bbea-b4e879b7e671.png)

'너 이거 값 바꿀거면 name 프로퍼티를 var로 고쳐!'라고 말이다.

> 최초작성: tistory / 20210915
