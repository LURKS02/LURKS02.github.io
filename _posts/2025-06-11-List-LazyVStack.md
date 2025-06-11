---
layout: post
title: 'List와 LazyVStack에 대하여'
categories: swift
---


최근에 SwiftUI를 활용해서 뷰를 짜면서 수직으로 반복되는 뷰를 구현할 일이 생겼는데요!<br>
SwiftUI에서는 주로 List와 LazyVStack + ScrollView의 조합을 많이 사용하는 것 같습니다. <br>
그래서 이 둘 사이에 어떤 차이점이 있는지 알아보고자 글을 쓰게 되었습니다. 🙂 <br>


<br>


<br>

[참고]

[[Apple 공식 문서 - List]](https://developer.apple.com/documentation/swiftui/list)

[[Apple 공식 문서 - LazyVStack]](https://developer.apple.com/documentation/swiftui/lazyvstack)

[[Apple 공식 문서 - Displaying data in lists]](https://developer.apple.com/documentation/swiftui/displaying-data-in-lists)

[[Apple 공식 문서 - Creating performant scrollable stacks]](https://developer.apple.com/documentation/swiftui/creating-performant-scrollable-stacks)

<br>

<br>

## List
먼저 List는 데이터의 행을 하나의 열로 정렬하여 표시할 수 있는, SwiftUI에서 기본적으로 지원하는 컨테이너입니다.<br>
List의 각 항목은 식별 가능한 데이터를 나타낼 수 있으며, 따라서 해당 데이터는 Identifiable하거나 id 매개변수로 고유한 값을 전달해야 합니다.<br>

```swift
struct Data: Identifiable {
    ...
}

var body: some View {
    List(datas) {
        // 셀 뷰
    }
}
```
따라서 위와 같이 Identifiable한 데이터를 활용해서 반복적으로 뷰를 보여줄 수 있습니다.<br>
이러한 List는 기존의 UIKit 컴포넌트를 감싼 구현입니다. <br>
List를 View Hierarchy에서 찍어보면..!<br>

![스크린샷 2025-06-11 오전 1 30 29](https://github.com/user-attachments/assets/2518a11f-b09f-462c-92d7-ac9a438960bd)

이렇게.. CollectionView를 일부 사용하는 듯한 구조를 가지고 있습니다!<br>

이러한 List는 listStyle을 통해 기본적으로 제공되는 다양한 스타일을 적용할 수 있습니다.<br>

![temp](https://github.com/user-attachments/assets/f10cb49f-2949-45d6-8aea-68688803fbfb)

또한 ForEach 문과 결합하여 List의 편집 기능을 사용할 수 있습니다.<br>
예를 들어 swipeActions, onDelete, onMove 등이 있습니다.<br>
따라서 다음과 같은 동작들을 편하게 지원해줍니다.<br>

동일한 코드를 ScrollView + LazyVStack에 적용하면 동작하지 않습니다.<br>

<br>

## LazyVStack
다음은 LazyVStack 입니다.<br>
LazyVStack의 경우 지연(lazy) 방식으로 동작하는 것이 핵심이며 기본적으로는 스크롤 기능이 없습니다.<br>
그래서 Scroll 기능 없이 그냥 LazyVStack만 사용하면 고정된 상태로 표시됩니다.<br>

<br>

## 비교

```swift
ForEach(1...500, id: \.self) {
  CustomView(text: String($0))
}

struct CustomView: View {
    var body: some View {
        HStack {
            Image("wallpaper")
            
            Text(text)
                .padding(10)
        }
    }
}
```

위와 같이 반복되는 뷰를 표시할 때 일반 VStack을 사용하면 500개의 View가 한꺼번에 로드됩니다. <br>

![스크린샷 2025-06-11 오전 3 59 55](https://github.com/user-attachments/assets/55864fc0-ffa3-442c-8957-3e95d0d4d4d2)

![스크린샷 2025-06-11 오전 2 32 47](https://github.com/user-attachments/assets/b85c8e82-8e1e-4f8a-95fd-d5b64bdc2cfb)

스크롤을 해도 메모리 사용량에는 변동이 거의 없고 일정하게 유지됩니다.<br>

ScrollView 없이 LazyVStack을 쓰면 어떻게 될까요?<br>

<img src="https://github.com/user-attachments/assets/c27f2aef-cf36-4c50-b1ad-ec1ae804aecc" width=400>

![스크린샷 2025-06-11 오전 4 14 30](https://github.com/user-attachments/assets/1d1cfa0c-f9a9-4150-99c5-8439d5c1e405)

이렇게 화면에 일부 보여지는 253번째 셀까지 뷰가 생성됩니다. (레이아웃 높이를 계산하기 위한 것 같습니다.)<br>

![스크린샷 2025-06-11 오전 2 35 35](https://github.com/user-attachments/assets/8a92ab10-eb1e-4eba-96bc-0a398bc4ecad)

이전처럼 뷰가 모두 로드되지는 않으므로 메모리 사용량이 줄어든 모습이네요.<br>

이제 ScrollView + LazyVStack 조합을 사용해 보겠습니다.<br>

<img src="https://github.com/user-attachments/assets/2a9730c8-b367-45b7-8890-c4ba08d1fe16" width=400>

![스크린샷 2025-06-11 오전 4 15 11](https://github.com/user-attachments/assets/217128c7-e215-4f3f-9d7a-a810f0210399)

이번에는 화면에 보여지는 4번 뷰까지만 뷰가 생성되었습니다.<br>

![스크린샷 2025-06-11 오전 2 59 25](https://github.com/user-attachments/assets/86dc9c6d-188c-4d0e-ab98-44b9923276aa)

사용하는 메모리의 양은 비슷합니다. <br>
앞서 LazyVStack에서 init이 여러번 불린 것은 레이아웃 때문이라고 예측할 수 있겠네요..!<br>

이러한 LazyVStack은 List보다 좀 더 유연한 커스터마이징이 가능하다는 장점이 있습니다.<br>

![Jun-11-2025 03-46-38](https://github.com/user-attachments/assets/a771f760-18ea-40df-ab1e-29f0dff423f5)

ScrollView를 적용한 LazyVStack을 살펴보면 이렇게 init과 deinit이 번갈아가며 나타납니다.<br>
즉 뷰를 필요한 시점에 로드하고, 필요 없어지는 시점에는 언로드를 진행합니다.<br>

저는 LazyVStack과 List의 주요 차이는 셀 재사용에 있다고 생각했는데요..!<br>

https://stackoverflow.com/questions/77142272/list-and-lazyvstack-performance-and-reusability

그러던 중에 위 글을 보게 되었고, 메모리 실험을 직접 해보게 되었습니다....<br>

iOS17에서의 결과는 어떨까요?<br>

<img src="https://github.com/user-attachments/assets/63056336-82a3-46a9-be51-0b65bedfa996" width=300>

<img src="https://github.com/user-attachments/assets/834cc121-4879-4112-a2a4-96a7e9375ba0" width=300>

List와 LazyVStack 둘 다 인스턴스가 1000개라고 나옵니다. 😧<br>
분명히 deinit은 호출되었는데 말이죠..!<br>

LazyVStack을 사용한 경우에는 이처럼 DummyClass가 메모리에 남아있는 것을 확인할 수 있었습니다.<br>

![스크린샷 2025-06-11 오후 12 26 34](https://github.com/user-attachments/assets/3e7a0e43-6dff-4c84-9f32-6384ac7b5643)

반대로 List의 경우 DummyClass 관련된 로그는 보이지 않았습니다. (이런저런 테스트를 진행해 보았지만 이유를 파악하지는 못했습니다. 🥹)<br>
대신 UICollectionView 관련 인스턴스들이 List에서만 존재하는 것으로 보아 내부적으로 이를 사용하고 있다는 점만 확실히 파악할 수 있었네요..<br>

![스크린샷 2025-06-11 오후 12 28 52](https://github.com/user-attachments/assets/a06c959f-ed8a-4718-a924-2d1774b35e75)

<br>

iOS18에서는 어떨까요?!<br>

<img src="https://github.com/user-attachments/assets/db1ccaf9-9f17-4d05-9f45-b51ceca30f8e" width=300>

먼저 List를 보면 이처럼 인스턴스 수에 변화가 생겼습니다. (메모리 해제가 적시에 이루어지고 있다는 뜻 같아요)<br>

![스크린샷 2025-06-11 오후 12 34 55](https://github.com/user-attachments/assets/e22c00a2-4ae4-4928-bfac-711fea90e4f7)

그리고 iOS17에서는 없었던 CALayer가 보입니다.<br>
이게 셀에서 그리는 것과 연관이 있는 것 같은데 확실하지 않네요 😓<br>
찾아보면 label 등 SwiftUI 뷰의 텍스트/레이아웃을 Core Graphics로 그리는 Internal View라고 합니다.<br>

![스크린샷 2025-06-11 오후 12 56 57](https://github.com/user-attachments/assets/5485dcbf-aea9-4e0c-b255-d2141823cb7e)

기존과 동일하게 CollectionView 관련된 인스턴스가 존재합니다.<br>

<img src="https://github.com/user-attachments/assets/207ec615-39e6-4203-8069-1cda4322e771" width=300>

LazyVStack을 사용하면 List와 동일하게 줄어든 모습입니다.<br>

![스크린샷 2025-06-11 오후 12 43 11](https://github.com/user-attachments/assets/5b64fd36-8a3d-4379-a475-3de212bc6add)

살아있는 인스턴스의 수는 더 적은데 할당된 횟수는 LazyVStack이 훨씬 많네요.<br>

<br>

신기한 것은...<br>

![스크린샷 2025-06-11 오후 1 20 22](https://github.com/user-attachments/assets/fe8352fc-fc7e-4b37-aef2-c064f38e556e)

이렇게 Call Tree를 따라가면 List의 경우 내부적으로 익숙한 UICollectionView의 dequeueReusableCellWithReuseIdentifier가 등장합니다.<br>
이는 예측된 동작이었는데요..!<br>

![스크린샷 2025-06-11 오후 1 23 15](https://github.com/user-attachments/assets/b70795ae-86a9-4ca8-a767-0f49773dfd0e)

LazyVStack을 살펴보면 LazyLayoutViewCache라는 것이 있고 여기서 재사용 가능한 뷰를 찾는 동작이 보입니다.<br>
여기서 View의 reuseIdentifier를 사용하고 있고 이는 해시 값으로 분석을 하는 것 같네요.<br>
즉 LazyVStack도 내부적으로는 재사용 최적화가 이루어지고 있는 것 같습니다.<br>


<br>

## 마무리..?
결국 iOS17 -> iOS18로 넘어오면서 메모리 최적화 쪽으로 어떤 변화가 있었던 것 같습니다.<br>
다른 자료를 찾아봤을 때, List가 LazyVStack보다는 유지하는 DummyClass 인스턴스의 수가 적었습니다.<br>
LazyVStack도 1000개를 유지하지 않는 것을 보면 최적화적인 면에서 화면 밖의 뷰는 버려지는 것 같습니다.<br>
iOS18에서는 둘 다 내부적으로 재사용 최적화도 진행되고 있습니다.<br>
하지만.. 결국 View를 구성하고 테스트를 하면서 제대로 비교를 해봐야 할 것 같네요 😅<br>

