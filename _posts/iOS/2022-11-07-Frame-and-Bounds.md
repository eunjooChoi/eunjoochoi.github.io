---
title: "[iOS] Frame and Bounds"
excerpt: 둘의 차이는?
  
toc: true
toc_sticky: true

categories:
  - iOS
tags:
  - [iOS, Swift]

permalink: /ios/frame-and-bounds

---

### frame

![image](https://user-images.githubusercontent.com/22000470/200304245-3dd9f5ad-2159-4e14-9b66-f502bcae6b7e.png)

- **superview**의 좌표계에서 뷰의 위치와 크기를 설명
- layout operation 중에 superview 내에서 표시해야 할 뷰의 크기와 포지션을 설정한다
- frame 값이 변경되면 자동으로 draw(_:) 호출 없이도 view를 다시 표시. 만약 해당 메서드를 사용하고 싶다면 contentMode 프로퍼티를 `UIView.ContentMode.redraw`로 설정

![image](https://user-images.githubusercontent.com/22000470/200315899-bc9af59e-17d3-49a7-998f-6a95ca4f5d3f.png)
- view1의 superview는 화면의 최상위 뷰
- view2의 superview는 view1
   - view1의 frame 좌표인 100을 더해서 (120.0, 120.0, 100.0, 100.0)이 나와야 할 것 같지만 superView로부터의 거리를 따지기 때문에 20으로 출력

![image](https://user-images.githubusercontent.com/22000470/200318017-00274125-9ca4-4ab1-b09b-4d541be9dba7.png)
- view를 회전시키는 경우 해당 view를 감싸는 사각형의 크기와 위치가 frame의 좌표+크기가 된다. 회전되는 경우 회전된 view1을 모두 감싸는 크기의 사각형으로 잡히기 때문에 `(85, 129, 228, 228)` 로 출력됨

### bounds

![image](https://user-images.githubusercontent.com/22000470/200319570-ff0205da-89db-4fe0-99b7-eb69eb5689fa.png)

- **own coordinate system** 에서의 view 위치와 크기를 나타낸다.

![image](https://user-images.githubusercontent.com/22000470/200321420-502bf86b-32ca-4ac1-8bae-ad6e8a12099f.png)
- frame 출력을 bounds 출력으로 변경
   - 자기 자신의 좌표계에서 위치를 나타내기 때문에 view1과 view2, 심지어 회전된 view1의 좌표도 모두 0,0
   - frame은 view를 감싸는 사각형의 크기라 size가 변화하지만 bounds는 변화하지 않음.
