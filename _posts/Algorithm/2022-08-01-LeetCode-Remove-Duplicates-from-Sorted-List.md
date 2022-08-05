---
title: "[Algorithm] Remove Duplicates from Sorted List"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, LeetCode]
  
permalink: /algorithm/leetcode-remove-duplicates-from-sorted-list/

---

## 문제 설명

정렬된 링크드리스트의 head 노드를 받아 중복된 노드를 제거해 반환하는 문제. 문제에 나온 예시를 살펴보면 다음과 같다.

![image](https://user-images.githubusercontent.com/22000470/182067653-430e1b21-10fc-499f-ae88-1ef28fbbfa78.png)

코드에 LinkedList의 클래스를 넣어주어서 테스트가 용이했다 👍 

## 푼 방법

```swift
   func deleteDuplicates(_ head: ListNode?) -> ListNode? {
        var copyHead = head
        var resultValues: [Int] = []
        
        while copyHead != nil {
            resultValues.append(copyHead?.val ?? 0)
            copyHead = copyHead?.next
        }
        
        let arrayValues = Array(Set<Int>(resultValues)).sorted()
        
        var result: ListNode? = nil
        var resultHead: ListNode? = nil
        
        arrayValues.forEach { value in
            if result == nil {
                result = ListNode(value)
                resultHead = result
            } else {
                result?.next = ListNode(value)
                result = result?.next
            }
        }
        
        return resultHead
    }
```

- while문을 통해 head 노드부터 차례로 값을 뽑아서 resultValues에 저장한다.
- resultValues를 가지고 Set에 저장해 중복을 제거한 뒤 다시 Array로 변환한 값을 arrayValues에 저장한다.
- arrayValues를 가지고 새롭게 링크드리스트를 만들어 반환한다

그냥 반복문 한번 돌리면 끝날 것 같은 문제였는데... 심지어 다른 코드 보니까 그렇게 되어있던데...
LinkedList를 배우고 푸니까 그래도 확실히 이해도가 올라온 게 느껴진다.
forEach와 Set을 사용해 볼 좋은 기회였다고 생각한다 😄 

## 결과

![image](https://user-images.githubusercontent.com/22000470/182068029-3d6ba7a7-52f1-4bd8-b282-3da8fa85a1cb.png)


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/926c3950d0496406d851554edcd6ff91ee37ebc6)


