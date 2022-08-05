---
title: "[Algorithm] Sqrt(x)"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, LeetCode]

permalink: /algorithm/LeetCode-Sqrt

---

## 문제 설명

파라메터로 들어온 x의 root 값을 체크하고 소수점으로 나오는 경우 소수점을 버리고 정수 리턴.
단 내장함수 사용 금지!

ex) 9는 3리턴 8은 2.8xx이므로 2리턴

## 푼 방법

```swift
func mySqrt(_ x: Int) -> Int {
        if x == 0 { return 0 }
        
        var result = 2
        var square = 1
        
        while true {
            square = result * result
            
            if square == x {
                return result
            } else if square > x {
                return result - 1
            } else {
                result += 1
            }
        }
        
        return result
    }
```

- 0인 경우는 0 리턴
- 이외의 수는 while을 돌면서 1부터 쭉 제곱해보고 제곱한 수가 x보다 같거나 커지면 그때 result를 구함

## 결과

![image](https://user-images.githubusercontent.com/22000470/181877053-fe9cac1a-e75d-4f6f-8c69-2998c9494ef6.png)


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/6378dd60803e60a7f97e6de2f79bf51421dee9be)
