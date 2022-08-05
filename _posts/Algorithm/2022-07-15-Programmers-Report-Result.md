---
title: "[Algorithm] 신고결과받기"
excerpt: "프로그래머스 lv.01"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, Programmers]

permalink: /algorithm/programmers-report-result/

---

릿코드로 문제 풀다가 프로그래머스가 생각나서 생각난 김에 프로그래머스 알고리즘 문제 푸는걸로 넘어왔다. 
lv.1 첫문제였던 신고 결과 받기 문제를 풀어봤는데, 아무렇게나 풀면 제한시간 10초에 걸려 시간초과가 떠버리는 게 열받긴 했지만 어찌저찌 100점을 맞을 수 있었다.

## 문제 설명
우선 문제는 신고 횟수 k번 이상 신고 당한 사람이 있는 경우 그 사람을 신고한 사람들에게 신고 결과를 전송해주는 문제이다. 
한사람이 여러 사람을 신고할 수 있고 동일한 사람을 여러번 신고 할 수도 있지만 동일한 사람을 계속 신고하는 경우에도 신고 카운트는 1만 올라가는 것이 유의할 점.

![image](https://user-images.githubusercontent.com/22000470/179214645-89724fbd-65d6-4c1a-8fa5-f5a16cd3cd71.png)

메서드에는 유저 목록인 id_list와 신고하는사람-신고할사람이 묶여있는 report, 정지 기준이 되는 신고횟수 k가 주어진다. 
report가 문자열이지만 띄어쓰기로 나눠져 있어서 잘 파싱해서 사용하면 된다.

## 푼 방법

처음에는 초단순하게 report를 파싱해서 신고 카운트를 세고 k번 이상인 유저를 찾아낸 다음에 다시 Report를 확인하면서 이 유저를 신고한 사람이 있는지를 찾으면 되겠거니 싶었다. 
근데 for문이 중첩되어서 들어가니까 바로 시간초과가 떠버리는 참사...


그래서 결과적으로는 아래와 같이 풀게 되었다.

```swift
func solution(_ id_list:[String], _ report:[String], _ k:Int) -> [Int] {
        var reportList: [String: Set<String>] = [:]
        var countList: [String: Int] = [:]
        var idIdx: [String: Int] = [:]
        
        var result: [Int] = [Int](repeating: 0, count: id_list.count)
        
        
        for (idx, name) in id_list.enumerated() {
            countList[name] = 0
            idIdx[name] = idx
        }
        
        for list in report {
            let splitedList = list.split(separator: " ")
            let key = String(splitedList[0])
            let value = String(splitedList[1])
            
            if reportList[key] == nil {
                reportList[key] = [value]
            } else if reportList[key]?.contains(value) == false {
                reportList[key]?.insert(value)
            } else {
                continue
            }
            
            countList[value] = (countList[value] ?? 0) + 1
        }
        
        for (name, count) in countList {
            if count < k { continue }
            
            for list in reportList {
                if list.value.contains(name), let idx = idIdx[list.key] {
                        result[idx] = result[idx] + 1
                }
            }
        }
        
        return result
    }
```

유저별로 신고 당한 횟수를 저장하는 countList Dictionary와 배열로 들어온 신고 리스트를 신고자 - 신고한사람들...로 바꿔준 reportList, id_list를 통해 idIdx 배열을 만들어 id_list의 유저 순서를 기억하도록 만들었다.

그 후 report를 돌며 reportList, countList를 만들었고, 정리가 끝난 뒤에는 countList를 확인하면서 result 배열에 정답을 추가할 수 있도록 했다.
만들다보니 reportList에 중복으로 신고한 사람이 그대로 들어가는 걸 확인해서 [String: <Set>String]으로 타입을 변경해주었다. 그러면 중복된 애들은 안들어가니까...

어찌저찌 통과는 했는데 다른 분들 문제 풀이 한 걸 보니까 reduce 같은거 써서 짧고 간단하게 푸셨던데, 언제쯤 나도 그렇게 짤 수 있을지...? 일단 오늘은 풀어둔 것에 만족하자.



## 결과

<img src="https://user-images.githubusercontent.com/22000470/179214946-77aee5d4-ceb2-4295-a430-44b8c08531f0.png" width="200">


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/e699baf90c6c3afee6261026407bc1ab48169e28)
