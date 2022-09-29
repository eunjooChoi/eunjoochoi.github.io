---
title: "[Swift] HTML로 내려오는 문자열을 정상 표기로 변환하기"
excerpt: 
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, Swift]

permalink: /ios-problems/html-to-string

---

### HTML To String

검색 API를 사용해서 결과값을 받으면 description이나 title에 `<b> <\b>` 같은 태그나 특수문자 `&lt` 같은 게 그대로 내려와서 UI에 노출되지 않도록 변환하는 로직이 필요했다.

```swift
extension String {
    init?(htmlEncodedString: String) {
        guard let data = htmlEncodedString.data(using: .utf8) else { return nil }
        
        let options: [NSAttributedString.DocumentReadingOptionKey: Any] = [
            .documentType: NSAttributedString.DocumentType.html,
            .characterEncoding: String.Encoding.utf8.rawValue
        ]
        
        guard let attributedString = try? NSAttributedString(data: data, options: options, documentAttributes: nil) else { return nil }
        
        self.init(attributedString.string)
    }
}
```

html을 원하는 string으로 변경할 수 있도록 String에 extension을 추가하는 방법을 사용했다.

failable init으로 생성자를 하나 만들어서 실패시에는 nil을 리턴하도록 하는 코드.

원하는 string을 `String(htmlEncodedString: 원하는 스트링)` 으로 사용할 수 있다.

하지만 요 NSAttributedString을 생성하는 코드는 메인 스레드에서 실행이 되어야 해서 SwiftUI에 사용하려면 자꾸만 크래시가 났다...

결국 main thread에서 사용하기 위해 어디서 파싱하는 게 가장 좋을까 고민하다가 URLSession으로 값을 받아서 저장할 때 사용하는 게 가장 낫겠단 결론이 났다.

고 부분은 다음 포스팅으로 😎
