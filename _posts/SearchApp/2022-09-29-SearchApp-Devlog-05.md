---
title: "[SearchApp Devlog 05] 원하는 키워드 하이라이트 처리하기"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog05

---

## Devlog 05

### 원하는 키워드 하이라이트

내가 검색한 키워드.. (b태그로 감싼거 말고 딱 내가 넣은 검색어만)를 하이라이트 처리를 하고 싶었다.

아무리 찾아봐도 SwiftUI에서는 AttributedString의 backgroundColor 값을 조정하라는데, 도대체 내가 생각한 키워드만은 어떻게 변경하는 것일까...

찾아보니 attributedString의 특정 range에 접근할 수가 있었다.

`attributedString[특정 range].backgroundColor = .yellow` 요렇게!!

그래서 그러면, 일단 내가 검색한 키워드가 문장에 있으면 거기 range를 다 뽑아내자고 생각했다.

### 문자열에서 원하는 키워드 range 뽑기

```swift
private extension AttributedString {
    func ranges(of keyword: String) -> [Range<AttributedString.Index>] {
        var string = self
        let reversedKeyword = AttributedString(keyword.reversed())
        var ranges: [Range<AttributedString.Index>] = []
        
        while let range = string.range(of: keyword) {
            ranges.append(range)
            string.replaceSubrange(range, with: reversedKeyword)
        }
        
        return ranges
    }
}
```
우선 ranges 메서드를 만들었다. 클래스에 만들지 않고 AttributedString으로 만들었다. 왜냐면 일반 String의 Rnage로는 attributedString에 접근이 되지 않아서 **[Range<AttributedString.Index>]** 의 반환값이 필요했기 때문!

코드를 살펴보면 
- AttributedString에서 지원하는 range(of:)로 검색어의 range를 찾아준다.
- 찾은 range는 결과값 반환할 ranges에 추가하고 replaceSubrange로 찾은 키워드를 변경한다.
   - 글자수가 똑같아야 range가 안변하니까 keyword를 reverse한 값을 사용했다.
   - 글자를 변경한 이유는 range(of:)가 계속 처음 range 만을 찾기 때문..! 그냥 String이었다면 특정 range 사이에서 찾을 수 있는 메서드를 지원해주는데 AttributedString에서는 지원되지 않았다. 이런건 좀 같이 지원해줘 애플..
- range(of:)로 더이상 찾을 값이 없을 때 return한다.

### 마무리
처음에 구글의 바다에서 검색을 할 때는 String으로만 지원해주는 메서드를 많이 봤는데, AttributedString 코드는 하나도 없어서... String 코드를 사용할 수 있게 변경을 해보았다. Range<AttributedString.Index> 

정리해보니 이런거는 Index 통일 좀 해주면 안되는것일까 싶긴 했다. 다음 글은 이제 웹뷰 연결하는 글이 되겠다!
