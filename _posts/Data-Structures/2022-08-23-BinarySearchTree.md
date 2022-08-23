---
title: "[Data Structures] Binary Search Trees"
excerpt: "Data Structures in Swift - Binary Search Tree"
  
toc: true
toc_sticky: true

categories:
  - Data-Structures
tags:
  - [Data-Structures, BinarySearchTree]
  
permalink: /data-structures/BinarySearchTree

---

## Binary Search Tree

### 용어 설명
정렬된 이진트리. 빠른 조회, 삽입 및 제거 작업을 용이하게 하는 데이터 구조.

<img src="https://user-images.githubusercontent.com/22000470/186112223-aa966b97-0c5b-4b7b-a805-14b94fa746a6.png" width="400">

** binary search tree 성립 조건**
> left child의 값은 parent보다 작아야 한다.
> right child의 값은 parent보다 커야 한다.
> subtree로 봤을 때도 위 두 조건을 만족해야 한다.

### array vs Binary Search Tree

#### Search
위에 이미지에 있는 binary search tree와 배열 [6, 8, 9, 10, 11, 12, 14] 에서 값을 찾는다고 가정해보자.

- array
우선 배열에서 값을 찾을 때는 `contains(:)` 를 사용하기 때문에 O(n)의 시간이 걸린다. (하나씩 확인해봐야 하니까)

- binary search tree
하지만 binary search tree라면?
위에 적어둔 성립 조건을 만족한다는 가정하에 트리를 탐색해서 값을 찾기 때문에 트리의 분기점에서 선택하는 순간마다 검색해야 하는 공간을 절반으로 줄일 수 있다. 그래서 O(log n)이라는 시간 복잡도가 나온다.

#### Insertion
[6, 8, 9, 10, 11, 12, 14] 에 1을 추가한다고 생각해보자. 
- array
배열의 가장 앞에 값이 추가된다면 기존 배열 요소들이 한칸씩 뒤로 밀리게 되므로 배열의 insertion 시간 복잡도는 O(n)이 된다.

- binary search tree
binary search tree는 search와 동일하게 트리의 분기점에서 한 번 선택 할때마다 검색 공간이 절반으로 줄어드므로 O(log n).

#### Removal
- array
[6, 8, 9, 10, 11, 12, 14]에서 9를 제거한다고 생각해보자. 중간 값을 지우므로 뒤에 있는 값들이 한칸씩 앞으로 당겨진다. (O(n)).

- binary search tree
Insertion, Search와 동일하게 O(log n).

### Binary Search Tree 구현해보기

앞에서 생성해둔 BinaryNode를 활용해서 Binary Search Tree를 구현한다.

```swift
struct BinarySearchTree<Element: Comparable> {
  private(set) var root: BinaryNode<Element>?
}
```

#### Insert

값을 추가할 때 유의해야 할 점은 **right child는 더 큰 값, left child는 더 작은 값** 이라는 부분이다. 

```swift
private func insert(from node: BinaryNode<Element>?, value: Element) -> BinaryNode<Element> {
        // terminating recursion
        guard let node = node else {
            return BinaryNode(value: value)
        }
        
        // 들어갈 값에 따라 left로 가야 하는지 right로 가야 하는지 체크
        if value < node.value {
            node.leftChild = insert(from: node.leftChild, value: value)
        } else {
            node.rightChild = insert(from: node.rightChild, value: value)
        }
        
        return node
    }
```

#### Find

```swift
func contains(_ value: Element) -> Bool {
        guard let root = root else {
            return false
        }
        
        var found = false
        root.traverseInOrder {
            if $0 == value {
                found = true
            }
        }
        
        return found
    }
```

> in-order traversal이 O(n)이므로 contains 또한 O(n)

```swift
func contains(_ value: Element) -> Bool {
        // 시작 노드는 root부터 시작
        var current = root
        
        // node가 nil이 아닌 경우 계속 반복
        while let node = current {
            // 만약 node 값이 찾는 값이라면 true
            if node.value == value {
                return true
            }
            // current보다 value가 작으면 left, 크면 right로 이동해서 찾음
            if value < node.value {
                current = node.leftChild
            } else {
                current = node.rightChild
            }
        }
        
        return false
    }
```

> 이렇게 찾으면 O(log n)

#### Remove

- Leaf Node
자식이 없는 가장 끝의 노드이므로 그냥 제거

<img src="https://user-images.githubusercontent.com/22000470/186153407-0d6c1b2b-aec4-42b6-bd8c-4b1992e99d93.png" width="400">

- Nodes with one child
지워야 하는 노드가 자식이 하나 있다면 노드를 지우고 자식 노드를 그 위의 부모(조부모...?)에 연결해준다.

<img src="https://user-images.githubusercontent.com/22000470/186154787-33dd51a5-642c-4eb0-bbdd-acebf589f768.png" width="400">

- Nodes with two children
지우고 싶은 노드가 자식이 있는 경우는 지우기가 쉽지 않다.

<img src="https://user-images.githubusercontent.com/22000470/186157516-f35d6e7f-d97b-417d-a35a-a92ef2906f04.png" width="400">

그냥 무작정 지우고 아래 자식들을 연결하고 싶어도 binary search tree이므로 노드의 children이 세개가 될 수 없기 때문이다.

그럼 어떻게 삭제를 해야할까?

**두 자식으로 노드를 제거할 때, 제거한 노드를 오른쪽 서브 트리에서 가장 작은 노드로 바꾼다. BST의 규칙에 따라, 작은 노드는 오른쪽 서브트리의 가장 왼쪽 노드이다.**

이게 무슨말이냐 하면, 위 트리에서 봤을 때 제거해야하는 노드인 25의 오른쪽 서브트리에서 가장 작은 값을 가진 노드, 27을 25 자리에 갖다 넣으라는 소리다.

<img src="https://user-images.githubusercontent.com/22000470/186159047-54d4155c-dd61-46be-a2ca-7f28ba71c8b2.png" width="400">

요렇게 하면 오른쪽 서브트리에서 가장 작은 값을 가져왔기 때문에 오른쪽 서브트리의 모든 노드가 27보다 클 것이고, 오른쪽 서브트리에서 가져왔기 때문에 당연히 왼쪽 서브트리의 모든 노드는 27보다 작은 값임을 보장할 수 있다. (BST의 생성 조건이 그러므로..!)

요걸 코드로 표현하면 아래와 같다.

```swift
private func remove(node: BinaryNode<Element>?, value: Element) -> BinaryNode<Element>? {
        guard let node = node else {
            return nil
        }
        
        // leaf 노드를 제거하는 경우
        if node.leftChild == nil && node.rightChild == nil {
          return nil
        }
        
        // left child가 없는 경우
        if node.leftChild == nil {
          return node.rightChild
        }
        
        // right child가 없는 경우
        if node.rightChild == nil {
          return node.leftChild
        }
        
        // 양쪽 다 자식이 있는 경우
        node.value = node.rightChild!.min.value
        node.rightChild = remove(node: node.rightChild, value: node.value)
        
        return node
    }
```
