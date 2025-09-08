---
layout: post
title: 'iOS 아키텍처와 디자인 패턴'
categories: swift
published: false
---

아키텍처 패턴과 디자인 패턴은 모두 소프트웨어를 더 유지보수하기 쉽고 재사용 가능한 형태로 만들기 위한 방법론입니다.<br>
이들은 iOS에만 한정된 지식이 아닌 소프트웨어 공통적인 개념입니다.<br>
오늘은 평소에 얼핏 알고 있었던 이러한 개념들을 한 번 자세히 정리해보고자 합니다. 🙂


<br>


<br>

[참고]

[[Apple Document - UIViewController]](https://developer.apple.com/documentation/uikit/uiviewcontroller)

[[Apple Documentation Archive - Model-View-Controller]](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html#//apple_ref/doc/uid/TP40010810-CH14)

[[Apple Documentation Archive - Cocoa Design Patterns]](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/CocoaDesignPatterns.html)

[[iOS Architecture Patterns - Demystifying MVC, MVP, MVVM and VIPER]](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52)

[[Wikipedia - 아키텍처 패턴]](https://ko.wikipedia.org/wiki/%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98_%ED%8C%A8%ED%84%B4)

[[MVC XEROX PARC 1978-79]](https://folk.universitetetioslo.no/trygver/themes/mvc/mvc-index.html)

[[Android Architecture 패턴: MV 형제들, 옆에서 볼까 앞에서 볼까?]](https://meetup.nhncloud.com/posts/342)

[[Android MVI]](https://jaehochoe.medium.com/android-mvi-7304bc7e1a84)

[[Github: Design-Patterns-In-Swift]](https://github.com/ochococo/Design-Patterns-In-Swift?tab=readme-ov-file)

[[Factory Comparison]](https://refactoring.guru/design-patterns/factory-comparison)

<br>

<br>

## 아키텍처 패턴과 디자인 패턴
아키텍처 패턴과 디자인 패턴, 무엇이 다를까요? 🤔<br>
둘 다 소프트웨어의 범용적인 문제를 해결하기 위한 설계 원칙인데요, 왜 패턴이라는 말을 사용하는 것일까요?<br>
그 이유는 반복적으로 등장하는 문제에 대한 반복적인 해결책이기 때문이라고 합니다.<br>

핵심적으로는 적용 범위에 약간의 차이가 있다고 하는데요! <br>
아키텍처 패턴은 가장 높은 수준의 애플리케이션 설계 스타일(아키텍처 스타일)을 구현하기 위한 방법이고, 디자인 패턴은 국지적인 문제를 해결하기 위한 방법이라고 합니다.<br>
따라서... 아키텍처 패턴은 애플리케이션 전체 수준의 구조와 설계를 구상하기 위한 문제를 해결하고, 디자인 패턴은 구성 요소 수준에서 구체적인 문제를 해결합니다.<br>

어느 정도 두 개념의 차이를 확인했으니, 이제 iOS에서 자주 쓰이는 패턴에는 어떤 것들이 있는지 배워보도록 하겠습니다. 🥳

<br>

## 아키텍처 패턴
아까 아키텍처 패턴은 애플리케이션 전체 수준의 문제를 해결한다고 했습니다.<br>
결국 애플리케이션을 어떻게 구성하고, 각 책임을 어떻게 분리할 것인가에 대한 문제를 해결하는 방법입니다.<br>
코드를 중복 없이 간결하게 작성하고, 구성 요소들 간에 책임을 적절히 분배해서 테스트가 가능하도록 하면 장기적으로 서비스를 개발할 수 있는 좋은 기반을 만들 수 있겠죠!<br>

이렇게 애플리케이션 전체 수준의 문제를 해결하는 방법 중 하나로.. 널리 알려진 MVC가 있습니다.<br>

<br>

### MVC 패턴
MVC는 iOS에만 한정된 개념이 아닙니다.<br>
다양한 분야와 플랫폼에서 MVC 구조를 채택하여 사용하고 있습니다.<br>
그렇다면 이러한 MVC가 도대체 어떤 이점을 가졌기에 이렇게 널리 사용되었는지, "왜" 사용하는지를 아는 것이 중요하겠죠?! 😯<br>

MVC의 기원부터 알아보겠습니다!<br>
MVC는 1979년 Xerox PARC에서 Smalltalk 언어를 개발하던 중 등장한 아키텍처라고 합니다.<br>
당시 Alan Kay의 Dynabook과 같이 사용자의 의도를 중심으로 설계되어 사용자가 컴퓨터를 지배할 수 있도록 하는 구조가 MVC의 기초 철학이 되었습니다.<br>
[MVC XEROX PARC 1978-79](https://folk.universitetetioslo.no/trygver/themes/mvc/mvc-index.html)에서는 사용자가 방대하고 복잡한 데이터 세트를 직접 조작하지 않도록, 인간 사용자의 정신 모델과 컴퓨터 내 디지털 모델 사이의 간극을 메우는 데 MVC의 본질적인 목적이 있다고 말합니다.<br>
CLI 기반의 일반인이 사용하기 어려웠던 컴퓨터를 넘어서 사용자가 정보를 직접 보고 조작하는 것처럼 느끼게 하는 것이 중요했던 것이죠!<br>

따라서 MVC의 각 역할에는 사용자 중심의 철학이 녹아들어 있습니다.<br>
Model은 사용자의 데이터를, View는 사용자가 이해할 수 있는 시각적인 창, Controller는 사용자의 의도를 해석하기 위한 상호작용의 중심적인 역할을 표현합니다. <br>

이러한 MVC 철학은 자연스럽게 Apple, Microsoft 등으로 퍼져나갔습니다.<br>
그렇게 영향을 받게 된 것이 Cocoa Framework이고, UIKit 또한 Cocoa 기반으로 만들어졌기 때문에 MVC 기반으로 설계되어 있습니다.<br>

전통적인 Smalltalk의 MVC 개념에서는 모델 객체의 상태가 바뀌었을 때 Observer 패턴을 통해 뷰 객체에게 변경을 알릴 수 있었습니다.<br>

<img src="https://github.com/user-attachments/assets/02d5b82b-d508-4c7b-9ce5-67e2d12287ce" width=700>

이때 뷰와 모델이 직접적으로 연결되면 재사용이 어려워지겠죠?<br>
따라서 Cocoa MVC에서는 컨트롤러가 뷰와 모델 사이의 중재자 역할을 수행하게 됩니다.<br>
뷰와 모델 객체를 분리시키고, 고도로 재사용할 수 있도록 하기 위한 전략입니다.<br>

<img src="https://github.com/user-attachments/assets/12a00797-2851-462b-9ece-bbe833945653" width=700>

따라서 Cocoa 버전의 MVC는 위와 같은 형태로 구성되었습니다.<br>
즉, 컨트롤러 객체가 모델과 뷰 사이의 데이터 흐름을 양방향으로 중재하게 됩니다.<br>
Apple에서는 MVC의 각 역할을 병합할 수 있으며, 객체가 컨트롤러와 뷰의 역할을 모두 수행하게 된다면 이를 뷰 컨트롤러(View Controller)라고 부른다고 말합니다.<br>

이렇게 iOS의 ViewController는 모델이 바뀌면 뷰를 갱신하고, 사용자-뷰 사이의 상호작용에 반응하며, 뷰를 관리합니다.<br>
하지만, 이는 ViewController가 View의 생명주기에 관여하는 만큼 강하게 결합되어 있는 형태입니다.<br>
따라서... Apple의 ViewController는 사실상 View와 별개의 요소라고 하기 어렵습니다.<br>
이로 인해서 흔히 Massive View Controller라고 하는 문제가 발생하게 되는데요!<br>
뷰와 로직이 ViewController 내에서 뒤섞이면서 비즈니스 로직만 테스트하기가 어려워지고, ViewController의 책임이 너무 커지게 됩니다.<br>

<br>

### MVP 패턴
MVP 패턴은 Massive View Controller 문제를 해결하기 위해 등장한 아키텍처 패턴 중 하나입니다. <br>

<img src="https://github.com/user-attachments/assets/7e64e52c-26e5-4d5b-8f0b-649b3184014b" width=700>

MVP 패턴을 살펴보면 위에서 봤던 MVC와 동일한 형태를 가지고 있는 것을 확인할 수 있습니다. 무엇이 바뀐 걸까요?<br>
기존의 UIViewController는 이제 View 영역에 속하게 되었고, 별도의 Presenter가 존재합니다.<br>
즉, 기존에 로직이 UI와 결합되어 있던 것을 분리하기 위해서 View와 Model 사이의 중재자 계층을 추가한 것입니다.<br>
따라서 Presenter는 사용자 액션을 처리하고, Model의 데이터를 받아 View에 전달하는 역할을 맡게 됩니다.<br>

```swift
protocol ExampleView: AnyObject {
    func showData(_ data: String)
}
```

```swift
final class ExamplePresenter {
    weak var view: ExampleView?
    let exampleService: ExampleServiceProtocol

    init(view: ExampleView, service: ExampleServiceProtocol) {
        self.view = view
        self.exampleService = service
    }

    func doSomething() {
        exampleService.fetchData { [weak self] data in
            self?.view?.showData(data)
        }
    }
}
```

이렇게 되면 UI와 로직을 분리하게 되면서 테스트가 가능해지고, View에 대한 Mocking도 수월해지겠죠?<br>
하지만 여전히 모든 로직이 Presenter에 몰려 Massive Presenter가 될 가능성이 있습니다.<br>

그러나 MVP의 또 다른 한계점이 존재했습니다.<br>
예를 들어서...<br>
```swift
protocol MyView: AnyObject {
    func show(data: String)
}

class SomePresenter {
    weak var view: MyView?

    func doSomething() { ... }
    ...
}
```
이런 관계의 View와 Presenter가 있다고 하면,<br>
```swift
final class MyViewController: UIViewController, MyView {
    var presenter: SomePresenter!

    override func viewDidLoad() {
        super.viewDidLoad()
        presenter.doSomething()
    }

    func show(data: String) { ... }
}
```
이런식으로 ViewController에서 사용하고 있겠죠!<br>

여기서 만약에.. doSomething이라는 Presenter의 로직을 똑같이 재사용하고 싶다고 해봅시다.<br>
MyView 프로토콜을 정의해뒀기 때문에 MyView를 따르는 뷰에서는 재사용이 가능하겠죠.<br>
하지만 View가 MyView와 다른 형태라면...? MyView 프로토콜을 따르지 않는다면 어떻게 해야 할까요?<br>
별도의 Presenter와 별도의 View 프로토콜이 존재해야 되겠죠! <br>

결국 Presenter가 특정한 View 프로토콜에 맞춰져 있는 형태이기 때문에 1:1의 구도가 됩니다.<br>
이를 해결하기 위해 값을 노출하는 형태의 ViewModel이 등장하게 됩니다.<br>

<br>

### MVVM 패턴

<img src="https://github.com/user-attachments/assets/4ce178df-a184-4b2f-90b1-3785e63cdb85" width=700>

MVVM 패턴의 View와 Model 개념은 이전과 동일합니다.<br>
다만 Presenter에서 ViewModel로 교체가 되었네요!<br>

기존의 Presenter는 View Protocol과 결합된 형태였습니다.<br>
하지만 ViewModel은 View와 무관합니다. 즉 ViewModel은 View에 필요한 값을 가지고 있고, 이 값이 변경될 때 View가 자동으로 업데이트 됩니다.<br>
MVVM에서 View는 ViewModel과 바인딩하여 스스로 상태를 업데이트하는 책임이 더 생기게 됩니다.<br>
따라서 ViewModel은 View와 분리되어 있기 때문에 테스트가 쉽고, 로직의 재사용도 가능합니다. 🥳<br>

하지만 화면에 나타나는 모든 정보를 ViewModel에서 관리하면, 당연히 여러 개의 상태 변수가 존재하겠죠?<br>
이 경우 서로 관련된 상태임에도 별개로 관리되면서 업데이트 순서가 꼬이거나 의도치 않은 동작이 발생하기 쉽습니다.<br>
여러 LiveData가 흩어져 있고, 결국 데이터의 흐름을 제어하지 못하기 때문에 버그가 발생합니다.<br>

또한 MVVM에서는 Side Effect가 암묵적으로 메서드 내부에서 흩어져 있습니다. <br>
Side Effect(부수 효과)란, 외부 상태에 영향을 주거나 외부 상태에 의존하여 예측 불가능한 결과를 내는 것을 말하는데요!<br>
기존의 MVVM에서는 상태가 동시에 변화하는 과정에서, Side Effect까지 뒤섞여 동작이 발생한 원인을 파악하기가 어려워집니다.<br>

<br>

### MVI 패턴
위와 같이 상태 관리의 어려움을 해결하기 위해 MVI 패턴이 등장하였습니다!<br>
MVI에서는 이를 단방향 데이터 흐름과 단일 상태 관리로 해결합니다.<br>

<img src="https://github.com/user-attachments/assets/6d67cd46-6ea6-47fa-bc85-76bbbc2d427b" width=700>

한 방향으로 로직이 흘러가는 모습이 보이시나요?<br>

MVI에서는 Intent를 통해 사용자의 의도를 나타냅니다. 사용자 액션이나 이벤트를 말하는 것이겠죠!<br>
View가 발행한 Intent를 통해 State가 변경되거나 Effect가 방출됩니다. <br>
이때 State는 별도의 LiveData로 이루어져 있지 않고 데이터의 불변 묶음을 의미합니다.<br>
이렇게 단일 상태를 관리함으로써 하나의 입력에 대해 하나의 상태 변화 결과를 검증하게 되면서 상태 불일치가 생기지 않게 됩니다.<br>
그리고 UI는 State라는 단일 상태만을 기준으로 다시 그리면 되니까 여러 LiveData를 고려하지 않아도 되겠죠!<br>

기존에 예측할 수 없었던 Side Effect같은 경우, Effect라는 흐름으로 분리하여 다시 Intent를 발생시킬 수 있도록 합니다.<br>
Effect가 다시 Intent를 발생시킨다는 것은 무엇을 의미할까요?<br>
결국 Effect 자체는 분리되고, Reducer는 순수하게 유지될 수 있음을 뜻하겠죠? 외부 영향을 받는 부분을 분리한 것입니다. 🙂<br>
이렇게 되면 예측이 가능하고, 테스트 코드를 작성하기가 용이해집니다.<br>

즉 MVI 패턴은 앞서 설명했던 다른 아키텍처보다는 앱 상태와 데이터 흐름을 다루는 방식에 대한 방법론에 가깝습니다.<br>

<br>

이렇게 MV(X) 시리즈를 모두 살펴보았네요! 😊<br>

<br>

## 디자인 패턴

이번에는 여러가지 디자인 패턴에 대해 알아보도록 하겠습니다.<br>

Apple은 [이 문서](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/CocoaDesignPatterns.html)에서 Cocoa의 기본적인 디자인 패턴들에 대해서 설명하고 있는데요, <br>
디자인 패턴이란 특정한 상황에서 반복적으로 발생하는 문제를 해결하기 위한 추상적인 설계라고 합니다.<br>
어떤 언어든, 어떤 플랫폼이든 구현 방식 자체는 달라질 수 있지만 해결하려는 문제는 동일합니다.<br>
즉.. 디자인 패턴 또한 특정한 언어나 문법에 종속된 기술이 아니라는 것이죠!<br>

그렇다면, 자주 사용되는 디자인 패턴과 Apple에서 소개하는 기본적인 패턴 몇 가지를 살펴보도록 하겠습니다. 😊<br>

<br>

### Singleton 패턴
Singleton 패턴은 클래스에 인스턴스가 하나만 존재하도록 보장하는 디자인 패턴입니다.<br>
shared라는 전역 접근 지점을 제공하고, 다른 인스턴스가 더 생성되지 못하도록 막는 것이 특징입니다.<br>

```swift
final class Singleton {
    static let shared = Singleton()

    private init() { }

    ...
}
```

위 코드처럼 전역 인스턴스를 만들고, 생성자를 막아 해당 인스턴스 1개만 사용하는 것입니다.<br>

Singleton 패턴은 언제 사용될까요? 인스턴스는 왜 하나만 존재해야 하는 걸까요?<br>
예를 들어.. 데이터베이스나 네트워크와 같이 공통된 리소스를 여러 객체가 사용할 때 싱글톤 패턴을 통해 구현할 수 있습니다.<br>
상태를 공유해야 하고, 전역적으로 인스턴스 하나만 유지하는 것이 안정적인 경우입니다. <br>
UIKit에서는 UIApplication을 예시로 들 수 있겠네요!<br>

저도 프로젝트를 진행할 때 네트워크나 데이터베이스와 관련된 코드는 싱글톤으로 작성한 경험이 있습니다. 🙂<br>

하지만 역시 만능 해결책은 없습니다! 이러한 Singleton 패턴의 단점은 무엇일까요?<br>

```swift
final class CounterManager {
    static let shared = CounterManager()
    private init() { }

    var count = 0
}
```
이런 싱글톤 인스턴스가 있다고 할 때, CounterManager를 테스트하고 싶다면 어떻게 해야 할까요?<br>

```swift
@Test func testA() async throws {
    CounterManager.shared.count = 10
    #expect(CounterManager.shared.count == 10)
}

@Test func testB() async throws{
    #expect(CounterManager.shared.count == 0)
}
```
한 번 이렇게 테스트 코드를 작성해 보았습니다.<br>

처음에는 CounterManager가 0일 것이고, CounterManager의 count를 10으로 바꾸면 당연히 10이 되겠죠?<br>

<img src="https://github.com/user-attachments/assets/400b8dda-9b8f-4a3a-9a14-9dcd4c1cd33b" width=700>

이렇게 잘 작동하는 것을 확인할 수 있습니다!!<br>
잘... 작동...?<br>

<img src="https://github.com/user-attachments/assets/68dcab2e-baf5-4719-acfc-af3a214c5e04" width=700>

동일한 코드를 가지고 다시 테스트를 돌렸는데, 이번에는 결과가 다릅니다. <br>
테스트를 실행할 때마다 결과는 계속 달라지게 됩니다.<br>
원인은 CounterManager를 Singleton으로 구현하였기 때문인데요! <br>

싱글톤은 기본적으로 인스턴스의 개수를 1개로 제한해놓은 상태이기 때문에... 이전 테스트 결과가 다음 테스트에 영향을 끼치는 구조입니다.<br>
그래서 testB가 먼저 실행되었을 때는 테스트가 성공하고, 그 반대의 경우 count가 10이 되면서 테스트가 실패하는 것이죠!<br>
즉, 전역 상태가 이전 테스트로 인해 오염되게 됩니다.<br>


<br>

### Facade 패턴

이번에는 파사드 패턴에 대해서 알아보겠습니다.<br>

<img src="https://github.com/user-attachments/assets/95de75c3-ab33-4dae-830f-11555a5f5fd6" width=700>

파사드 패턴은 복잡한 서브시스템들의 인터페이스를 하나로 감싸서, 단순한 인터페이스만 외부에 제공하는 디자인 패턴입니다. <br>

Apple에서는 NSImage가 Facade 패턴의 예시라고 말합니다. <br>
우리가 사용하는 이미지는 JPEG, PNG, TIFF, PDF, EPS... 등등 다양한 포맷을 가지고 있습니다.<br>
하지만, 각 포맷에 맞게 직접 디코딩을 해주거나, 스케일 계산하고 렌더링 최적화를 해주지 않아도 우리는 이미지를 쉽게 코드에서 사용할 수 있습니다. (아직은)<br>

바로 이러한 복잡한 로직들을 UIImage에서 내부적으로 대신 처리해주는 것이죠!<br>

```swift
let image = UIImage(named: "photo.jpg")
```
우리는... 단순히 이 한 줄 하나로 이미지를 쉽게 사용할 수 있습니다. (UIImage... 고마운 녀석이네요 🥹)<br>
이렇게 실제로 이미지를 표현하고 포맷 처리를 담당하는 복잡한 서브시스템들은 숨기고, 단순한 인터페이스만 노출시켜 단순화시키는 방법이 바로 파사드 패턴입니다.<br>

<br>

### Adapter 패턴
Adapter 패턴은 클래스 인터페이스를 클라이언트가 원하는 다른 인터페이스로 변환합니다. <br>
호환되지 않는 인터페이스끼리 동작할 수 있도록 연결해주는 디자인 패턴인데요..!<br>

그러니까, 기존 클래스가 제공하는 인터페이스가 우리가 원하는 형태와 맞지 않을 때! 말그대로 둘 사이를 연결해주는 Adapter를 만들어주는 방법이 바로 Adapter 패턴입니다.<br>
마치 110v 플러그에 어댑터 없이는 220v 플러그를 연결할 수 없는 것과 똑같습니다. 😅<br>

단순하게 생각하면 아래와 같은 구조가 되겠네요.<br>

```swift
struct Old {
    var oldInterface: Float
}

protocol New {
    var newInterface: Double { get }
}

struct NewAdapter: New {
    private let old: Old

    ...

    var newInterface: Double {
        return Double(old.oldInterface)
    }
}
```

기존의 Float 기반의 Old에서 우리가 원하는 Double 형태의 인터페이스를 사용하기 위해 New 프로토콜을 채택하는 어댑터로 연결을 해준 모습입니다!<br>
Protocol의 경우 호환되지 않는 인터페이스 간 상호작용을 가능하게 해준다는 점에서 Adapter 패턴과 목적이 일치한다고 할 수 있겠습니다. <br>

실제 상황에서는... 레거시 코드나 외부 라이브러리를 코드 수정 없이 우리가 원하는 형태로 호출할 수 있겠죠!<br>

<br>

### Factory Method 패턴
Factory Method 패턴은 객체의 생성을 서브클래스에 위임하는 패턴입니다!<br>
생성을 서브클래스가 맡게 된다는 것은, 클라이언트 코드에서는 구체적인 타입을 몰라도 동작할 수 있어야 한다는 말입니다. <br>
왜 클라이언트 코드가 구체적인 타입을 알면 안될까요? 바로 구체 클래스에 대한 의존도를 낮추기 위함입니다.<br>
구체 클래스에 의존하게 되면 나중에 새로운 타입이 추가되거나 변경 사항이 생겼을 때 구체적인 클래스에 의존하고 있던 모든 코드를 수정해야겠죠? 🤔<br>
이러한 일을 막기 위해서 클라이언트 코드가 구체적인 타입을 알지 못하도록 객체 생성 코드를 캡슐화 한 것입니다. <br>

즉, 서브 클래스에서 팩토리 메서드를 오버라이드하여 특정 Product를 생성할 수 있도록 하는 방법입니다.<br>

<br>

<img src="https://github.com/user-attachments/assets/1ab642fc-a664-4919-8fd0-3673f1a36b00" width=700>

<br>

정확히는 Factory Method 패턴은 이런 구조를 나타냅니다.<br>
생성 메서드는 구체적인 구현체 대신 추상화된 대상을 만들게 되며, 서브클래스에서 실제로 어떤 구현체를 만들지 결정하는 방법입니다.<br>

```php
abstract class Department {
    public abstract function createEmployee($id);

    public function fire($id) {
        $employee = $this->createEmployee($id);
        $employee->paySalary();
        $employee->dismiss();
    }
}

class ITDepartment extends Department {
    public function createEmployee($id) {
        return new Programmer($id);
    }
}

class AccountingDepartment extends Department {
    public function createEmployee($id) {
        return new Accountant($id);
    }
}
```
이렇게 추상 메서드를 통해 하위 클래스에서 어떤 구체 클래스를 생성할지 결정해주게 됩니다.<br>

Swift에서는 이 개념을 어떻게 응용할 수 있을까요?<br>
```swift
protocol Employee {
    func paySalary()
    func dismiss()
}

class Programmer: Employee {
    ...
}

class Accountant: Employee {
    ...
}
``` 
이렇게 프로토콜을 통해 추상화했을 때... <br>
```swift
protocol Department {
    func createEmployee(id: Int) -> Employee
    func fire(id: Int)
}

extension Department {
    func fire(id: Int) {
        let employee = createEmployee(id: id)
        employee.paySalary()
        employee.dismiss()
    }
}

class ITDepartment: Department {
    func createEmployee(id: Int) -> Employee {
        Programmer(id: id)
    }
}

class AccountingDepartment: Department {
    func createEmployee(id: Int) -> Employee {
        Accountant(id: id)
    }
}
```
아마 이런식으로 기본 동작에서 생성되는 객체의 종류를 바꿔줄 수 있겠죠..?<br>
Swift에는 abstract 키워드가 없는 대신 protocol과 enum이 있기 때문에, 좀 더 종류별 생성에 초점을 맞춰 사용할 수 있는 것 같습니다.<br>

```swift
enum Theme {
    case light
    case dark
}

enum ButtonFactory {
    static func createButton(for theme: Theme) -> ThemedButton? {
        switch theme {
            ... // factory
        }
    }
}
```

<br>

### Abstract Factory 패턴

이번에는 추상 팩토리 패턴입니다!<br>
위에서 등장했던 Factory Method 패턴과 비슷해서 혼동할 수 있지만, 조금 다른 의미를 가진다고 합니다.<br>

우선 추상 팩토리 패턴은... 관련된 객체들의 집합을 구체적인 클래스를 지정하지 않고도 생성할 수 있도록 하는 디자인 패턴입니다. <br>
즉 연관되거나 의존적인 객체들을 일관적으로 생성할 수 있게 도와주는 것이죠!<br>

만약 어떤 테마에 맞춰 컴포넌트를 생성해야 한다고 가정하면, 개별적으로 생성해서 주입해주는 방식은 확장성이 떨어지고, 특정 구현체에 의존할 위험이 있습니다. <br>
이 경우에는, 해당 테마에 맞게 추상화된 구현체 세트를 생성해주는 공장을 지어주는 것이 올바른 방향이겠죠?<br>

<br>

<img src="https://github.com/user-attachments/assets/e888d838-a5ef-487d-a817-2515359e08bd" width=700>

<br>

전반적인 형태는 위와 같습니다. <br>
전체 제품의 생성 목록을 정의하는 Abstract Factory가 있고, 이에 대한 구체적인 구현체 Factory가 존재합니다.<br>
이 Factory들은 결국 추상적인 제품들을 생산하기 때문에, 클라이언트에서는 각 팩토리에서 어떤 제품이 생성되는지 알 필요가 없습니다.<br>
단지 Factory의 구체적인 구현 내에서 연관된 실제 객체들을 생성해주기만 하면 되는 것이죠!<br>
따라서 연관된 제품들의 생성 코드를 집중화할 수 있어 책임에 따라 코드를 분리하기 용이합니다.<br>

<br>

### Builder 패턴

Builder 패턴은 이름에서부터 어떤 결과물을 만들어내는 방법이라는 것이 느껴지는데요,<br>
이 패턴은 복잡한 객체를 단계적으로 구성할 수 있도록 도와줍니다.<br>

예를 들어서...<br>
```swift
struct Profile {
    let name: String
    let age: Int
    let email: String?
    let address: String?
    let phoneNumber: String?
    ...
}
```
이렇게... 사용자 프로필이라는 모델이 있다고 가정합니다.<br>
이 모델의 멤버와이즈 이니셜라이저는 어떻게 형성될까요?<br>

```swift
init(
    name: String,
    age: Int,
    email: String? = nil,
    address: String? = nil,
    phoneNumber: String? = nil
    ...
)
```
아마 이런 형태가 될 것입니다.<br>
예시 코드라서 비교적 덜 복잡한 형태이지만, 실제로는 더 많은 프로퍼티들이 요구될 수 있습니다.<br>
이렇게 되면 해당 객체를 생성하는 생성자가 너무 길고 복잡해지는 문제가 있습니다.<br>
게다가 모든 사용자 프로필에 이메일이나 주소 정보가 포함되는 것이 아니기 때문에 불필요한 초기화 과정이 포함됩니다.<br>

그렇다고 해서, 생성자 길이와 모든 경우의 수를 고려하여 생성자를 분리한다고 생각해보면...<br>
```swift
extension UserProfile {
    init(name: String, age: Int) {
        self.init(name: name, age: age, email: nil, address: nil, phoneNumber: nil)
    }

    init(name: String, age: Int, email: String) {
        self.init(name: name, age: age, email: email, address: nil, phoneNumber: nil)
    }

    init(name: String, age: Int, email: String, address: String) {
        self.init(name: name, age: age, email: email, address: address, phoneNumber: nil)
    }

    ...
}
```
프로퍼티가 추가됨에 따라 생성자 계층이 무한히 늘어날 수도 있겠네요. 😅<br>
이 문제를 해결하기 위해 Builder 패턴을 응용할 수 있습니다.<br>

<br>

```swift
class UserProfileBuilder: UserProfileBuildable {
    ...

    func setName(_ name: String) -> Self {
        self.name = name
        return self
    }

    func setAge(_ age: Int) -> Self {
        self.age = age
        return self
    }

    func setEmail(_ email: String) -> Self {
        self.email = email
        return self
    }

    func setAddress(_ address: String) -> Self {
        self.address = address
        return self
    }

    func setPhoneNumber(_ phone: String) -> Self {
        self.phoneNumber = phone
        return self
    }

    func build() -> UserProfile {
        return UserProfile(...)
    }
}

let builder: UserProfileBuildable = UserProfileBuilder()

let profile = builder
    .setName("Alice")
    .setAge(28)
    .setEmail("alice@example.com")
    .build()
```
위처럼 유저 프로필을 구성하기 위한 공통적인 구성 단계를 정의할 수 있습니다.<br>
이렇게 되면 객체에 필요한 구성 단계만 선택하여 호출할 수 있기 때문에, 다양한 형태의 제품을 만들 수 있게 됩니다.<br>
복잡한 객체를 단계적으로 필요한 부분만 구성할 수 있게 되는 것이죠!<br>
저는 이전에 네트워크 레이어를 구성하면서 Builder 패턴을 기반으로 HttpRequest를 구현했던 적이 있었는데요, 복잡한 요청을 이와 같이 메서드 체이닝 형태로 나타낼 수 있었습니다. 🙂<br>

<br>

### Observer 패턴
유명한 Observer 패턴입니다! 이 패턴은 특정한 객체에서 발생한 이벤트를 여러 객체에게 알릴 수 있도록 해주는 구독 메커니즘을 표현합니다.<br>

어떤 값이 바뀌었을 때 이를 코드의 다른 부분에서 알아채기 위해서는 어떻게 해야 할까요?<br>
해당 값이 바뀌었는지 주기적으로 확인하거나, 값이 바뀌었다고 직접 알리는 방법이 있을 것입니다.<br>
전자의 경우 값이 바뀌지 않았을 때도 관찰을 해야 하므로 리소스 소모의 위험이 있고, 후자의 경우 값이 바뀐 쪽에서 알려야 하는 대상을 정확히 알아야 한다는 문제가 있습니다.<br>

따라서 Observer 패턴은 Publisher가 공통된 인터페이스를 사용하는 구독자들의 목록을 관리하며, 새로운 이벤트가 발생했을 때 각 구독자들이 정의한 알림 메서드를 호출하는 형태를 가지고 있습니다.<br>
인터페이스를 기반으로 연결되기 때문에 Publisher와 구독자가 일대다로 느슨하게 결합되는 형태입니다.<br>
이때 구독자 목록은 런타임 동안 동적으로 구성될 수 있습니다. 따라서 알림을 구독하고, 취소하는 동작이 가능합니다.<br>

Observer 패턴의 예시로 Swift에서는 NotificationCenter를 떠올릴 수 있는데요! 이는 Observer 패턴을 기반으로 한 일대다 메시지 브로드캐스트 형태로 구현되어 있습니다.<br>
문자열로 식별되는 알림을 어디서든 게시(post)할 수 있고, NotificationCenter는 관찰자 목록에 등록된 객체들에게 메시지를 통해 알림을 전송합니다.<br>

또는 Combine의 구독 형태를 쉽게 떠올릴 수 있습니다.<br>
```swift
let cancellable = viewModel.$username
    .sink { newValue in ... }
```
이런 식으로 관찰하고자 하는 값에 Observer를 추가할 수 있었습니다.<br>
구독의 결과물이 Cancellable인 이유는, Publisher가 Subscriber를 참조하며 구독을 유지하기 때문입니다.<br>
구독의 수명을 제어하기 위한 의미인 것이죠! 🙂<br>

<br>

### Strategy 패턴
전략 패턴은 알고리즘에 관한 패턴입니다. 각 로직을 별도의 클래스로 분리하여 교체하며 사용할 수 있도록 하는 방법입니다.<br>
어떤 작업을 수행하기 위한 알고리즘을 정의한다고 할 때, 이 알고리즘을 별도의 클래스로 추출한다면 이것을 전략(Strategy)이라고 부릅니다.<br>
전략을 사용하는 Context에서는, 추상화된 전략에 대한 참조를 가지고 이 객체에 작업을 위임합니다. 즉 알고리즘을 추상화시킨 형태인 것이죠!<br>
이러한 전략 객체만 교체해준다면 동적으로 다른 알고리즘을 사용할 수 있게 됩니다.<br>

따라서... Strategy 패턴은 특정한 동작을 추출하여 추상화시키는 방법이라고 생각할 수 있겠네요.<br>

놀랍게도 앞서 배웠던 MVC 패턴에는 Strategy 패턴이 적용되어 있습니다. 어디에 숨어있는 걸까요? 🤔<br>
바로... 컨트롤러 객체입니다!<br>
뷰는 화면을 표현하는 역할만 담당하고, 뷰의 이벤트를 받아 해석하여 데이터 흐름을 중재하는 전략을 적용하는 것은 컨트롤러의 역할입니다.<br>
따라서.. 뷰가 이벤트에 대한 해석을 전적으로 컨트롤러에 위임하고 있는 형태이기 때문에 뷰 입장에서는 컨트롤러가 전략 객체인 셈입니다.<br>

아무튼 전략 패턴은 추상화의 중요성을 설명하는 디자인 패턴이라고 할 수 있겠네요! <br>

<br>

### Composite 패턴
Composite 패턴은 객체를 트리 구조로 구성할 수 있도록 도와주는 디자인 패턴입니다.<br>
표현하고자 하는 모델을 계층 구조로 나타낼 수 있다면... 동일한 인터페이스로 단일 요소와 복합 요소를 표현하게 됩니다.<br>
이 말은 무엇을 의미할까요? 만약 트리 구조로 나타낼 수 있는 어떤 객체의 계층 구조가 있다고 할 때, 트리의 부분 집합이 될 수 있는 객체 구성(Composition)을 객체와 동일하게 취급하겠다는 것을 말합니다.<br>
즉... 단일 요소와 복합 요소가 동일한 프로토콜로 추상화가 되어 있는 것이죠!<br>

따라서 클라이언트 입장에서는 이게 단일 요소인지, 복합 요소인지 알지 못합니다.<br>
그리고 해당 객체가 단일 요소가 아닌 경우에는, 하위 요소 배열에 요청을 재귀적으로 전달하여 결과를 합산합니다.<br>
이렇게 되면... 클라이언트는 리프와 컨테이너에 대해 동일한 인터페이스 기반으로 단순하게 처리할 수 있습니다.<br>

UIKit에서는 뷰의 계층 구조를 예시로 들 수 있겠네요!<br>

<img src="https://github.com/user-attachments/assets/ac94d5ba-e499-43ea-903e-af61bb8b0da3" width=400>

UIWindow의 Content View 안에 다양한 서브 뷰가 추가되고... 다른 서브 뷰를 추가적으로 포함하는 UIView, UIStackView가 있을 수도 있고, 말단에는 UILabel이나 UIImageView 같은 것들이 존재할 수 있겠죠!<br>
하지만 이들은 모두 UIView로 취급되며, 서브 뷰를 많이 가지고 있는 뷰도 UIView일 뿐 입니다.<br>
이러한 뷰 계층 구조를 따라 그리기를 수행하기도 하고, 재귀적으로 탐색하며 이벤트 처리를 수행하기도 합니다.<br>
이처럼... UIKit은 Composite 패턴을 응용하여 UIView라는 하나의 인터페이스로 뷰 계층을 구성하고 재귀적으로 탐색하며 필요한 작업을 처리할 수 있도록 하고 있습니다.<br>

<br>

## 마치며
오늘은 몇 가지 주요 아키텍처 패턴과 디자인 패턴에 대해서 간단하게 알아보았는데요!<br>
결국 이들은 공통된 문제를 해결하기 위한 방법이기 때문에, "왜" 등장하게 되었는지를 이해하는 것이 중요한 부분인 것 같습니다.<br>
지금까지 다양한 개발자들이 해결하려고 노력한 부분이기도 하면서, 앞으로 저희가 해결해나가야 할 문제와 가까울 것이라고 생각하기 때문입니다.<br>

몇 가지 오늘 다루지 않은 디자인 패턴들도 나중에 추가할 수 있게 되면 좋을 것 같네요. 🙂<br>
