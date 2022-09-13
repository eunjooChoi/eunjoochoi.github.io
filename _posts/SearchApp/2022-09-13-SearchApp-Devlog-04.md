---
title: "[SearchApp Devlog 04] URLSession 코드 네트워킹 없이 테스트하기"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog04

---

## Devlog 04

검색 API를 조회할 수 있게 되었고, decode를 통해 blog, news, image 정보를 담을 수 있게 되었는데, 요 로직을 테스트 해보고 싶어졌다.

URLSession을 통해 조회를 하기는 하지만 어쨌든 테스트 코드는 네트워킹 동작이 없는 상태에서 해야할 것 같아서 URLSession을 Fake로 만들어 사용하기로 했다.

> [참고 링크: 우아한 형제들 기술 블로그](https://techblog.woowahan.com/2704/)

> [수정사항 반영 커밋](https://github.com/eunjooChoi/SearchApp/commit/20dec7cb6a484631d6a020b94f8aa105fa134b49)

### URLSession을 session 프로퍼티로 선언

기존 SearchAPI에서는 requestSearchResult 메서드 내에서 `URLSession.shared.dataTask(...)`의 형태로 호출하고 있다.

하지만 나는 네트워킹이 동작하지 않는 상황에서의 테스트 코드를 짜려고 하고 있으므로 메서드 내에서 URLSession을 직접 사용하는 것을 변경해 줄 필요가 있다. (SearchAPI 클래스를 테스트 할 때 URLSession을 가짜 URLSession으로 바꿔줄 것이기 때문.)

그래서 URLSession을 class property로 빼주고 테스트 코드에서 SearchAPI를 생성할 때 가짜 URLSession으로 넣어줄 수 있도록 session을 생성자에서 초기화 하도록 수정했다.

```swift
    let searchType: SearchType
    let session: URLSessionProtocol
    
    init(searchType: SearchType, session: URLSession = URLSession.shared) {
        self.searchType = searchType
        self.session = session
    }
```

### URLSessionProtocol 생성

지금 검색 API를 호출하기 위해 사용하는 메서드는 아래와 같다.

```swift
session.dataTask(with: urlRequest) {data, response, error in ... }
```

dataTask 메서드는 URLSession 메서드 내에 정의가 되어있다. 하지만 우리는 요 dataTask 메서드도 내 입맛대로 고쳐써야하는 것..!

이유는 URLSession 내에 정의된 dataTask를 사용하면 네트워킹 없이 테스트 하겠다는 나의 다짐이 물거품이 되기 때문이다.

내가 만든 가짜 urlsession을 진짜로 알고 쓸 수 있도록 해줘야 테스트 코드에서 목 데이터를 넣어줄 수가 있다.

그렇게 하려면 URLSession의 dataTask를 조금 속여봐야 한다.

우선은 URLSession을 속일 Protocol(구현해야 할 함수가 dataTask인)을 하나 선언한다. 그리고 그 프로토콜을 URLSession이 채택하도록 한다.

```swift
protocol URLSessionProtocol {
    func dataTask(with request: URLRequest, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
}

extension URLSession: URLSessionProtocol {}
}
```
요렇게 하면 이미 URLSession에는 URLSessionProtocol에 정의된 메서드와 동일한 메서드가 구현되어 있기 때문에 extension에서 별도의 구현을 하지 않아도 기존의 메서드로 동작한다. (그럼 나는 나중에 가짜 URLSession을 만들 때 이 프로토콜을 채택해 구현하면 되겠지!)

그리고 테스트 코드에서 가짜 URLSession을 만들 때 URLSessionProtocol을 채택한 클래스로 만들어줄 것이기 때문에 1단계에서 URLSession으로 선언해두었던 session 프로퍼티의 타입을 URLSessionProtocol로 변경한다.

```swift
    let searchType: SearchType
    let session: URLSessionProtocol
    
    init(searchType: SearchType, session: URLSessionProtocol = URLSession.shared) {
        self.searchType = searchType
        self.session = session
    }
```

### 가짜 URLSessionDataTask 만들기

가짜 URLSession을 만들기 전에 먼저 확인해봐야 할 부분이 있다. 바로 SearchAPI에서 dataTask를 실행할 때 꼬리에 붙는 `resume()`이다.

resume()의 정의를 살펴보면 아래와 같다.

> Resumes the task, if it is suspended. Newly-initialized tasks begin in a suspended state, so you need to call this method to start the task.

task를 시작하는 과정에 사용되는 것 같은데(해석만 보면 그런데 맞겠지..?), 우리는 네트워킹 없이!! 하는 테스트이기 때문에 요 resume이 하는 역할도 달라질 필요가 있다.

그래서 가짜 URLSession을 만들기 전에 resume도 가짜로 바꿔칠 수 있도록 가짜 URLSessionData 클래스도 만들어주어야 한다.

```swift
class URLSessionDataTaskSpy: URLSessionDataTask {
    var resumeDidCall: () -> Void = {}

    override func resume() {
        resumeDidCall()
    }
}
```

URLSessionDataTaskSpy는 resume을 오버라이드 하고, 메서드 내부에서는 resumeDidCall을 호출하도록 했다.

resumeDidCall은 추후에 내부 동작을 맘대로 수정할 수 있도록 var로 선언한다.

### 가짜 URLSession 만들기.

이제 가짜 URLSession을 만들어보자. URLSessionSpy로 이름붙인 클래스는 아까 위에서 만들어둔 URLSessionProtocol을 채택하고 dataTask를 구현한다.

```swift
class URLSessionSpy: URLSessionProtocol {
    var shouldRequestSuccess: Bool
    var searchType: SearchType
    var sessionDataTask: URLSessionDataTaskSpy?

    init(shouldRequestSuccess: Bool = true, searchType: SearchType) {
        self.shouldRequestSuccess = shouldRequestSuccess
        self.searchType = searchType
    }

    func dataTask(with request: URLRequest, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask {

        let sucessResponse = HTTPURLResponse(url: request.url!,
                                             statusCode: 200,
                                             httpVersion: "2",
                                             headerFields: nil)
        let failureResponse = HTTPURLResponse(url: request.url!,
                                              statusCode: 402,
                                              httpVersion: "2",
                                              headerFields: nil)

        let sessionDataTask = URLSessionDataTaskSpy()

        if shouldRequestSuccess {
            let data = load("\(searchType.rawValue).json")
            
            sessionDataTask.resumeDidCall = {
                completionHandler(data, sucessResponse, nil)
            }
        } else {
            sessionDataTask.resumeDidCall = {
                completionHandler(nil, failureResponse, nil)
            }
        }
        self.sessionDataTask = sessionDataTask
        return sessionDataTask
    }
    
    private func load(_ filename: String) -> Data? {
        let data: Data
        
        guard let file = Bundle(for: type(of: self)).url(forResource: filename, withExtension: nil) else {
            fatalError("Couldn't find \(filename) in main bundle.")
        }
        
        do {
            data = try Data(contentsOf: file)
            return data
        } catch {
            fatalError("Couldn't load \(filename) from main bundle:\n\(error)")
        }
    }
}
```

- shouldResqustSuccess 값으로 조회 성공/실패 두 가지 경우 테스트 가능
- sessionDataTask의 resumeDidCall 조작해서 dataTask에서 return
- 성공일 때 테스트 시 json 파일을 가져올 수 있도록 load 메서드 추가

### 가짜 URLSession을 주입해 테스트 코드 돌려보기
<img src="https://user-images.githubusercontent.com/22000470/189937615-360fe0d2-ef33-40ff-b0df-0c3920109179.png" width="400">

간단하게 json에서 원하는 struct로 저장이 잘 되는지 테스트 해보았는데, 아주 잘 되었다.

> 추가로 수정한 사항이 있었다.. [수정PR](https://github.com/eunjooChoi/SearchApp/pull/11)

### 마무리

URLSessionDataTaskSpy()로 생성하는 게 이제 deprecated라고 떠서 나중에 워닝을 없애는 방향으로 수정하는 걸 해봐야 할 것 같다.

그걸 제외하고는 이제 UI를 마무리 지으면서 부족한 부분들을 보완해보면 얼추 끝날 것 같다. 게으르지만 않았다면 조금 더 빨리 끝낼 수 있었겠는데...
