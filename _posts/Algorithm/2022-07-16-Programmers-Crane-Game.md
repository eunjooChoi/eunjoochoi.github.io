---
title: "[Algorithm] 크레인 인형뽑기 게임"
excerpt: "프로그래머스 lv.01"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, Programmers]
  
permalink: /algorithm/programmers-crane-game/

---

github.io 만든 기념으로 풀어보는 [프로그래머스 알고리즘](https://school.programmers.co.kr/learn/courses/30/lessons/64061) 문제


## 문제 설명
인형 뽑기 판인 2차원 배열이 있다. 배열 속에는 숫자(라고 쓰고 인형이라고 표현하는)들이 들어있다.
0은 비어있다는 뜻, 나머지 숫자는 인형의 종류라고 생각하고 문제를 풀게 된다.

solution 메서드의 파라메터로 moves를 받게 되고 moves에 위치한 인형을 뽑아 바구니에 넣는다.
바구니의 최상위 칸에 뽑은 인형과 동일한 인형이 있다면 인형 두 개가 없어진다. (약간 뿌요뿌요 같기도..)

이미지를 확인해보자.

<img src="https://user-images.githubusercontent.com/22000470/179245755-5a8e73d2-406c-46e1-acdc-f278bc65ed18.png" width="400">

moves가 [1, 5, 3, 5]라고 주어진다면 아래와 같이 생각해볼 수 있다.
- 1번 라인에서 어피치를 뽑음
- 5번 라인에서 튜브 뽑음
- 3번 라인에서 네오 뽑음
- 5번 라인에서 네오 뽑음 - **basket에 네오가 두개가 되면서 인형 두개가 터져 없어짐**

## 푼 방법

처음에는 주어지는 2차원 배열이 moves에서 체크하는 세로칸과 다르게 가로 배열로 넣어주어서 이걸 행과 열을 바꿔서 풀까 했었다.
하지만 그렇게 풀면 귀찮으니까.. 각 행마다 나오는 1차원 배열에서 [moves-1]의 인덱스를 체크하는 걸로 해결했다.

```swift
func solution(_ board:[[Int]], _ moves:[Int]) -> Int {
        var copyBoard: [[Int]] = board
        var basket: [Int] = []
        var result = 0
        
        for move in moves {
            let index = move - 1    // 각 board의 열
            for (idx, boardRow) in copyBoard.enumerated() {
                var row = boardRow
                if row[index] != 0 {
                    if basket.last == row[index] {
                        let _ = basket.popLast()
                        result += 2
                    } else {
                        basket.append(row[index])
                    }
                    
                    row[index] = 0
                    copyBoard[idx] = row
                    
                    break
                }
            }
        }
        
        return result
    }
```

1. 2차원 배열의 각 배열을 순회하면서 [moves-1]의 인덱스를 봤을 때 처음 0이 아닌 수가 나오는 지점을 찾고
2. 그 숫자가 basket의 last와 동일하다면 터뜨린 인형의 갯수를 판정하는 result를 2 올려준다.
3. 인형이 터져 없어졌으므로 인형뽑기 판의 인형 자리와 basket의 인형을 없애준다.

basket을 stack처럼 쓰기 위해 배열로 관리했다. append로 넣으면 가장 뒤에 붙고 popLast와 last로 체크하면 쉽게 확인이 됐다.



## 결과

<img src="https://user-images.githubusercontent.com/22000470/179247106-33ced3d9-6965-49f5-a3d9-9c6783ffce41.png" width="300">


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/c9a0a6c7f4bb1b819384f86388749537a600d66a)
