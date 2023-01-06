---
title: "[iOS] 이전 화면으로 데이터를 넘겨주기"
excerpt: 반대로 데이터 넘겨주는 건 어떻게 할까?
  
toc: true
toc_sticky: true

categories:
  - iOS
tags:
  - [iOS, Swift]

permalink: /ios/how-to-send-data-parent-view-controller

---
### delegate 이용하기
자식 viewcontroller에서 delegate 정의 후 부모 viewcontroller에서 delegate 채택 후 자식 viewcontroller 생성 시 delegate 주입.

- 부모 VC

```swift
func presentDelegateViewController() {
        guard let delegateViewController = self.storyboard?.instantiateViewController(withIdentifier: "DelegateViewController") as? DelegateViewController else { return }
        
        delegateViewController.modalPresentationStyle = .formSheet
        delegateViewController.delegate = self
        
        self.present(delegateViewController, animated: true, completion: nil)
    }
```

- 자식 VC

```swift
protocol DataSendable: AnyObject {
    func send(data: String)
}

class DelegateViewController: UIViewController {
    weak var delegate: DataSendable?
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        self.delegate?.send(data: "Delegate")
    }
}
```

### closure 이용하기

closure를 이용하는 방법.
present된 viewcontroller에서 데이터를 전달할 클로저를 정의하고, 부모 viewcontroller에서 dataClosure의 데이터를 받아 처리한다.

- 부모 VC

```swift
   func presentClosureViewController() {
        guard let closureViewController = self.storyboard?.instantiateViewController(withIdentifier: "ClosureViewController") as? ClosureViewController else { return }
        
        closureViewController.modalPresentationStyle = .formSheet
        
        closureViewController.dataClosure = { data in
            self.dataLabel.text = data
        }
        
        self.present(closureViewController, animated: true, completion: nil)
    }
```

- 자식 VC

```swift
   var dataClosure: ((_ data: String) -> Void)?
```

### 결과 화면
![Jan-05-2023 23-09-45](https://user-images.githubusercontent.com/22000470/210799495-985ce3fc-73c4-4eca-9848-57e53741389d.gif)


### 전체 코드
- [GitHub](https://github.com/eunjooChoi/DataSendToPresentingViewController)
