---
title: "API 호출해 받은 Data를 json 파일로 저장하기"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, Swift]

permalink: /ios-problems/save-json-file

---

codable의 등장으로 api 결과값을 아주 손쉽게 사용할 수 있게 되어 행복하다.

JSONDecoder로 decode만 해주면 되니 아주 좋은 것..!

하지만 테스트 코드를 만들기 위해서는 json 데이터를 파일로 저장해야 해서 Data를 어떻게 하면 json으로 저장할 수 있는지를 찾아보았다.

```swift
        URLSession.shared.dataTask(with: urlRequest) { data, response, error in
            let url = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first?.appendingPathComponent("파일명.json")

                    do {
                        let json = try JSONSerialization.jsonObject(with: data!, options: []) as? [String : Any]
                        try data?.write(to: url!)
                    } catch {
                        // 에러 처리
                    }
        }.resume()
```

나는 네이버에서 제공하는 검색 API를 활용했고, data 값에 검색 결과가 들어있다. data를 저장하면 되는 작업.

- FileManager로 저장할 경로 + 파일명으로 url 위치를 잡아준다.
   - 나중에 url 경로를 po로 출력해 터미널에서 바로 open 할 수 있다.
- data를 Dictionary로 변경 후 아까 만들어둔 url에 write

요렇게 코드를 추가하면 url 위치에 `파일명.json`형식으로 파일이 아주 잘 저장된다.

테스트 코드를 위해 강제 언래핑을 사용했지만 실제로 사용할 때는 이렇게 사용하지 말아야지.

> 검색 API의 쿼리에 들어가는 keyword를 encoding 해서 보내면 title 같은 게 encoding 되어 내려온다. 근데 그냥 보내면 그냥 string으로 내려옴.. 왜일까? (blog 기준)
