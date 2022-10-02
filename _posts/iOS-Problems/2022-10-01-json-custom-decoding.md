---
title: "[Swift] JSONDecoder 내가 원하는 모양으로 파싱하기"
excerpt: 
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, Swift]

permalink: /ios-problems/swift-json-custom-decoding

---

### JSONDecoder

URLSession을 이용해 API를 조회하고 받은 Data를 JSONDecoder를 사용해 내가 원하는 struct의 모양으로 파싱을 했었다.

바로 요렇게!!

```swift
            guard let data = data else { return }
            
            let decoder = JSONDecoder()
            
            DispatchQueue.main.async {
                switch self.searchType {
                case .blog:
                    if let result = try? decoder.decode(APIResult<Blog>.self, from: data) {
                        self.blogs = result.items
                    }
                case .news:
                    if let result = try? decoder.decode(APIResult<News>.self, from: data) {
                        self.news = result.items
                    }
                case .image:
                    if let result = try? decoder.decode(APIResult<Photo>.self, from: data) {
                        self.photos = result.items
                    }
                }
            }
```

URLSession에서 넘어온 data를 JSONDecoder의 decode 메서드를 통해 원하는 모습으로 파싱한다.

요렇게 하게 되면 Blog 구조체의 기본 생성자를 통해 값이 초기화가 된다.

하..지..만... 내가 원했던 건 Blog에 들어가는 title과 description에 html 특수문자를 모두 치환한 형태를 저장하고 싶었다.

이 방식으로 하면 그냥 치환안된 날 것(?)의 문자열이 저장이 되기 때문에 열심히 또 찾아보았다.

### init(from decoder: Decoder) throws 의 존재

처음에는 struct에 init을 생성해서 그 안에서 html 치환 메서드를 호출하면 될 줄 알았는데, init이 있더라도 decode 메서드를 실행하면 그냥 기본 생성자가 호출되어버렸다.

여러 방식으로 해보다가 찾아낸 init(from decoder: Decoder) throws 메서드의 존재.

decoder를 받아 초기화 하는 init을 작성하면 Decoding 해서 만들어진 데이터를 저장하기 전에 init 메서드를 통해 원하는 문자열로 커스텀을 할 수 있었다.

```swift
    enum CodingKeys: String, CodingKey {
        case title, link, description, bloggername, bloggerlink, postdate
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        let decodedTitle = try container.decode(String.self, forKey: .title)
        self.title = String(htmlEncodedString: decodedTitle) ?? ""
        
        self.link = try container.decode(String.self, forKey: .link)
        
        let decodedDescription = try container.decode(String.self, forKey: .description)
        self.description = String(htmlEncodedString: decodedDescription) ?? ""
        
        self.bloggername = try container.decode(String.self, forKey: .bloggername)
        self.bloggerlink = try container.decode(String.self, forKey: .bloggerlink)
        self.postdate = try container.decode(String.self, forKey: .postdate)
    }
```

이런 식으로 enum CodingKeys를 생성해 새로 만든 생성자에서 사용할 수 있도록 한다.(요 때 enum은 CodingKey를 채택해야 한다.) 

그 다음 init의 파라메터로 들어온 decoder에서 container를 뽑아내고, CodingKey 값으로 각 값을 원하는 원시값으로 뽑아낼 수 있다.

나는 이 init 메서드에서 decoder로 넘어온 title과 description을 먼저 string으로 뽑아낸 뒤 이전 포스팅에서 만들어둔 htmlEncodedString을 호출해 치환해 구조체의 초기값으로 할당했다.

> 요 메서드를 struct 내부에 넣으면 기본 생성자를 사용할 수 없으므로 extension에 init 메서드를 분리해 넣었다. 그렇게 하면 내가 만든 init은 JSON 파싱할 때 사용하고 swiftUI의 프리뷰에서는 기본 생성자를 사용하게 된다.

