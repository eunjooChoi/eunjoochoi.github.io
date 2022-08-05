---
title: "[Algorithm] Plus One"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, LeetCode]

permalink: /algorithm/LeetCode-Plus-One
---

## 문제 설명

int형 배열을 받아서 숫자로 변환 후 그 숫자에 + 1을 한 값을 다시 int형 배열로 반환해야 한다.
테스트 케이스를 보면 훨씬 이해가 쉽다.

![image](https://user-images.githubusercontent.com/22000470/181417924-d1824120-9921-484d-8bc3-b994e3a63646.png)

배열로 1, 2, 3이 들어왔다면 123 + 1 인 124를 1, 2, 4로 반환하면 된다.
받아올림이 있는 경우도 체크해주어야 한다.

## 푼 방법

처음에는 그냥 digits를 string으로 바꾸고 그걸 다시 Int로 바꿔서 + 1을 한 다음에 배열로 변환해서 리턴해주도록 했다.
하지만 결과는 fail... 테스트 케이스 다 잘 돌아가는데 왜 실패로 뜨나 봤더니

![image](https://user-images.githubusercontent.com/22000470/181418087-a4a16879-1b01-4330-889e-28f2b9bd24a8.png)

digits의 길이가 100자리 이내... 그럼 UInt64로도 커버가 안되는 길이가 나온다... 😢 

그래서 그냥 digits 배열을 가지고 마지막 요소부터 보면서 + 1을 해주고 받아올림이 있는지 판단해서 계속 앞쪽으로 올 수 있게 구현했다.

```swift
    func plusOne(_ digits: [Int]) -> [Int] {
        var plusOneDigits = digits
        
        guard let last = plusOneDigits.last else { return digits }
        
        if last + 1 < 10 {
            let plusOne = last + 1
            plusOneDigits[plusOneDigits.count - 1] = plusOne
            
            return plusOneDigits
        }
        
        var add = 0
        for (idx, plusOneDigit) in plusOneDigits.reversed().enumerated() {
            let originIdx = plusOneDigits.count - idx - 1
            
            if idx == 0, plusOneDigit + 1 == 10 {
                plusOneDigits[originIdx] = 0
                add = 1
                continue
            }
            
            if plusOneDigit + add < 10 {
                plusOneDigits[originIdx] = plusOneDigit + add
                add = 0
                break
            } else {
                plusOneDigits[originIdx] = 0
                add = 1
            }
        }
        
        if add == 1 {
            plusOneDigits.insert(1, at: 0)
        }
        
        return plusOneDigits
    }
```

- 1을 더했을 때 받아올림이 없다면 그대로 마지막 index의 값만 업데이트해서 return
- 배열을 거꾸로 돌면서 (더하기를 1의자리부터 하기 위해) 받아올림이 없을때까지 계속 1씩 더한다
   - 1의 자리는 add를 더하는게 아닌 1로 더해 판단하고, 
   - 10의 자리부터는 받아올림이 있다면 n의자리를 0으로 만들고 add를 1로 변경, 
   - 받아올림이 없으면 n의 자리 결과를 업데이트한 후 for문을 빠져나온다
- 마지막까지 다 돌았는데도 add가 1이 남아있다면 자리수가 하나 추가된 것이므로 0번째 인덱스에 1을 끼워넣는다.

삽질을 엄청 했던 문제.. 어려운 건 아닌데 이렇게 더럽다니 흑흑

## 결과

![image](https://user-images.githubusercontent.com/22000470/181418698-a8853c6a-0168-4b93-9a4e-f2857819bd1f.png)

## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/8ac23a15af402cbc38170a84db770738579c983b)

