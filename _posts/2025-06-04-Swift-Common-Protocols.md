---
layout: post
title: 'Swift의 프로토콜 - Hashable, Equatable, Comparable, Identifiable'
categories: swift
---


최근에 바빴던 탓에 오랫만에 글을 올리게 되었습니다 😅<br>
오늘은 평소에 자주 사용하던 Hashable, Equatable, Identifiable 같은 Swift의 기본 프로토콜들에 대해 좀 더 자세히 알아보겠습니다!<br>
막연하게 코드를 작성하고.. 안 붙이면 경고가 나오니까 대충 Hashable 붙여주고.. 이렇게 사용하면 안되겠죠..!<br>
그렇다면 이 프로토콜들은 어떤 역할을 하는지, 어떻게 사용해야 하는지 알아보겠습니다 🙂<br>


<br>


<br>

[참고]

[[Adopting Common Protocols]](https://developer.apple.com/documentation/swift/adopting-common-protocols)

[[Apple 공식 문서 - Equatable]](https://developer.apple.com/documentation/swift/equatable)

[[Apple 공식 문서 - Comparable]](https://developer.apple.com/documentation/swift/comparable)

[[Apple 공식 문서 - Identifiable]](https://developer.apple.com/documentation/swift/identifiable)

[[When and How to Use the Equatable and Identifiable Protocols in Swift]](https://khawerkhaliq.com/blog/swift-equatable-identifiable/)

<br>

<br>

## 서론
Swift에는 기본적으로 제공되는 다양한 프로토콜이 존재합니다. <br>
Equatable, Hashable, Comparable, Identifiable 같은 것들이 있죠! 🙂<br>
이러한 프로토콜을 사용하면 객체를 비교하거나 식별할 때 표준화된 형태를 사용해서 쉽게 처리할 수 있습니다.<br>
저도 프로젝트를 할 때 이러한 프로토콜을 많이 응용하는데요.. 막상 이들 간의 관계는 어떻고, 어떻게 사용해야 하는 것인지 막연한 부분이 있었습니다.<br>
그럼 각 프로토콜이 어떤 특징을 가지고 있는지 알아보겠습니다. 😊<br>

<br>

## Equatable
먼저 Equatable 입니다!<br>
이름에서도 알 수 있듯이 값의 동등성(Equality)을 비교할 수 있는 프로토콜입니다.<br>
Equatable을 준수하고 있는 타입은 동등 연산자(==)와 동등하지 않음 연산자(!=)를 사용하여 값이 동일한지 비교할 수 있습니다.<br>

![스크린샷 2025-06-03 오전 3 29 43](https://github.com/user-attachments/assets/7653d453-a27c-428a-8089-4db86623f483)

Equatable의 요구사항은 위처럼 == 연산자의 구현을 요구합니다.<br>
!=의 경우에는 ==의 결과를 부정하는 식으로 계산하게 됩니다.<br>

하지만... 모든 경우에 위 요구사항을 구현할 필요는 없습니다!<br>
바로 Equatable의 요구사항을 컴파일러가 자동 합성해주기 때문입니다.<br>
따라서 구조체의 모든 저장 프로퍼티가 Equatable 하거나, Enum의 연관값이 없거나, 연관값이 모두 Equatable한 경우에는 요구사항이 자동으로 합성됩니다.<br>

Swift에서 사용되는 기본 데이터 타입(Int, Bool, String 등)들은 대부분 Equatable을 준수합니다.<br>

![스크린샷 2025-06-03 오전 3 45 18](https://github.com/user-attachments/assets/6a9a83fa-3234-47f4-8bf1-86b781fb8065)

위처럼 말이죠..!<br>
따라서 기본적으로 Equatable한 타입들만 가져도 요구사항을 만족할 수 있습니다.<br>
하지만 그 외의 경우에는 자체적으로 == 연산자의 구현을 제공해야 합니다. <br>

또한 클래스는 모든 프로퍼티가 Equatable 하더라도, 자동 합성이 되지 않습니다.<br>
이는 클래스의 경우 프로퍼티 값과 관계 없이 고유한 식별성을 지니기 때문입니다. <br>
따라서 늘 == 연산자를 수동으로 구현하게 됩니다.<br>

Array의 contains와 같은 메서드는 Element가 Equatable할 때 제공됩니다.<br>
이렇게 Equatable을 채택하여 배열에서 특정 값을 찾거나 포함 여부를 검사하는 기본 메서드를 활용할 수 있습니다. 🙂<br>

<br>

## Comparable
Comparable은 값을 비교할 수 있게 해주는 프로토콜입니다.<br>
즉, 관계 연산자 <, <=, >, >= 로 값을 비교할 수 있습니다.<br>
Swift에서도 대부분의 기본 자료형은 값의 비교를 지원하고 있죠!<br>

```swift
protocol Comparable : Equatable
```
Comparable은 위와 같이 Equatable을 채택하고 있습니다.<br>
비교가 가능한 값이라면 당연히 값이 동일한지 판별이 가능해야 하겠죠?<br>
따라서 Comparable은 == 연산과 < 연산을 동시에 정의해야 합니다.<br>
이렇게 두 메서드만 정의해주면, >, >=, <= 의 연산은 Swift 표준 라이브러리에서 기본 구현을 제공합니다.<br>

참고로 Comparable은 기본 구현을 제공하지 않습니다.<br>
논리적인 비교 방식이 상황에 따라 달라지기 때문이죠!<br>
예를 들어, 어떤 타입이 여러 종류의 프로퍼티를 가진다면 어떻게 비교할지 명확하지 않은 경우가 있습니다. <br>
이렇게 Comparable을 채택하면 sorted와 같은 정렬, 비교 연산을 수행할 수 있습니다.<br>

그렇다면 String의 경우에는 값을 어떻게 비교할까요?<br>
바로 유니코드 스칼라 값을 기준으로 값을 정렬합니다.<br>
문자열은 여러 개의 유니코드 스칼라 값으로 이루어져 있고, 이들이 하나의 그래프 클러스터로 합쳐지면 Character 타입이 됩니다.<br>

하나 특이한 점은, 국제화에 따라 String 배열의 정렬 순서가 달라질 수 있다는 점인데요..!<br>
한번 예시를 들어보겠습니다. <br>

```swift
let a = "ä"
let b = "z"

let german = Locale(identifier: "de_DE")
let swedish = Locale(identifier: "sv_SE")

let germanResult = a.compare(b, locale: german)
let swedishResult = a.compare(b, locale: swedish)

print("독일어: \(germanResult)")
print("스웨덴어: \(swedishResult)")
```

이렇게... 동일한 문자에 대해 locale을 다르게 해서 비교를 해보겠습니다.<br>
결과는 어떨까요? 

<br>

<img src="https://github.com/user-attachments/assets/14c2c756-a8c8-4fde-adca-3555d761313f" width=500>

위 사진처럼 각 독일어 / 스웨덴어의 정렬 순서가 다르게 도출됩니다. 🤔<br>
독일어는 orderedAscending, 스웨덴어는 orderedDescending 이네요.<br>

따라서 독일어의 경우 ä가 z보다 앞에 오도록, 스웨덴어의 경우 ä는 z보다 뒤에 있도록 배치됩니다.<br>
즉, 각 나라마다 문자 체계에 따라 정렬 규칙이 달라질 수 있습니다.<br>
국제화를 하면서 정렬 기능을 구현할 때는 꼭 locale을 고려해야겠네요. 🙂<br>

<br>

## Hashable

대망의 Hashable 입니다!<br>
```swift
protocol Hashable: Equatable
```
Comparable과 마찬가지로 Hashable 또한 Equatable을 기본적으로 채택하고 있습니다.<br>
이 이유는 해시 충돌을 해결하기 위함입니다.<br>

Hashable을 채택하면 해당 타입을 해시 함수에 입력해서 정수 해시 값을 생성할 수 있도록 해줍니다. <br>
```swift
@frozen public struct Dictionary<Key, Value> where Key : Hashable { ... }

@frozen public struct Set<Element> where Element : Hashable { ... }
```
Set과 Dictionary의 구현을 보면 Set은 Element 자체에, Dictionary는 Key에 위와 같이 Hashable을 요구하고 있습니다.<br>
이는 Set과 Dictionary가 내부적으로 해시 테이블을 기반으로 동작하기 때문입니다. <br>
Set의 경우 중복되는 값을 찾기 위해 해시 값을 만들어 필터링을 빠르게 수행하고, Dictionary의 경우 마찬가지로 key 기반의 빠른 조회를 위해 해시 값을 활용합니다.<br>


기본적으로 값을 응용할 때 해시 값을 생성해서 비교를 하게 되는데, 이 과정에서 서로 다른 값이 동일한 해시 값을 가질 수 있습니다.<br>
이렇게 되면 두 객체가 실제로 동일한 객체인지 판별해야 하기 때문에, Equatable을 통해 이를 판단합니다.<br>
이때 같은(equal) 인스턴스는 동일한 해시값을 가져야 합니다. 그렇지 않으면 예상치 못한 동작이 발생할 수 있습니다.<br>

```swift
struct BrokenHash: Hashable {
    let id: Int

    static func == (lhs: BrokenHash, rhs: BrokenHash) -> Bool {
        return true
    }

    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }
}

let a = BrokenHash(id: 1)
let b = BrokenHash(id: 2)

let set: Set<BrokenHash> = [a, b]
```
예를 들어 위처럼 두 값의 해시값은 다르지만, 결국 동일한 값이라고 가정을 하면<br>
Set에 추가했을 때는 해시값을 기준으로 두 값은 다른 값으로 인식됩니다.<br>
같으면서 다르다는 것이 어색하게 느껴집니다. 🤔<br>

```swift
struct BrokenHash: Hashable {
    let id: Int

    static func == (lhs: BrokenHash, rhs: BrokenHash) -> Bool {
        return true
    }

    func hash(into hasher: inout Hasher) {
        hasher.combine(0)
    }
}

let a = BrokenHash(id: 1)
let b = BrokenHash(id: 2)

let set: Set<BrokenHash> = [a, b]

print("Set count:", set.count)
```
이렇게 두 값이 무조건 동일하고 동일한 해시값이 나온다고 가정했을 때<br>
결국 두 값은 하나의 동일한 값으로 인식됩니다.<br>

```swift
struct BrokenHash: Hashable {
    let id: Int

    static func == (lhs: BrokenHash, rhs: BrokenHash) -> Bool {
        return lhs.id == rhs.id
    }

    func hash(into hasher: inout Hasher) {
        hasher.combine(0)
    }
}

let a = BrokenHash(id: 1)
let b = BrokenHash(id: 2)

let set: Set<BrokenHash> = [a, b]
```

두 값의 해시값이 동일할 때는, Equatable을 보고 값의 같고 다름을 판단하게 됩니다.<br>

이러한 Hashable은 Swift 4.0까지 자동 합성이 없었기 때문에, 직접 hashValue를 구현해야 했습니다. <br>
예를 들어 모든 프로퍼티를 XOR 연산을 한 값을 hashValue로 사용했다고 합니다.<br>
이후 4.1에서 Hashable 적합성 자동 합성이 도입되었고, 4.2에서는 해시값을 생성하는 과정이 Hasher를 통해 추상화됩니다.<br>
즉 이전의 해시값 생성 연산이 hash(into:) 메서드를 통해 프로퍼티를 넘기는 방식으로 추상화 되었습니다. <br>
```swift
struct MyStruct: Hashable {
    let id: UUID
    let name: String
    let age: Int

    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }
}
```
이때 플러딩 공격을 막기 위해서 Hash에서 사용되는 키 값은 런타임에 무작위로 생성됩니다.<br>
따라서 Apple 공식 문서에도 이렇게 나와 있죠..!<br>
> Hash values are not guaranteed to be equal across different executions of your program. Do not save hash values to use during a future execution.

```swift
struct MyStruct: Hashable {
    let id: UUID
    let name: String
    let age: Int

    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }
}

...

let myStruct = MyStruct(name: "john", age: 10)
```

이렇게 동일한 프로퍼티로 생성한 값은 매번 해시값이 동일할 것 같지만 그렇지 않습니다.<br>
```
>>> -6345471484115811263
>>> -3207879799353082130
>>> 4822880755250253257
...
```
따라서 해시값을 어딘가에 저장해서 계속 사용하게 되면 문제가 생기겠죠..!<br>
이러한 부분은 잘 숙지해서 조심해야 할 듯 합니다. 😅<br>

<br>

## Identifiable
마지막으로 Identifiable 입니다.<br>
다른 프로토콜들에 비해 비교적 나중에 등장한 프로토콜인데요..!<br>
이 프로토콜은 식별 개념을 부여할 때 사용됩니다.<br>

그냥 id 프로퍼티를 두고 Equatable로 비교하는 것과 뭐가 다르지? 하는 생각이 들 수 있습니다. 😅<br>
Identifiable은 해당 타입에 객체의 정체성이 존재함을 명시합니다.<br>
따라서 SwiftUI에서는 정의한 식별성에 대해 자동으로 id를 추론하기도 합니다.<br>

```swift
@available(macOS 10.15, iOS 13.0, watchOS 6.0, tvOS 13.0, *)
public protocol Identifiable<ID> {
    associatedtype ID : Hashable

    var id: Self.ID { get }
}
```
Identifiable의 구현부를 보면 이렇게 Hashable한 ID를 요구하고 있습니다.<br>
Hashable한 ID를 통해 Set이나 Dictionary에도 사용할 수 있겠죠!<br>
값 기반 동등성을 사용하는 경우에도 Identifiable의 id를 활용하여 일관된 형태로 비교할 수 있습니다.<br>

```swift
@available(macOS 10.15, iOS 13.0, watchOS 6.0, tvOS 13.0, *)
extension Identifiable where Self : AnyObject {
    public var id: ObjectIdentifier { get }
}
```
클래스 타입의 경우 Idenfitiable의 기본 구현을 제공받으며, 이는 ObjectIdentifier를 사용해서 접근할 수 있습니다.<br>

```swift
struct MyStruct: Identifiable {
    var id: UUID
    let name: String
    let age: Int
}

class MyClass: Identifiable {
    let name: String
    let age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}
```
그래서 위처럼 작성하는 것이 가능하네요!<br>

```
>>> ObjectIdentifier(0x0000600000c675d0)
```
이 값은 객체 생명주기 동안 유지되는 값으로 클래스 타입과 메타 타입에게만 제공됩니다.<br>
Identifiable에서 제공하는 ObjectIdentifier의 값은 해당 인스턴스의 ObjectIdentifier를 구한 값과 동일합니다.<br>
```swift
print(">>> \(myClass.id)")
print(">>> \(ObjectIdentifier(myClass))")

// >>> ObjectIdentifier(0x0000600000c6d440)
// >>> ObjectIdentifier(0x0000600000c6d440)
```
이러한 ObjectIdenfitier는 객체 생명주기 동안 유효한 비교값으로 이는 단순히 객체의 메모리 주소를 래핑한 값입니다.<br>
```swift
extension ObjectIdentifier : Equatable {
    @inlinable public static func == (x: ObjectIdentifier, y: ObjectIdentifier) -> Bool
}

extension ObjectIdentifier : Comparable {
    @inlinable public static func < (lhs: ObjectIdentifier, rhs: ObjectIdentifier) -> Bool
}

extension ObjectIdentifier : Hashable {
    @inlinable public func hash(into hasher: inout Hasher)

    public var hashValue: Int { get }
}
```
ObjectIdentifier의 구현을 들여다보면 이 값은 Hashable하고, Comparable하며, Equatable 합니다.<br>
따라서 생성된 단일 인스턴스의 생명주기에 연결된 정체성의 의미를 가지며 클래스와 메타 타입에서는 이를 identifier로 활용할 수 있습니다.<br>

<br>

## 마치며
오늘은 Swift의 대표적인 프로토콜 4총사에 대해 알아보았습니다.<br>
어떤 경우에 어떤 프로토콜을 왜 써야 하는지 제대로 숙지하고 사용하는 개발자가 되도록 노력해야겠다는 생각이 들었습니다. 😊<br>
