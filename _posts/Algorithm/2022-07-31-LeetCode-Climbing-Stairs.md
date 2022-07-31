---
title: "[Algorithm] Climbing Stairs"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - Algorithm
  - LeetCode

---

## 문제 설명

파라메터로 들어온 n칸의 계단이 있다고 가정해보자. 계단은 한번에 1칸 또는 2칸까지만 오를 수 있다. 이 때 n번째 칸까지 도달하는 방법은 총 몇가지일까?

ex) 3칸이라면 `1, 1, 1`, `2-1`, `1-2` 총 세 가지의 방법이 존재한다.

## 푼 방법

```swift
func climbStairs(_ n: Int) -> Int {
        var result = [1, 1]
        
        for num in 1..<n {
            result.append(result[num] + result[num-1])
        }
        
        return result.last ?? 0
    }
```

어... 이걸 이렇게 푸는 건 아니었을 것 같은데... 2, 3, 5, 8, 13으로 피보나치 수열처럼 답이 나오길래 냅다 피보나치 항을 반환하도록 리턴했는데 어이없이 정답.... 원래 이렇게 푸는거였을까...?

## 결과

![image](https://user-images.githubusercontent.com/22000470/182013626-41a37dda-6ae6-4290-8187-3ee3e3d425d5.png)

## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/c1f5a7c0b0949d48f0cd68ae04ab86b5d17e163c)

