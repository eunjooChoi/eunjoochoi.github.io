---
title: "Binding 값을 preview에 넣는 방법"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, SwiftUI]

permalink: /ios-problems/Binding-property-preview

---

State와 Binding에 대해 정리하다가 당황했던 부분이 있다. 바로 Binding 프로퍼티를 preview에서 파라메터로 어떻게 넘겨주는가였는데, Bool 값인 Binding 프로퍼티여서 그냥 true나 false를 넣어주면 될 줄 알았다. 그런데 그런건 안받는다고 뱉어버려서 아주 당황했다.

값을 넣어주는 방법은 아주 간단했다. **.constant(true)** 와 같은 식으로 .constant를 사용하면 되는거였다.

<img src="Sep-04-2022 01-23-38](https://user-images.githubusercontent.com/22000470/188280154-535174a8-2f57-4a97-8a43-24df3b438859.png" width="400">

혹시 다른 정보도 있을까 해서 docs를 열어보니 딱히 별건 없었다. binding을 생성하는데 immutable value로 만드는 것.. 심지어 Discussion에 usfule when using a PreviewProvider라고 명시도 해주었다.

![image](https://user-images.githubusercontent.com/22000470/188280210-d039bfce-d6db-4474-9882-7d16ed7c9491.png)

간단해서 좋군..
