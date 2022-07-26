---
title: "[Data Structures] LinkedList - Removing Values"
excerpt: "Data Structures in Swift - LinkedList 03"
  
toc: true
toc_sticky: true

categories:
  - Data-Structures
tags:
  - Data-Structures
  - LinkedList

---

## Removing Values

이번엔 LinkedList에 값을 제거하는 코드를 확인!

값 제거에는 세 가지가 있다. 
- 앞에서 빼기(pop), 
- 뒤에서 빼기(removeLast), 
- 어떤 값 바로 뒤에 있는 노드를 빼기(remove(at:)).

요 포스팅에서는 링크드리스트에서 값을 제거하는 세 가지 방법을 각각 구현해보고 시간복잡도도 알아보도록 한다.

#### pop 구현해보기

pop은 어떤 값을 LinkedList의 가장 앞에서 제거한다.
push와 비슷하고 쉽다.

push를 구현해보자.

```swift
    mutating func pop() -> T? {
        defer {
            self.head = head?.next
            if self.isEmpty { self.tail = nil }
        }
        
        return self.head?.value
```

pop을 구현할 때 제거되는 노드의 value를 리턴하도록 했다. list가 비어있을 수도 있기 때문에 value는 옵셔널로 리턴된다.

pop은 head 노드를 제거하고 그 head의 next 노드가 head가 되는 일이므로 ARC는 이 메서드가 끝나면 pop된 노드를 메모리에서 제거한다. (더이상 참조하지 않으므로?)

defer에서 목록이 비어있게 되는 경우 tail을 nil로 설정해서 혹시나 남아있을 모든 참조를 제거해버린다.

![image](https://user-images.githubusercontent.com/22000470/180934725-70acdd60-e57a-40b2-b043-62fbe59956e5.png)

마지막 insert 메서드 이후 pop을 실행한 결과를 보면 `2 - 10000 - 3` 만 남게 된다.

#### removeLast 구현해보기

removeLast는 약간 코드가 길어진다. tail 노드를 빼고 그 앞에 값을 tail로 지정하면 되지 않냐?! 할 수도 있지만 우리가 구현하고 있는 LinkedList는 prev의 값을 가지고 있지 않기 때문에 tail 노드의 이전 노드를 알지 못한다.

그래서 list를 순회해서 마지막 노드와 그 앞 노드를 알아내야 한다.

```swift
    mutating func removeLast() -> T? {
        guard let head = self.head else { return nil }
        
        guard head.next != nil else { return self.pop() }
        
        var prev = self.head
        var current = self.head
        
        while let next = current?.next {
            prev = current
            current = next
        }
        
        prev?.next = nil
        self.tail = prev
        
        return current?.value
    }
```

- head가 존재하지 않는 경우는 linkedlist가 비어있다는 뜻이므로 nil을 return 한다.
- head.next가 존재하지 않는 경우 head 하나만 남았다는 뜻이므로 pop으로 head의 노드를 제거한다.
- 이후 리스트의 마지막까지 while문으로 돌면서 tail과 tail의 앞 노드를 알아낸다.
- tail의 값은 return 하고 tail의 앞 노드를 tail로 변경한다.

![image](https://user-images.githubusercontent.com/22000470/180935940-bfddc180-c15b-4d8b-bf4b-35068433b05a.png)

앞 코드에 이어서 removeLast를 실행하면 가장 마지막 value인 3이 제거되고 `2 - 10000` 이 남는다.

#### remove(after:) 구현해보기

remove(after:) 는 원하는 노드 다음의 노드를 제거하도록 구현해야 한다. 순서를 따지면 이렇다.

1. 리스트에서 특정 노드를 찾는다. (원하는 값이 없을 수도 있다.)
2. 특정 노드 다음(.next)을 제거한다.

1번의 경우는 앞에 insert(after:) 메서드를 구현할 때 사용한 node 메서드를 사용할 수 있다.

```swift
    mutating func remove(after node: Node<T>) -> T? {
        defer {
            if node.next === tail {
                tail = node
            }
            
            node.next = node.next?.next
        }
        
        return node.next?.value
    }
```

기본적으로는 node의 다음 value를 return 한다.
만약 특정 노드의 다음이 tail이라면 tail을 제거하게 되는 꼴이 되므로 tail의 값을 현재 node로 대체한다.
그 후 node의 다음은 제거되므로 node.next는 다음의 다음 노드가 되도록 변경한다.

![image](https://user-images.githubusercontent.com/22000470/180937298-2d4ca40c-fb14-4670-8b2a-c23067f4be85.png)

removeLast() 메서드 다음에 1, 2를 push로 집어넣어 `2 - 1 - 2 - 10000` 으로 만든 뒤 1번 Index의 노드 다음을 제거하도록 했다.
결과적으로 `2 - 1 - 10000` 으로 1번 index인 1 뒤의 값이 제거되었다.

#### 시간복잡도 정리

![image](https://user-images.githubusercontent.com/22000470/180937662-4b5d2058-763c-4704-9d21-d4477123e3b5.png)

pop과 remove(after:)은 원하는 위치의 노드를 한번에 제거하므로 O(1)이고 removeLast는 tail과 tail의 앞 노드를 찾아야해서 반복문을 사용하므로 O(n)의 시간복잡도를 가진다.

> 다음시간에는 Remove로....!!!
