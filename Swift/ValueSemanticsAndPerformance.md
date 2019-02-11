# Value Semantics & Performance

## Overview

[Swift 성능 이해하기: Value 타입, Protocol과 스위프트의 성능 최적화 - 유용하님](https://academy.realm.io/kr/posts/letswift-swift-performance/) 의 글을 보고, 개인적으로 정리한 내용입니다.

[WWDC15. Protocol-Oriented Programming in Swift](https://developer.apple.com/videos/play/wwdc2015/408/)

[WWDC15. Building Better Apps with Value Types in Swift](https://developer.apple.com/videos/play/wwdc2015/414/)

[WWDC16. Protocol and Value Oriented Programming in UIKit Apps](https://developer.apple.com/videos/play/wwdc2016/419/)

를 보며 내용을 첨삭할 예정입니다.

## Table of Contents

- [Value Semantics](#Value Semantics)
- [Considerations for Performance](#Considerations-for-Performance)
  - [Memory Allocation](#Memory-Allocation)
  - [Reference Counting](#Reference-Counting)
  - [Method dispatch](#Method-dispatch)
  - [Swift Abstract Techniques and Performance](#Swift Abstract Techniques and Performance)
- [Reference](#Reference)

## Value Semantics

`struct`, `enum`, `tuple`

- 특징

  - **Heap을 사용하지 않습니다.** (only, stack!) 따라서 Reference Couting이 필요없습니다.

    > class의 경우, stack에 reference가, heap에 구조체가 할당됩니다.

    - 변수 할당 시 stack에 값 전체가 저장됩니다.
    - 다른 변수에 할당될 때, 값이 복사되어 영향을 미치지 않습니다.

    - Thread간 의도치 않은 공유로 부터 안전합니다.

- '값'에 의해 구분되므로 'Value'가 중요합니다. `Equatable`을 이용하여 동치를 나타낼 수 있습니다.

  ```swift
  protocol Equatable {
      func ==(lhs: Self, rhs: Self) -> Bool
  }
  ```



## Considerations for Performance

### Memory Allocation

**👍Stack** or **👎Heap**

- Heap 할당 시, thread safe해야 하므로 lock 등의 동기화 동작으로 인해 성능을 저하시킵니다.
- Dictionary의 key가 Value type으로 stack에서만 메모리를 할당해서 Heap 할당 오버헤드가 없습니다.

### Reference Counting

**👍No** or 👎**Yes**

- 변수 복사 시 마다 실행되는 등 매우 빈번하게 실행됩니다.
- Thread safe를 위하여 카운트를 atomic하게 증감해야합니다.

### Method dispatch

**👍Static** or 👎**Dynamic**

> static : method를 compile time에 호출하는가? 
>
> dynamic : method를 run time에 호출하는가?
>
> 컴파일 타임 - 링크 타임 - 런타임

- **static**

  - 컴파일 타임에 메서드 실제 코드 위치를 안다면 실행 중 찾는 과정 없이 그 주소로 점프 가능합니다.
  - Method Inlining이 가능합니다.

- **dynamic** 

  - 컴파일 타임에 알 수 없고, 런타임에만 파악 가능합니다.
  - 컴파일 최적화를 못하므로 속도 저하의 요소이빈다.

- ##### static dispatch로 강제하기

  - `final`, `private`을 사용하여 상속성을 끊습니다.
  - `dynamic` 키워드의 사용을 줄입니다.
  - objc 연동을 최소화합니다.
  - WMO(Whole Module Optimization)을 사용합니다.

### Swift Abstract Techniques and Performance

- `class` 

  - Reference Semantics가 필요할 때 사용합니다.
  - `final class `는 static Mehod Dispatch입니다.

- `struct`

  - Reference 타입이 있는 `struct`는 성능이 저하될 수 있습니다.

- `protocol`

  - Value semantric에서도 적용 가능합니다.

  - 값의 관리는 Existentail Container로 이루어지게 되는데,

    > Existentail Container : Protocol type의 실제 값을 넣고 관리하는 구조

    - 3 words 이하 : 새로운 Existential container에 전체가 복사됩니다.
    - 3 words 이상 : 새로운 Existential container를 생성하고, 값 전체가 새로운 Heap 할당 후 복사됩니다.(값들은 Heap에, 해당 reference는 Existential container에 저장됩니다.)

## Reference

- https://academy.realm.io/kr/posts/letswift-swift-performance/