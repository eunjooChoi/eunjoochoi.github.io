---
title: "[Algorithm] 오픈채팅방"
excerpt: "프로그래머스 lv.02"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, Programmers]

permalink: /algorithm/Programmers-Open-Chat-Room
---

두 번째 레벨2 문제. 그래도 이건 생각보다는 쉬웠다.
물론 쉽다고 해서 코드가 깔끔한 건 아닌가보다..ㅎㅎ

## 문제 설명
카카오 오픈 채팅방 관리자가 유저들의 출입 기록을 확인할 때 어떤 식으로 표현되는 지 확인해보는 문제.
~가 들어왔습니다 / ~가 나갔습니다 문구가 출력되는데 유저가 닉네임을 변경하면 기존에 남아있던 출입 메시지의 닉네임도 싹 바꿔서 보여주어야 한다.

![image](https://user-images.githubusercontent.com/22000470/179455960-609e186e-564d-43a3-997c-99365a9759cc.png)

입출력 예를 보면 알겠지만 
- record에는 유저들의 출입 기록과 userid, 닉네임이 들어있다. 
- 닉네임은 동일할 수 있기 때문에 유일한 값인 uid로 유저를 구분한다.
- Enter과 Leave, Change가 있다.

user1234인 사람이 Muzi로 들어왔다 나간 이후 Prodo란 이름으로 재접속 했기 때문에 record의 모든 닉네임을 Muzi에서 Prodo로 바꿔 표기해야한다.
uid4567인 사람이 닉네임을 Prodo에서 Ryan으로 바꾸었기 때문에 record의 모든 닉네임을 Prodo에서 Ryan으로 바꿔 표기해야한다. 

## 푼 방법

uid가 유저를 구분하는 값이기 때문에 uid와 닉네임을 쌍으로 저장하는 userNickName 딕셔너리를 만들었다.
닉네임을 uid와 짝지어 놓고 enter나 change가 있는 경우 uid에 매칭되는 닉네임을 바꿔준 뒤, 
미리 파싱해둔 recordArr을 확인하며 change면 무시(fliter를 사용했다.), enter면 들어왔습니다/ leave면 나갔습니다를 result에 저장하도록 했다.

코드를 커밋해두고 보니 recordArr을 만들 필요 없이 record를 돌면서 풀었어도 됐을 것 같은 느낌..

```swift
    func solution(_ record:[String]) -> [String] {
        enum Keyword: String {
            case enter = "Enter"
            case leave = "Leave"
            case change = "Change"
            
            var message: String {
                switch self {
                case .enter:
                    return "님이 들어왔습니다."
                case .leave:
                    return "님이 나갔습니다."
                default:
                    return ""
                }
            }
        }
        
        var userNickName: [String: String] = [:]
        let recordArr: [[String]] = record.map { record in
            let arr = record.components(separatedBy: " ")
            
            // userNickName 관리
            if arr[0] == Keyword.enter.rawValue || arr[0] == Keyword.change.rawValue {
                userNickName[arr[1]] = arr[2]
            }
            
            return arr
        }
        
        let result: [String] = recordArr.filter { $0[0] != Keyword.change.rawValue }
                                        .map { info in
                                            let nickName = userNickName[info[1]] ?? ""
                                            if info[0] == Keyword.enter.rawValue {
                                                return nickName + Keyword.enter.message
                                            } else {
                                                return nickName + Keyword.leave.message
                                            }
                                        }
        
        return result
    }
```

## 결과

<img src="https://user-images.githubusercontent.com/22000470/179456867-86d444c7-cae2-4845-abb8-6553d916b1ef.png" width="400">


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/38dd61baeaf66d43b47e7909ec26a1c3889c3e4b)
