---
title: "[Data Structures] Binary Trees"
excerpt: "Data Structures in Swift - Binary Tree"
  
toc: true
toc_sticky: true

categories:
  - Data-Structures
tags:
  - Data-Structures
  - BinaryTree

---

## Binary Tree

### 용어 설명
바이너리 트리는 기존 트리에서도 모든 노드의 최대 자식이 둘인 트리를 말한다.

### Binary Tree 구현해보기

자식이 둘(left, right)인 BinaryNode를 생성하면 된다.

```swift
class BinaryNode<T> {
    var value: T
    var leftChild: BinaryNode?
    var rightChild: BinaryNode?
    
    init(value: T) {
        self.value = value
    }
}
```

### Traversal Algorithms

```swift
var tree: BinaryNode<Int> = {
    let zero = BinaryNode(value: 0)
    let one = BinaryNode(value: 1)
    let five = BinaryNode(value: 5)
    let seven = BinaryNode(value: 7)
    let eight = BinaryNode(value: 8)
    let nine = BinaryNode(value: 9)
    
    seven.leftChild = one
    one.leftChild = zero
    one.rightChild = five
    seven.rightChild = nine
    nine.leftChild = eight
    
    return seven
}()
```
로 생성한 바이너리 트리를 in-order, pre-order, post-order로 돌아본다.

![image](https://user-images.githubusercontent.com/22000470/182299417-e0dbf9e0-8050-48d4-984d-d2b2127e5f81.png)

#### In-order traversal
- 만약 현재 노드가 left child를 가진다면 recursive 하게 그 child를 방문.
- 그 다음에 원래의 현재노드 방문
- 만약 현재 노드가 right child를 가진다면 recursive 하게 그 child를 방문

BinaryNode에 아래 코드를 넣는다.

```swift
func traverseInOrder(visit: (T) -> Void) {
        leftChild?.traverseInOrder(visit: visit)
        visit(value)
        rightChild?.traverseInOrder(visit: visit)
    }
```
왼쪽 자식부터 재귀로 돌고 그 다음 현재 노드를 방문, 마지막에 오른쪽 자식들을 모두 방문하는 코드이다.

![image](https://user-images.githubusercontent.com/22000470/182299697-4e1dcc88-aaf8-46b3-9270-bf196d5ece96.png)

그림으로 보면 이렇다.
![image](https://user-images.githubusercontent.com/22000470/182299816-01556669-e805-4ded-910f-54e168880c95.png)

#### Pre-order traversal
- 현재 노드를 먼저 방문한다. 그 이후에 차례로 왼쪽과 오른쪽 자식을 방문한다.

```swift
func traversePreOrder(visit: (T) -> Void) {
        visit(value)
        leftChild?.traverseInOrder(visit: visit)
        rightChild?.traverseInOrder(visit: visit)
    }
```

재귀호출의 순서만 바뀌었는데 결과가 아래처럼 나온다

![image](https://user-images.githubusercontent.com/22000470/182300064-4ee8154f-39c6-4cd3-8758-2e4910d2ddfb.png)

아까와 순서가 다르다.

![image](https://user-images.githubusercontent.com/22000470/182300136-b877ec8b-bbc1-4c14-8b5e-19a33339cfcc.png)

#### Post-order traversal
- 왼쪽, 오른쪽 자식을 방문한 뒤 현재 노드를 마지막에 방문한다. 코드로 보면 visit(value)가 가장 마지막으로 내려간다.

```swift
func traversePostOrder(visit: (T) -> Void) {
        leftChild?.traverseInOrder(visit: visit)
        rightChild?.traverseInOrder(visit: visit)
        visit(value)
    }
```

![image](https://user-images.githubusercontent.com/22000470/182300346-d81d3197-d433-4c7b-9088-0b06c131a88d.png)

![image](https://user-images.githubusercontent.com/22000470/182300379-3cc71898-3b72-4642-a3cb-ad67dd3f1828.png)

루트 노드가 가장 마지막에 찍힌다.

