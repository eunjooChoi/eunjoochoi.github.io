---
title: "[Algorithm] Merge Sorted Array"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, LeetCode]

permalink: /algorithm/LeetCode-Merge-Sorted-Array
---

## 문제 설명

nums1 배열과 nums2 배열을 합쳐 오름차순으로 정렬하는 문제.
그냥 정렬해 return 하는 것이 아닌 num1이 inout으로 들어와 num1을 변경해야한다.

메서드에는 nums1과 nums2 배열뿐 아니라 m과 n이라는 int 값도 들어온다. m은 nums1의 요소 숫자, n은 nums2의 요소 숫자다. 대신 nums1의 배열은 m+n만큼의 길이를 갖고 있고 m 이상인 경우에는 나머지가 0으로 채워진 상태로 들어오게 된다.

![image](https://user-images.githubusercontent.com/22000470/182294089-12eba2f6-7540-4ef4-9f97-a252391c362e.png)

추가로 아래의 input으로 문제를 더 명확히 이해할 수 있었다.
`
[-1,0,0,3,3,3,0,0,0]
6
[1,2,2]
3
`
위의 경우 m+n인 9가 nums1의 총 길이가 되고, 0이 아닌 수는 -1, 3, 3, 3, 1, 2, 2로 7개뿐이기 때문에 9에 맞춰 0을 두개 더 넣은 

**-1, 0, 0, 1, 2, 2, 3, 3, 3** 이 되어야 정답이다.

## 푼 방법

```swift
   func merge(_ nums1: inout [Int], _ m: Int, _ nums2: [Int], _ n: Int) {
        var nums1WithoutZero = nums1.filter { $0 != 0 }
        let needZero = m - nums1WithoutZero.count
        
        if needZero != 0 {
            for _ in 0...needZero - 1 {
                nums1WithoutZero.append(0)
            }
        }
        
        nums1 = (nums1WithoutZero + nums2).sorted(by: <)
    }
```

- 우선 nums1에서 0을 뺀 배열을 생성한다.
- m+n의 길이를 만족해야하므로 추가되어야 하는 0의 갯수가 몇인지 구해 다시 0을 추가한다.
- nums1WithoutZero와 nums2를 합쳐 sort한 결과를 nums1에 할당한다.

## 결과

![image](https://user-images.githubusercontent.com/22000470/182294957-ca89f449-0652-408b-baec-b642d1e9b20c.png)


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/56d1df4002ade8cafce83834a26cbd3664986c3c)


