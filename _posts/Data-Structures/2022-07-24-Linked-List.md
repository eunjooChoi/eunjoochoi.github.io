---
title: "[Data Structures] LinkedList"
excerpt: "Data Structures in Swift - LinkedList 01"
  
toc: true
toc_sticky: true

categories:
  - Data-Structures
tags:
  - [Data-Structures, LinkedList]
  
permalink: /data-structures/Linked-List

---
요즘 알고리즘 테스트를 준비하다 보면 간혹가다 자료구조에 대한 지식이 있어야 풀 수 있는 문제들이 나올 때가 있다.

물론 학부생때 자료구조를 공부했었지만 그 때는 C언어로 구현하는 게 주된 과제였고 지금은 Swift를 더 많이 사용하기 때문에 Swift로 자료구조를 정리 할 필요가 있을 것 같아서 시작한 자료구조 공부..

오늘은 LinkedList 부터 공부해보기로 한다.

## LinkedList

LinkedList는 값들이 한 방향으로 연결된 collection이다. 모양으로 보면 아래와 같다.

<img src="https://user-images.githubusercontent.com/22000470/180636839-666d5af7-1d30-415f-ae3e-6aa9a6b1e291.png" width="400">

요 이미지에서 보듯 동그란 칸 안에 숫자가 있고 화살표로 연결이 되어있는데, 이것을 **Node** 들이 연결 돼있다고 말한다.

#### Node

linked list에서 사용하는 Node는 두가지를 담당한다.
- 값 저장
- 다음 노드에 대한 reference 저장. (list의 마지막 node는 다음 노드가 없으므로 nil이 저장된다.)

이 노드를 코드로 구현해보자. 우선 노드가 할 일인 위의 두 가지를 포함해야한다.

```swift
class Node<T> {
    var value: T
    var next: Node?
    
    init(value: T, next: Node? = nil) {
        self.value = value
        self.next = next
    }
}
```

Node 클래스는 어떤 값도 넣을 수 있도록 제네릭으로 선언했다.

처음 클래스 인스턴스를 만들 때 node의 값과 다음 node를 저장할 수 있도록 했다.

그럼 요걸 가지고 Node를 만들어 연결을 해보면

<img src="https://user-images.githubusercontent.com/22000470/180637391-6de4e84f-8efa-4fa5-bb53-94cd5cb9b612.png" width="300">

print로 node1을 찍어봤을 때 잘 출력되는 것을 확인할 수 있다. (출력 메서드는 깃헙에서 확인 가능하다.)

#### LinkedList

이번엔 노드를 가지고 진짜 링크드리스트를 만들어보자. (노드만 가지고는 하나하나 노드를 선언하고 그것들을 순서대로 next에 할당해줘야하는 귀찮음이 있으므로 링크드리스트에서는 삽입과 제거를 담당하는 메서드도 구현해야 한다.)

링크드리스트에서 필요한 것을 생각해보면 아래 정도가 생각이 난다. 차근차근 정리를 해보자.
1. head (링크드리스트의 첫 노드)
2. tail (링크드리스트의 마지막 노드)
3. 삽입
4. 제거

```swift
struct LinkedList<T> {
    var head: Node<T>?
    var tail: Node<T>?
    
    init() {}
    
    var isEmpty: Bool { return head == nil }
}
```

우선은 head와 tail 노드를 저장할 수 있는 LinkList 구조체를 만들었다.
다음 포스팅에서 삽입, 그 다음 포스팅에서 제거를 정리한다.
