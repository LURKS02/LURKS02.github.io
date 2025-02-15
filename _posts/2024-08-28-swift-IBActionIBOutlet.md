---
layout: post
title: '[스토리보드 활용 - 1] IBAction과 IBOutlet에 대하여'
categories: swift
---

IBAction과 IBOutlet은 무엇일까요?<br>
평소에 코드베이스로만 작업하다 보니 가장 기본적인 IBAction / IBOutlet 마저 모르고 있다는 생각이 들었습니다. 😓<br>

### 참조

[Hacking with Swift: What is an IBOutlet?](https://www.hackingwithswift.com/example-code/xcode/what-is-an-iboutlet)

[How to use Swift's @IBAction](https://reintech.io/blog/mastering-swifts-ibaction)

[Apple 공식 문서: awakeFromNib()](https://developer.apple.com/documentation/objectivec/nsobject/1402907-awakefromnib)

<br>

## Interface builder
먼저 인터페이스 빌더의 개념부터 알아보겠습니다.<br>

**인터페이스 빌더**란, 코드를 직접 작성하지 않고도 애플리케이션 UI를 디자인할 수 있게 해주는 툴입니다.<br>

스토리보드, XIB 등을 활용하여 드래그 앤 드롭 방식으로 UI를 구성하는 것이 인터페이스 빌더가 제공하는 기능이라고 볼 수 있습니다.<br>

 <br>

## IBOutlet과 IBAction

위에서 언급했던 인터페이스 빌더를 통해 실제로 빌드되는 앱을 작성하려면, 인터페이스 빌더의 각 요소들이 어떤 프로퍼티, 메서드와 관련있는지 Xcode가 알도록 하는 것이 중요합니다.<br>

따라서 IBOutlet의 경우 인터페이스 빌더 컴포넌트와의 연결을 의미하고, IBAction은 해당 메서드가 인터페이스 빌더 스토리보드 파일의 어떤 액션과 연결되어 있음을 의미합니다. 즉, 코드와 제어할 필요가 있을 때 등록합니다.<br>

<br>

## 빌드 시 일어나는 일들
그럼 빌드했을 때 이것들은 대체 어떤 방식으로 동작하는 것일까요?<br>
일단 스토리보드 파일을 살펴보겠습니다.<br>

### 스토리보드 파일
사실 스토리보드 파일은 XML 파일입니다. 오른쪽 맨 위의 변환 버튼을 누르면 스토리보드에서 코드로 변경된 모습을 확인할 수 있습니다.<br>

![image](https://github.com/user-attachments/assets/023748ae-94e3-48b0-8c7d-c65f797dd0db)

코드를 읽어보면 연결해준 뷰컨트롤러와 색상 등 설정해준 여러 요소들이 포함되어 있습니다.<br>

![image](https://github.com/user-attachments/assets/ad35afc5-bb25-4c3d-8745-bd4d16efddaf)

이러한 스토리보드 파일에는 스토리보드 상의 뷰 컨트롤러 위치 정보까지 포함되어 있어 (결국 XML 코드로 스토리보드를 그리는 것이므로) 뷰 컨트롤러 위치를 조금만 옮겨도 스토리보드 파일이 변경사항을 가진다고 판단하게 됩니다.<br>

따라서 이런 이슈들 때문에 협업할 때 스토리보드를 사용하기가 까다롭다고 합니다.<br>

이제 IBOutlet을 찾아보겠습니다.<br>

![image](https://github.com/user-attachments/assets/2024c024-49f7-4dd1-97a2-d3c7b40d160f)

outlet이라는 이름으로 등록되어 있습니다.<br>

액션은 당연히 action이겠죠?<br>

![image](https://github.com/user-attachments/assets/dbed862d-4827-405e-8a35-3d37b87d619f)

위처럼 등록되어 있는 모습이 보입니다.<br>

스토리보드 상에서 버튼을 생성해서 뷰 컨트롤러에 끌어다 놓았을 때 바로 그 동작에서 해당 코드가 추가되는 것 같습니다.<br>

nextButtonTapped라는 IBAction의 이름도 설정되어 있고, 이벤트 타입도 지정되어 있네요.<br>

여기서 destination이라는 건 무엇일까요?<br>

![image](https://github.com/user-attachments/assets/d65aa7a8-5e50-4472-b720-2bedd5fcc5b6)

바로 뷰 컨트롤러의 id입니다.<br>

즉, 각 요소들은 id를 가지고 있고 액션은 해당하는 id의 뷰 컨트롤러 내에서 연결되었음을 의미한다고 생각할 수 있겠습니다.<br>

아마 빌드하면서 이 정보를 이용해서 실제 동작을 찾아가는 것이 아닐까 추측해볼 수 있겠네요. 🙂<br>

일단 XML 파일은 여기까지 살펴보고 빌드 시 어떻게 동작하는지 알아보겠습니다. <br>

<br>

### 빌드와 Nib
일단 빌드 후 빌드 파일을 확인해보겠습니다.<br>

![image](https://github.com/user-attachments/assets/7d13838b-5664-46af-81e9-a7f8f7fe5fba)

번들을 찾았습니다. 🥳<br>

이 안에 있는 storyboard 파일을 확인해 보겠습니다.<br>

![image](https://github.com/user-attachments/assets/dff8a435-7e2f-4f75-bc19-a778cb0a0dab)

이것은 nib으로 컴파일된 storyboard 파일입니다.<br>

Nib 파일이란 무엇일까요?<br>

### Nib이란?

빌드 과정에서 컴파일 된 스토리보드 파일로, 런타임에 로드되는 바이너리 파일입니다.<br>

이 파일은 앱이 실행되면 로드되어 메모리에 배치됩니다.<br>

바이너리 파일을 열어보면 어떤 내용이 있을까요?<br>

![image](https://github.com/user-attachments/assets/f09a73eb-0582-4ed6-83ed-c296d356a1de)

읽을 수 없는 내용이 잔뜩 들어 있습니다. 😅<br>
원래 컴파일된 바이너리 코드는 사람이 읽기 어려우니 무슨 말인지 모를 수 있습니다. <br>
이러한 Nib 파일은 언제 로드될까요? 이를 확인하기 위해 awakeFromNib() 메서드를 사용할 수 있습니다.<br>

### awakeFromNib 이란?

인터페이스 빌더로부터 / Nib 파일로부터 리시버가 로드된 뒤 서비스를 준비하는 시점을 말합니다. <br>
이 메시지가 전달될 때는 해당 객체의 outlet / action 이 모두 설정된 이후입니다. <br>
인스턴스화 과정에서 각 객체는 적합한 메서드로 초기화되는데, 객체를 인스턴스화/초기화한 뒤 해당 객체들의 outlet / action을 연결하게 됩니다. (이때 인스턴스 순서는 보장되지 않습니다.)
<br>

이렇게 연결이 완료된 이후에 awakeFromNib 메서드를 호출해주게 됩니다.<br>
직접 print를 해본 결과는 다음과 같습니다.<br>

탭바 컨트롤러에 포함된 vc1, vc2가 있다고 할 때 빌드를 하면 출력은 다음과 같습니다.<br>

![image](https://github.com/user-attachments/assets/271ea381-4466-4b7c-8965-25dd73a1f12f)

즉, 공식 문서의 내용처럼 스토리보드에 존재하는 객체들이 인스턴스화(초기화) 되고 outlet과 action을 연결한 뒤 awakeFromNib이 먼저 호출됩니다. 그리고 vc1을 표시해주어야 하므로 view가 로드되는 것을 확인할 수 있습니다.<br>

<br>

### 의문점
생각해보면 따로 코드와 연결해줄 필요 없이 스토리보드를 사용하면 뷰 컨트롤러 진입점도 설정할 수 있고, IBOutlet을 따로 지정해주지 않아도 되는 경우가 있는데요, 이런 일은 어떻게 가능한 것일까요? 🤔<br>

스토리보드를 작성할 때, ViewController는 기본적으로 고유한 ID를 가집니다.<br> 
이를 기반으로 스토리보드 자체에서 인스턴스화가 가능하고, 이에 엔트리 포인트를 지정하면 자동으로 Root View Controller로 설정해주는 것으로 예상됩니다. <br>

버튼 정보도 마찬가지로 스토리보드 파일 정보만으로 생성이 가능한 것 같네요. <br>

<br>

## 결론
결론적으로 IBOutlet과 IBAction의 목적은 스토리보드 요소를 코드와 연결하기 위한 것으로 보입니다.<br>
즉, 스토리보드와 코드를 상호작용하게 하여 이벤트에 반응하거나 특정한 제어를 원할 때 이러한 기능이 필요해지게 됩니다. <br>
