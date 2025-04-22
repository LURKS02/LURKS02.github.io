---
layout: post
title: 'SwiftUI와 상태 관리 그리고 MVVM/MV'
categories: swift
---

<br>

최근에 SwiftUI를 공부하기로 마음을 먹게 되어 SwiftUI에 대한 글을 써보려고 합니다. 🥳

<br>

SwiftUI는 선언형 UI를 위한 프레임워크로, 기존의 명령형 프로그래밍보다 간결하게 뷰를 표현할 수 있습니다. <br>
이는 상태를 기반으로 동작하기 때문입니다. SwiftUI를 활용하면 보여주는 상태에 집중하여 내부적인 제어 흐름은 프레임워크가 담당합니다.<br>

이러한 SwiftUI를 사용할 때, 단순히 기존에 UIKit에서 사용하던 방식과 동일하게 MVVM으로 코드를 구성하면 되지 않을까 생각하게 되는데 MV, TCA 등 다양한 방법들이 있는 것 같아 간단하게 SwiftUI를 톺아보고 이 부분에 대한 글을 써보려고 합니다. <br>


피드백은 환영입니다! 틀린 부분이 있다면 알려주세요. 

<br>

[참고]

[[How the SwiftUI View Lifecycle and Identity work]](https://careersatdoordash.com/blog/how-the-swiftui-view-lifecycle-and-identity-work/)

[[Apple 공식 문서 - Declaring a custom view]](https://developer.apple.com/documentation/swiftui/declaring-a-custom-view)

[[Apple 공식 문서 - Managing user interface state]](https://developer.apple.com/documentation/swiftui/managing-user-interface-state)

[[WWDC2019: Data Flow Through SwiftUI]](https://developer.apple.com/videos/play/wwdc2019/226/)

[[WWDC2020: Data Essentials in SwiftUI]](https://developer.apple.com/videos/play/wwdc2020/10040/)

[[WWDC2021: Demystify SwiftUI]](https://developer.apple.com/videos/play/wwdc2021/10022/)

[[Model-View-ViewModel (MVVM)]](https://learn.microsoft.com/en-us/dotnet/architecture/maui/mvvm)

[[MV State Pattern — A Better Way of Building SwiftUI Apps]](https://medium.com/better-programming/mv-state-pattern-a-better-way-of-building-swiftui-apps-2cf2da6652fa)

<br>

<br>

## Single Source of Truth

SwiftUI에는 여러가지 프로퍼티 래퍼들이 존재합니다.<br>
대표적으로 @State와 @Binding 같은 것들이 있습니다.<br>

이번에 공부를 시작하면서 WWDC를 시청하였는데, SwiftUI에서도 "Single Source of Truth"가 중요한 의미를 가지고 있다고 느꼈습니다.<br>
이전에 간단한 사이드 프로젝트를 만들면서, 동일한 데이터를 두 뷰에서 사용하는 경우가 있었는데요.<br>
A 화면에서도 데이터를 가지고 있고, B 화면에서도 데이터를 가지고 있고, 한 쪽의 값이 업데이트되면 다른 한 쪽도 업데이트를 해주고... <br>
당연히 버그 투성이였습니다. 😅<br>

결국 데이터는 하나의 진리의 원천만 존재해야 한다는 것입니다.<br>
그렇다면 SwiftUI는 어떻게 Single Source of Truth를 유지할 수 있도록 해주는 것일까요?<br>

<br>

## @State

우선 SwiftUI에서 기본적으로 제공하는 프로퍼티 래퍼로 @State가 있습니다.<br>
이는 데이터를 유지할 수 있는 저장소를 자동으로 할당해주는 역할을 합니다.<br>
따라서 뷰가 재생성 되더라도 @State 값은 지속적으로 유지됩니다.<br>
이러한 @State를 선언하게 되면, 해당 뷰 안에서 새로운 Source of Truth가 정의됩니다.<br>

<br>

뷰가 재생성 되더라도 @State 값이 유지된다는 것은 무슨 의미일까요?<br>

우리가 만든 SwiftUI 뷰는 다양한 상태를 가질 수 있습니다. 뷰의 상태가 바뀌면, 뷰의 새로운 값이 등장합니다. <br>
이때 뷰의 새로운 값은 뷰의 변경 여부를 알아보기 위해 SwiftUI가 비교하는 데 사용되며, 즉시 사라진다고 합니다.<br>
즉, body는 계속 재평가 됩니다. 하지만 그렇다고 이 뷰가 완전히 다른 뷰일까요?<br>

SwiftUI 관점에서는 이 값들이 모두 동일한 뷰로 인식된다고 합니다. 이것을 바로 정체성(Identity)이라고 합니다.<br>
@State나 @StateObject 같은 저장소들은 이 뷰의 "정체성"에 연결된 지속적인 저장소(Persistent Storage)입니다.<br>
따라서 body가 재평가되는 것과는 관계 없이 뷰 정체성 수명 동안 유지됩니다.<br>

<br>

예시를 들어보겠습니다.

```swift
struct ParentView: View {
    @State private var toggle = false
    
    var body: some View {
        VStack {
            Button("Toggle View") {
                toggle.toggle()
            }
            
            if toggle {
                CounterView(color: .red)
            } else {
                CounterView(color: .blue)
            }
        }
    }
}

struct CounterView: View {
    @State private var count = 0
    var color: Color
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
                .foregroundStyle(color)
            
            Button("Increment") {
                count += 1
            }
        }
    }
}
```

위와 같이 중첩된 뷰 구조를 만들고, 외부 toggle 값의 변경에 따라 내부 State 값이 유지되는지 테스트를 해보았습니다.

<img src="https://github.com/user-attachments/assets/f2a61cda-493e-4b8e-b6bb-bb3e1b4365cf" width=250>

위 경우 조건 분기에 대해서 서로 다른 정체성을 가진 뷰라고 판단하기 때문에, 매번 새로운 저장소가 생성됩니다.<br>

<br>

이번에는 코드를 아래와 같이 변경하였습니다.<br>

```swift
CounterView(color: toggle ? .red : .blue)
```

<img src="https://github.com/user-attachments/assets/670c36c3-c201-4f83-add1-ab58fd32b5a3" width=250>


이 경우에는 body가 재평가 되더라도 뷰의 정체성은 유지됩니다.<br>
따라서 저장소는 초기화되지 않고 유지됩니다.<br>

<br>

## @Binding
그렇다면 @State와 자주 묶여서 설명되는 @Binding은 언제 사용하는 것일까요? <br>
@State를 선언한다는 것은 해당 뷰가 자신만의 진리의 원천을 가진다는 것을 의미합니다.<br>

예시를 가져와보겠습니다.<br>

```swift
struct PlayerView: View {
    @State private var isPlaying: Bool = false
    
    var body: some View {
        VStack {
            Text("Music").foregroundColor(isPlaying ? .red : .gray)
            
            Button(action: { self.isPlaying.toggle() }) {
            Image(systemName: isPlaying ? "pause.circle" : "play.circle")
            }
        }
    }
}
```

이런 뷰가 있다고 할 때, 재생/정지 버튼을 재사용하고 싶을 수 있겠죠?<br>
이 버튼을 컴포넌트로 분리해본다고 하면, <br>

```swift
struct PlayButton : View {
    @State private var isPlaying: Bool = false
    var body: some View {
        Button(action: {
            self.isPlaying.toggle()
        }) {
            Image(systemName: isPlaying ? "pause.circle" : "play.circle")
        }
    }
}
```

위와 같이 작성할 수 있습니다.<br>
하지만 이 경우 isPlaying이라는 상태를 버튼에서 직접 소유하게 됩니다.<br>
이렇게 되면, 버튼을 소유하는 상위 뷰와 동기화를 해줘야 하고, 재사용하기가 어려워집니다.<br>

@Binding은 이와 같은 상황에서, 외부에서 주입된 상태를 읽고 쓸 수 있도록 연결만 해주는 역할을 합니다.<br>
즉, 진리의 원천에 대한 제어권 없이 참조만 전달받아 값을 읽고 변경할 수 있습니다.<br>

```swift
@Binding private var isPlaying: Bool

...

PlayButton(isPlaying: $isPlaying)
```

따라서 위와 같이 Binding을 생성하여 전달할 수 있습니다.<br>

<br>

## ObservableObject
앞서 살펴본 것처럼 @State는 뷰 내부에 국한된 일시적인 UI 상태를 나타낼 수 있습니다.<br>
뷰의 정체성에 묶여 있는 개념이기 때문입니다.<br>
하지만 앱을 개발하다 보면 UI 외부에 있는 데이터 모델을 사용하는 경우가 많은데... 이런 것들은 어떻게 처리할까요?<br>
이때 ObservableObject 개념이 등장하게 됩니다. 🙂<br>

```swift
public protocol ObservableObject: AnyObject {
	// The type of publisher that emits before the object has changed.
	associatedtype ObjectWillChangePublisher: Publisher = ObservableObjectPublisher where Self.ObjectWillChangePublisher.Faliure == Never
	
	// A publisher that emits before the object has changed.
	var objectWillChange: Self.ObjectWillChangePublisher { get }
}
```
위는 ObservableObject의 정의입니다.<br>
보면 내부에 Publisher가 있네요... <br>
이 Publisher의 이름은 ObjectWillChangePublisher 입니다. "객체가 바뀔 것이다" 라는 의미를 가지고 있네요.<br>

![image](https://github.com/user-attachments/assets/b65038cb-5b6d-4e79-8d20-019101d30088)

애플 문서도 찾아보면, 기본적으로 ObservableObject는 @Published 속성이 변경되기 전에 값을 방출하는 objectWillChange 퍼블리셔를 자동으로 가지고 있다고 합니다.<br>
```swift
let john = Contact(name: "John Appleseed", age: 24)
cancellable = john.objectWillChange
    .sink { _ in
        print("\(john.age) will change")
}
```
따라서 이렇게도 작성이 되네요...?<br>

<img src="https://github.com/user-attachments/assets/026ac219-c624-40e0-87e0-d92a9ee3a37b" width=300>

Xcode에서 보니 ObservableObject 안에는 objectWillChange가 있고 이것은 ObservableObjectPublisher 타입입니다.<br>
이러한 ObservableObject는 클래스 전용 프로토콜이므로, 참조 타입에서만 사용 가능합니다. <br>

ObservableObject 안의 변경 가능한 프로퍼티에 @Published를 붙이면, 값이 변경되기 직전에 알림을 발행합니다. 이에 따라 SwiftUI는 자동으로 뷰를 무효화(invalidate)합니다.<br>

<br>

## ObservedObject / @StateObject / @EnvironmentObject

그렇다면 이러한 ObservableObject에 대한 의존성을 생성하기 위한 방법에는 무엇이 있을까요?<br>
SwiftUI에서 제공하는 세 가지 프로퍼티 래퍼는 다음과 같습니다.<br>
- ObservedObject
- StateObject
- EnvironmentObject

<br>

### ObservedObject
먼저 ObservedObject는 ObservableObject를 준수하는 타입을 뷰의 프로퍼티로 선언할 수 있게 해줍니다. <br>
이때 해당 인스턴스의 생명 주기는 소유하지 않습니다. <br>
@ObservedObject를 사용하면, 해당 ObservableObject의 objectWillChange에 자동으로 구독하게 됩니다.<br>
이렇게 ObservableObject에 변화가 생기면, 이 객체에 의존하는 모든 뷰를 자동으로 업데이트시킬 수 있습니다.<br>

그럼... 이건 그냥 저의 궁금증..<br>
ObservableObject 내의 Published 중 하나만 사용하는 뷰가 있어도 뷰가 다시 계산될까요..?<br>

```swift
class MyStore: ObservableObject {
    @Published var name: String = "KIM"
    @Published var age: Int = 20
}

struct ParentView: View {
    @ObservedObject var store = MyStore()
    
    var body: some View {
        VStack {
            NameView(store: store)
            
            AgeView(store: store)
            
            Button("나이 변경") {
                store.age = Int.random(in: 1...100)
            }
            
            Button("이름 변경") {
                store.name = ["LEE", "KIM", "CHOI"].randomElement()!
            }
        }
    }
}
```
위와 같이 뷰를 짜고... NameView 안에 브레이크포인트를 걸었습니다.<br>
그리고 나이 변경 버튼을 눌렀더니..?<br>

<br>

<img src="https://github.com/user-attachments/assets/c5957e7f-938c-4750-84db-65733f0cd2dc" width=700>

<br>

NameView 안에서도 브레이크포인트가 걸립니다.<br>
콘솔 명령어로 확인해보니 애초에 _store가 변경되었다고 합니다.<br>
그럼 store를 전달받지 않고 프로퍼티만 전달받으면 어떻게 될까요?<br>

<br>

<img src="https://github.com/user-attachments/assets/abfefc06-8384-4751-a2f9-cc969fd84fe6" width=700>

<br>

브레이크포인트가 걸리지 않는 모습입니다.<br>

이 결과를 봐서는 store 객체는 @Published 프로퍼티 변화에 전체적으로 반응하는 것으로 보입니다.<br>
결국 objectWillChange 퍼블리셔는 어떤 @Published 프로퍼티가 바뀌든 호출되는 것 같네요.<br>
하지만 Binding으로 받게 되면 해당 값만 관찰하는 것으로 보입니다.<br>

<br>

### StateObject
ObservableObject를 사용하면서도, 이 생명 주기를 뷰와 연결하고 싶을 때는 StateObject를 사용합니다.<br>
@StateObject를 사용할 때는 @State와 동일하게 초기 값을 제공해야 합니다. <br> 
SwiftUI는 이 값을 body가 최초로 실행되기 직전에 인스턴스화 하며, 뷰의 전체 생명주기 동안 해당 객체를 유지합니다. <br>

뷰의 생명주기 동안 Object를 유지해야 하는 이유는 무엇일까요?<br>

```swift
class MyStore: ObservableObject {
    @Published var name: String = "KIM"
    @Published var age: Int = 20
    
    init() {
        print(">>> init")
    }
}

struct ParentView: View {
    @State var isToggle: Bool = false
    
    var body: some View {
        VStack {
            Text("I'm parent.")
            
            ChildView()
            
            Button("Toggle") {
                isToggle.toggle()
            }
        }
        .foregroundStyle(isToggle ? .blue : .red)
    }
}

struct ChildView: View {
    @ObservedObject var store = MyStore()
    
    var body: some View {
        Text("I'm child.")
    }
}
```

이 내용을 설명하기 위해서... 예시 코드를 짜봤습니다!!<br>
위에서 ObservableObject를 ChildView에서는 ObservedObject로 가져오고 있습니다.<br>
이때 ParentView가 다시 그려지면 어떤 일이 일어날까요?<br>

<br>

<img src="https://github.com/user-attachments/assets/ef8d7440-8b96-404a-9a55-05c7071919f2" width=700>

<br>

뷰가 바뀔 때마다 매번 새로운 Store가 생성됩니다.<br>
왜 이런 일이 일어날까요..<br>
ObservedObject는 해당 객체를 직접 관리하거나 소유하지 않기 때문에, 부모 뷰가 바뀔 때 매번 새로운 객체가 생성되는 것입니다. <br>
이 문제를 해결하려면, 다른 곳에서 생성된 모델을 뷰에 전달하는 방법이 있었습니다.<br>

하지만 @StateObject를 사용하면, 뷰의 정체성에 따라 상태를 유지하게 됩니다.<br>

<br>

<img src="https://github.com/user-attachments/assets/9ae2a28d-ab42-4145-9b87-f5adaf8b1265" width=700>

<br>

위처럼 Store는 한 번만 생성되고, 재생성되지 않습니다.

<br>

### EnvironmentObject
SwiftUI의 뷰는 가볍고 비용이 적어 작게 재사용할 수 있는 뷰로 화면을 구성하는 것이 좋습니다.<br>
따라서 우리가 만든 앱은 뷰들의 계층 구조로 되어 있습니다. <br>
하지만 만약 상위 뷰에서 가지고 있는 ObservableObject를 제일 깊은 하위 뷰에서 사용해야 한다면 어떨까요? 제일 깊은 하위 뷰까지 해당 인스턴스를 전달해야 할까요? <br>

이 문제를 해결하기 위해 등장한 개념이 EnvironmentObject 입니다. 🙂<br>

부모 뷰에서 environmentObject(_:) 수정자로 ObservableObject를 환경에 주입하면, 하위 뷰에서는 이를 @EnvironmentObject로 읽을 수 있습니다.<br>

또 예시 코드를... 작성해 보았습니다 😇

```swift
struct ParentView: View {
    @StateObject var store = MyStore()
    
    var body: some View {
        ChildView(store: store)
    }
}

struct ChildView: View {
    @ObservedObject var store: MyStore
    
    var body: some View {
        BabyView(store: store)
    }
}

struct BabyView: View {
    @ObservedObject var store: MyStore
    
    var body: some View {
        Text("안녕하세요, \(store.name)")
        
        Button("이름 변경") {
            store.name = "LEE"
        }
    }
}
```

위 뷰는 ParentView - ChildView - BabyView 의 3단 계층 구조인데요...<br>
여기서 ChildView는 store를 사용하지도 않는데 그냥 전달하는 목적으로 선언하고 있습니다.<br>
예시는 이렇지만 실제 앱에서는 더 복잡한 depth로 뷰가 들어갈 수도 있고, 일일히 인자로 넘겨주는 것은 비효율적입니다.<br>

따라서! @EnvironmentObject를 사용할 수 있습니다. <br>

```swift
struct ParentView: View {
    var body: some View {
        ChildView()
            .environmentObject(MyStore())
    }
}

struct ChildView: View {
    var body: some View {
        BabyView()
    }
}

struct BabyView: View {
    @EnvironmentObject var store: MyStore
    
    var body: some View {
        Text("안녕하세요, \(store.name)")
        
        Button("이름 변경") {
            store.name = "LEE"
        }
    }
}
```

이렇게 상위에서 의존성을 주입해 놓으면, 하위에서 편하게 사용할 수 있습니다. <br>
전역적인 설정 값을 전달할 때는 EnvironmentObject가 좋겠네요. 🙂<br>

<br>

## 왜 objectWillChange일까?

이쯤되면 드는 의문점이 있습니다...<br>
ObservableObject에서, object가 바뀌고 나서의 값을 사용하게 되니까 objectDidChange가 더 맞지 않나 라는 생각을 하게 되는데요.<br>

```swift 
var body: some View {
    VStack {
        NameView(name: $store.name)
            
        AgeView(store: store)
            
        Button("나이 변경") {
            store.age = Int.random(in: 1...100)
        }
            
        Button("이름 변경") {
            // 전에는 KIM
            store.name = "LEE"
        }
    }
    .onAppear {
        store.objectWillChange.sink { _ in
            print(">>> \(store.name)")
        }
        .store(in: &cancellables)
    }
}
```

좀 이상하긴 한데... 이렇게 어거지로 objectWillChange가 불리는 순간 store의 값을 체크해보면 변경 이전의 값을 읽게 됩니다. <br>

애플에서는 SwiftUI가 변화가 발생하기 전에 이를 감지하여 모든 변경을 하나의 업데이트로 병합(coalesce)하기 위함이라고 설명합니다.<br>

이게 무슨 말일까요...? <br>
우선 제 예측은, 값이 바뀌기 직전에 이 변경들을 모아서 한 번의 diffing을 수행한다는 말인 것 같습니다.<br>

이번에도 실험을 준비했습니다. 😅<br>

```swift
struct ParentView: View {
    @ObservedObject var store = MyStore()
    
    @State private var cancellables = Set<AnyCancellable>()
    
    var body: some View {
        VStack {
            Text("이름: \(store.name)")
            Text("나이: \(store.age)")
            Text("학년: \(store.grade)")
            
            Button("변경") {
                store.name = "LEE"
                store.age = 10
                store.grade = 4
            }
        }
    }
}
```
한 번의 버튼 클릭에 한꺼번에 3가지 프로퍼티를 변경합니다.<br>
그렇다면 뷰의 body는 3번 다시 그려지게 될까요?<br>

(사실 SwiftUI Instrument를 사용해보고 싶었던 걸지도...)<br>

<br>

<img src="https://github.com/user-attachments/assets/9693e0c7-c747-4e89-8802-472822622c66" width=700>

<br>

측정 결과로 볼 수 있듯이 body는 단 한 번만 그려집니다.<br>

만약 값의 변경에 지연을 주게 되면 어떨까요?<br>

```swift
struct ParentView: View {
    @ObservedObject var store = MyStore()
    
    @State private var cancellables = Set<AnyCancellable>()
    
    var body: some View {
        VStack {
            Text("이름: \(store.name)")
            Text("나이: \(store.age)")
            Text("학년: \(store.grade)")
            
            Button("변경") {
                store.name = "LEE"
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.25) {
                    store.age = 10
                }
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
                    store.grade = 4
                }
            }
        }
    }
}
```

<br>

<img src="https://github.com/user-attachments/assets/e6a77e18-7df8-4b58-9e24-393cec930833" width=700>

<br>

이렇게..! body는 총 3번 호출됩니다.<br>

즉 objectWillChange로 값의 변화를 받는 것은 모든 변경을 한 번의 업데이트에 처리하기 위함이 아닐까 싶습니다. 😊<br>

<br>

<br>

## MV vs MVVM

SwiftUI를 사용할 때 MVVM을 지양해야 한다는 글을 가끔 본 적이 있습니다.<br>
왜 SwiftUI와 MVVM이 맞지 않다는 의견이 존재하는 걸까요?<br>

MVVM은 뷰-뷰데이터 사이의 관계에 대해서 설명하고 있으며, iOS에만 한정된 개념이 아닙니다.<br>
View Model은 뷰가 바인딩할 수 있는 속성과 명령을 구현하고, 상태 변경을 알릴 수 있습니다.<br>
이렇게 애플리케이션 로직과 UI 간의 명확한 분리를 통해 테스트와 확장에 유리한 코드를 작성할 수 있습니다. 🙂 <br>

기존의 UIKit에서의 MVVM은 보통 RxSwift를 통한 Binding이 수반되었는데요,<br>
```swift
class ViewController {
    override func viewDidLoad() {
        viewModel.username
            .bind(to: label.rx.text)
            .disposed(by: disposeBag)
    }
}
```
SwiftUI에서는 뷰-데이터 간에 다음과 같은 형태로 작성할 수 있습니다. <br>
```swift
struct ContentView: View {
    @State var model = Themes.listModel

    var body: some View {
        List(model.items, action: model.selectItem) { item in
            ...
        }
    }
}
```
아마 이 부분 떄문에, ViewModel이라는 불필요한 계층이 추가되었다고 보는 것 같습니다.<br>
데이터 바인딩에 초점을 맞춰서 본다면, 작은 프로젝트에서는 굳이 ViewModel이 필요 없지 않을까 생각이 들기도 합니다. <br>

그렇다면 MV는 무엇일까요? <br>
SwiftUI가 ViewModel의 역할까지 수행하는 점을 들어 별도의 ViewModel 없이 사용하는 것입니다.<br>
어떤 ObservableObject를 Store 개념으로 두고, 이를 Aggregate Root 역할로 사용한다고 합니다.<br>
(Aggregate Root란, 모델 객체에 접근하는 진입점 역할을 말합니다.)<br>

예를 들어서...<br>
```swift
class WebService: ObservableObject {
    @Published var products: [Product] = []

    func loadProducts() async throws {
        ...
    }
}
```
이런 형태의 컨텍스트를 앱에서 생성하여 사용하는 것입니다.<br>
```swift
@StateObject private var webService = WebService()
```

하지만, 앱이 커지면서 MV 만으로는 단위 테스트와 유지보수가 쉽지 않다는 의견도 있었습니다. <br>

SwiftUI가 선언형 프레임워크라는 점에 기반하여 Redux와 좀 더 어울리는 구조라는 의견도 있었고, PointFree의 TCA가 SwiftUI와 활발하게 쓰이고 있습니다.<br>

TCA에서는 ViewModel이 비즈니스 로직을 가지고 있는 것처럼, Reducer가 이 역할을 수행합니다.<br>
뷰에서 Action을 Reducer에 전달하면, State가 변화하고 이에 따라 뷰가 업데이트 되며, 이 과정에서 기능의 핵심적인 로직 및 동작은 뷰와 격리된 상태로 구현됩니다. <br>
따라서 좀 더 재사용 가능하고, 테스트가 쉬운 구조를 만들어준다고 합니다.<br>
TCA는 직관적으로 SwiftUI와 연결되기 쉽다는 점에서, SwiftUI로 프로젝트를 만들 때 고려해볼 수 있는 방안이라고 생각합니다.<br>

<br>

## 마무리

저는 여러 방안을 모두 공부해서, 진행하는 프로젝트에 맞게 최선의 선택을 하는 것이 정답이라는 생각을 하게 되었습니다. 😅<br>
제가 읽었던 아티클의 저자는 "Silver Bullet은 없다."고 말했는데요, <br> 이 말처럼 만능 해결책은 없기에 개발자는 끊임없이 공부해야 하지 않을까 싶습니다. <br>
