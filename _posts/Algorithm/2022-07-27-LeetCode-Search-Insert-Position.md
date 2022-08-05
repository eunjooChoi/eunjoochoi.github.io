---
title: "[Algorithm] Search Insert Position"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, LeetCode]

permalink: /algorithm/LeetCode-Search-Insert-Position

---

오랜만에 풀어보는 릿코드 문제~~

## 문제 설명
주어진 배열과 target을 가지고 target이 배열의 몇 번째 인덱스에 있는지 확인하는 문제.
단 배열은 오름차순으로 정렬이 되어있고 target이 배열에 없는 경우 배열에 넣은 뒤 그 배열에서 몇 번째 인덱스에 있는지 리턴해야 한다.

![image](https://user-images.githubusercontent.com/22000470/181044615-d1e8da51-2911-4012-a9f7-0aa06af36e26.png)

easy라 그런지 금방 풀리는 문제였다.

## 푼 방법

swift에서 제공하는 메서드를 이용해서 풀었다. 우선 firstIndex를 가지고 target이 배열에 있는지 확인 후 있으면 그대로 반환, 없으면 배열에 넣은 뒤 sort()를 통해 오름차순 정렬 후 다시 한 번 firstIndex를 통해 값을 찾았다.

```swift
    func searchInsert(_ nums: [Int], _ target: Int) -> Int {
        guard let index = nums.firstIndex(of: target) else {
            var newNums = nums
            newNums.append(target)
            newNums = newNums.sorted()
            
            return newNums.firstIndex(of: target)!
        }
        
        return index
    }
```

## 결과

![image](https://user-images.githubusercontent.com/22000470/181045191-40345e4b-65af-47fe-8cef-19c45655d4f8.png)

## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/1460f14656446fbf5e9ddd941bb3bc9004114d01)

