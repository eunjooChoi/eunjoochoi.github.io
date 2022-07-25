---
title: "[Data Structures] LinkedList - Adding Values"
excerpt: "Data Structures in Swift - LinkedList 02"
  
toc: true
toc_sticky: true

categories:
  - Data-Structures
tags:
  - Data-Structures
  - LinkedList

---

## Adding Values

이번엔 LinkedList에 값을 추가하는 코드를 확인!

값 추가에는 세 가지가 있다. 
- 앞에 넣기(push), 
- 뒤에 넣기(append), 
- 어떤 값 바로 뒤에 넣기(insert:after).

요 포스팅에서는 세 가지를 각각 구현해보고 시간복잡도도 알아보도록 한다.

#### push 구현해보기

push는 어떤 값을 LinkedList의 가장 앞에 삽입한다. 

LinkedList의 가장 앞에 넣는다는 말은 head의 값이 바뀐다는 말이기도 하므로 **head-first insertion** 이라고도 한다.

push를 구현해보자.

```swift
    mutating func push(value: T) {
        self.head = Node(value: value, next: self.head)
        if self.tail == nil {
            self.tail = self.head
        }
    }
```

LinkedList를 구조체로 구현하고 있기 때문에 가지고 있는 값을 변경하기 위해 mutating 키워드를 사용한다. 

head-first insertion이기 때문에 기존에 있던 노드를 생성해 노드의 다음 노드를 head로 설정해준다.

만약 tail이 없다면 Node가 방금 생성한 것 하나만 있으므로 head와 tail을 같은 것으로 설정한다.

![image](https://user-images.githubusercontent.com/22000470/180787662-f184314c-09cb-4a11-ac3a-7f2ef4101064.png)

그림으로 설명해보면 이런 느낌...? 근데 굳이 tail을 설정해줘야하는지 모르겠다. 어떤 노드의 next 값이 nil이면 자동으로 tail로 판단할 수도 있는 게 아닌가..

![image](https://user-images.githubusercontent.com/22000470/180788074-cc70bcd7-0ca8-44be-ac7b-5e0a141bf4af.png)

아무튼 이 새로 만든 메서드를 가지고 LinkedList에 값을 추가하고 print를 해보면 뒤에 넣은 값이 가장 앞에 가는 `3 - 2 - 1` 모양으로 출력되는 걸 확인할 수 있다.

#### append 구현해보기

append는 리스트의 가장 뒤에 붙이는 방식이다. tail이 늘어나므로 **tail-end-insertion** 이라고도 부른다.

push와 마찬가지로 구조체의 값을 바꾸기 때문에 mutating 키워드를 붙여 메서드를 구현한다.

```swift
   mutating func append(value: T) {
        guard self.isEmpty == false else {
            self.push(value: value)
            return
        }
        
        self.tail?.next = Node(value: value)
        self.tail = self.tail?.next
    }
```

push와 다르게 조금 코드가 길다. tail에 넣기 때문인데, isEmpty가 true라면 리스트가 비어있는 것이므로 push를 통해 head와 tail을 설정해준다. 그게 아니라면 이미 값이 들어있는 리스트이므로 새로운 노드를 생성해 tail의 뒤에 연결해주도록 한다.

![image](https://user-images.githubusercontent.com/22000470/180790542-d3c1e14d-1fc9-4977-b5d3-e008fa1bbb36.png)

그림으로 보면 이런 구조로 동작한다.

![image](https://user-images.githubusercontent.com/22000470/180790662-66e07acd-76f9-44ec-af62-4b55817bf4d2.png)

아까와 값을 넣는 순서는 똑같고 push에서 append로 변경한 코드를 실행하면 `1 - 2 - 3` 으로 tail에 붙어서 추가되는 걸 알 수 있다.

#### insert(after:) 구현해보기

insert(after:) 는 원하는 노드 다음에 넣을 수 있도록 구현해야 한다. 순서를 따지면 이렇다.

1. 리스트에서 특정 노드를 찾는다. (원하는 값이 없을 수도 있다.)
2. 새로운 노드를 특정 노드 다음에 집어넣는다.

그럼 먼저 node가 어디 있는지를 확인하는 코드가 필요하다.

```swift
    func node(at index: Int) -> Node<T>? {
        var currentNode = self.head
        var currentIndex = 0
        
        while currentNode != nil, currentIndex < index {
            currentNode = currentNode?.next
            currentIndex += 1
        }
        
        return currentNode
    }
```

head부터 원하는 index 전까지 리스트를 순회하며 원하는 index의 node를 찾아준다. 만약 nil인 경우는 원하는 index보다 리스트가 짧은 것이므로 nil이 return된다.

그럼 원하는 위치에 node를 넣는 insert(after:) 메서드를 구현해보자.

```swift
   mutating func insert(value: T, after node: Node<T>?) {
        guard let node = node, self.tail !== node else {
            self.append(value: value)
            return
        }
        
        node.next = Node(value: value, next: node.next)
    }
```

어떤 노드를 던져주면 우선 그 노드가 리스트의 tail 위치에 있는지 판단한다. (tail이라면 append로 추가하면 되므로)  

넘겨준 노드가 마지막 노드가 아니라면 노드의 뒤에 새로운 노드를 연결하고, 새로운 노드의 next에 기존 node의 next를 이어붙인다.

그림으로 보면 더 이해가 빠를 것 같다.

![image](https://user-images.githubusercontent.com/22000470/180794733-ccc2b01d-37b0-4334-9892-59ab6b436ccd.png)

이제 코드를 실행시켜 보면

![image](https://user-images.githubusercontent.com/22000470/180794812-3d17769e-e845-46bf-89a8-dadeb9110765.png)

기존 list에서 1번 index에 있는 node를 찾고 그 노드 다음에 10000을 넣어주도록 했다. 결과는 `1 - 2 - 10000 - 3` 으로 잘 나온다.

#### 시간복잡도 정리

![image](https://user-images.githubusercontent.com/22000470/180795438-197514a7-0f83-4499-9a9b-abb000ba534c.png)

push, append, insert 모두 원하는 위치에 한번에 넣으므로 O(1). node(at:)만 while 문을 돌아야하므로 O(i).

> 다음시간에는 Remove로....!!!
