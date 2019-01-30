# API Design Guidelines With Grammar

## Overview

함수명, 변수명들을 일정 규칙과 문법을 따라 만든다면, 이름만을 보고 그것들의 역할을 어느정도 유추가 가능할 것입니다. 

[Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)와 노수진님의 [Swift 개발자처럼 변수 이름 짓기](https://soojin.ro/blog/english-for-developers-swift), [Bool 변수 이름 제대로 짓기 위한 최소한의 영어 문법](https://soojin.ro/blog/naming-boolean-variables) 를 읽고 입맛대로 정리한 내용입니다. 개인적인 주관이 다수 섞여있습니다.



## Fundamentals

API를 디자인함에 있어 가장 중요한 것은 **"사용 시점"에서의 명료성**입니다. 이때, **명료성은 간결성보다 중요**합니다.

또한, 모든 선언문에는  **Documentation Comments을 작성**해줘야합니다. (참고 : [Markup Formatting Reference](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_markup_formatting_ref/))

> 간단한 용어로 API 기능을 설명하지 못한다면, **API 설계에 문제가 있을 가능성이 높습니다.**



## Naming

### Promote Clear Usage

- **모호성을 피하기 위하여(명료성을 위하여) 필요한 단어를 포함**합니다.

  ```swift
  extension List {
    public mutating func remove(at position: Index) -> Element
  }
  //위 함수만 있음을 가정할 때,
  ❌ employees.remove(x) // x를 제거해라? x위치의 것을 지워라? 모호함.
  ✅ employees.remove(at: x) //x위치의 것을 지워라.
  ```

- **불필요한 단어는 생략**합니다.

  > 단, 간결성을 위하여 명료성을 해치면 안됩니다.

  ```swift
  ❌ 단순히 타입 정보를 반복하는 단어는 생략
  public mutating func removeElement(member: Element) -> Element?
  allViews.removeElement(cancelButton) //cancelButton Element를 지워라.
  
  ✅
  public mutating func remove(member: Element) -> Element?
  allViews.remove(cancelButton) // cancelButton을 지워라.
  ```

  ```swift
  struct User {
  	❌ let userID: String	// User와 userID의 'user'가 중복되었습니다.
  	✅ let identifier: String	
  }
  ```

- Type constraints가 아니라, **''역할''에 따라 variable, parameter, associated type의 이름을 정합니다.**

  ```swift
  ❌ var string = "Hello"
  ✅ var greeting = "Hello"
  
  protocol ViewController {
  ❌  associatedtype ViewType : View
  ✅  associatedtype ContentView : View
  }
  
  class ProductionLine {
  ❌  func restock(from widgetFactory: WidgetFactory)
  ✅  func restock(from supplier: WidgetFactory)
  }
  ```

  만약, associated type이 protocol contraint에 매우 밀접하게 결합되어 protocol 이름이 그 역할인 경우, 해당 protocol 이름에 `Protocol`을 붙임으로써 충돌을 피합니다. e.g. `Iterator` + **`Protocol`**

  ```swift
  protocol Sequence {
      associatedtype Iterator : IteratorProtocol
  }
  
  protocol IteratorProtocol { ... }
  ```

  > The `IteratorProtocol` protocol is tightly linked with the `Sequence` protocol. Sequences provide access to their elements by creating an iterator, which keeps track of its iteration process and returns one element at a time as it advances through the sequence.

- Parameter의 역할을 명확히 하도록, **취약한 type 정보를 보완**합니다.

  > 간결성을 추구하다가 명료성을 해치면 안됩니다.

  ```swift
  ❌ 
  func add(_ observer: NSObject, for keyPath: String)
  grid.add(self, for: graphics) // 사용하는 곳이 모호합니다.
  
  ✅ 약하게 정의된 parameter의 앞에 역할을 설명하는 명사를 붙입니다.
  func addObserver(_ observer: NSObject, forKeyPath path: String)
  grid.addObserver(self, forKeyPath: graphics) // clear
  ```



### Strive for Fluent Usage

- method와 function을 **사용하는 곳에서 method와 function이 영어 문법 구문의 형태**가 되는 것을 선호합니다.

  ```swift
  ❌ x.insert(y, position: z)
  ✅ x.insert(y, at: z)          //“x, y를 z에 삽입해라.”
  
  ❌ x.subViews(color: y)
  ✅ x.subViews(havingColor: y)  //“x, 색상 y을 갖는subviews를 반환해라.”
  
  ❌ x.nounCapitalize()
  ✅ x.capitalizingNouns()       //“x, 명사를 대문자화해라”
  
  
  //UIView 조동사 + 동사원형
  func willRemoveSubview(_ subview: UIView)
  willRemoveSubview(y) //UIView에서 y라는 subView가 지워질 것이다.
  /*
  `func willRemove(_ subview: UIView)는 "Parameter의 역할을 명확히 하도록, 취약한 type 정보를 보완합니다."를 충족하지 않습니다.
  */
  ```

- **factory methods는 “make”로 시작**합니다. 

  [Factory Method?](#Factory-Method?) 

- ```
  x.makeIterator()
  ```

- **사이드 이펙트에 따라 함수와 메소드 이름을 지정**합니다.

  [Side Effect?](#Side-Effect?)

  - 사이드 이펙트가 없다(**Pure Function**)? **명사구**

    ```swift
    x.distance(to: y)
    ```

    swift는 getter가 존재하지 않고, 타입 이름(명사)로 시작하면 됩니다.

    ```swift
    func distance(from location: CLLocation) -> CLLocationDistance        
    ```

  - 사이드 이펙트가 있다? **동사구문**

    ```swift
    print(x)	// x를 출력해라. I/O가 있으므로 print도 Side Effect가 존재합니다.
    x.sort() 	// x를 정렬해라. x라는 값을 변경 시키기에 Side Effect가 존재합니다.
    x.append(y)	// x에 y를 덧붙여라. x라는 값을 변경 시키기에 Side Effect가 존재합니다.
    ```

  - **Mutating/nonmutating** 메소드 쌍을 **일관되게 이름을 지정**합니다.

    [Mutating, Non Mutating?](#Mutating,-Non-Mutating?)

    > mutating method는 종종 유사한 구문의 nonmutating method가 존재하지만, 
    >
    > mutating은 **인스턴스 자체(in-place)**를 업데이트합니다.
    >
    > nonmutating은 인스턴스 자체(in-place)를 업데이트하는 것 대신, **새로운 값을 반환**합니다.

    - 동사 
      - mutating : 동사
      - nonmutating : 동사 + -ed 또는 -ing

    | Mutating      | Nonmutating          |
    | ------------- | -------------------- |
    | `x.sort()`    | `z = x.sorted()`     |
    | `x.append(y)` | `z = x.appending(y)` |

    ```swift
    mutating func sort() 		//mutating, in-place
    func sorted() -> [Element]	//nonmutating, 새로운 배열을 반환
    ```

    - 명사
      - mutating : from + 명사
      - non mutating : 명사

    | Mutating  | Nonmutating   |
    | -------------------- | --------------------- |
    | `y.formUnion(z)` | `x = y.union(z)` |
    | `c.formSuccessor(i)` | `j = c.successor(i)` |

    ```swift
    mutating func formUnion<S>(_ other: S) where Element == S.Element, S : Sequence
    //mutating
    func union<S>(_ other: S) -> Set<Set<Element>.Element> where Element == S.Element, S : Sequence
    //non mutating
    ```

- `fetch`, `request`, `perfrom` 

  - `fetch` : 실패하지 않는 작업, 결과를 바로 반환하는 경우에 사용합니다.

  - ```swift
    //PHAsset - Photos Framework
    class func fetchAssets(withLocalIdentifiers identifiers: [String], options: PHFetchOptions?) -> PHFetchResult<PHAsset>
    ```

  - `request` : 실패할 수 있는 작업, 요청을 거절할 수 있는 경우에 사용합니다.

  - ```swift
    //CLLocationManager
    func requestAlwaysAuthorization() 
    // 사용자에 의하여 요청이 거절될 수도 있기 때문에 request를 사용합니다.
    //Requests permission to use location services whenever the app is running.
    ```

  - `perform` or `execute` : 작업의 단위가 closure나 requesst로 wrapping되어있는 경우에 사용합니다.

  - ```swift
    //RunLoop
    func perform(_ block: @escaping () -> Void)
    ```

- **Boolean method와 properties**는 nonmutating일 경우 **receiver에 대한 주장**으로 읽어야합니다.

  > **Uses of Boolean methods and properties should read as assertions about the receiver** when the use is nonmutating
  >
  > Bool 메서드나 프로퍼티 이름은 인스턴스에 대한 평서문처럼 읽혀야한다…??
  >
  > receiver가 가리키는 것이 무엇일까??

  ```swift
  //Collection
  x.isEmpty					//x가 비어있는가?
  
  //SKNode
  node1.intersects(node2)		//node1이 node2와 교차하는가?
  ```

  - 조동사 + 동사원형

    - can : "~할 수 있는가?"

      ```swift
      var canBecomeFirstResponder: Bool { get } //UIResponder: first responder가 될 수 있는가?
      ```

    - should, will : "~해야하는가?" or "~할 것인가?"

      ```swift
      var shouldRefreshRefetchedObjects: Bool { get set } //NSFetchRequest: 가져온 값을 refresh 할 것인가?
      ```

  - is +

    - 명사 : "(무엇)인가?" 

    ```swift
      func isDescendant(of view: UIView) -> Bool //UIView: "view의 자식인가?" 
    ```

    - ~ing : "~하는 중인가?"

    ```swift
      var isExecuting: Bool { get } //Operation: "오퍼레이션의 작업이 현재 실행 중인가?"
    ```

    - 형용사(자체)

    ```swift
      var isOpaque: Bool { get set } //UIView: "view가 불투명한가?"
    ```

    - 과거분사

    ```swift
      var isSelected: Bool { get set } //UIView: "view가 선택되어졌는가?"
      var isHidden: Bool { get set } //UiView: "view가 숨겨졌는가?
    ```

    - **단, 절대 is + 동사원형을 사용하면 안됩니다.**

- **무언가를 설명하는 프로토콜은 명사**로 읽어야 합니다. (e.g. `Collection`)

- **Capability를 설명하는 프로토콜은 able, ible 또는 ing 접미사**를 사용하여 이름을 지정해야 합니다. (e.g. `Equatable`, `ProgressReporting`).

------



#### Factory Method?

> https://en.wikipedia.org/wiki/Factory_method_pattern
>
> https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/ClassFactoryMethods/ClassFactoryMethods.html
>
> 아직은 잘 모르겠습니다. 공부가 필요합니다.

#### Side Effect?

> 사이드 이펙트는 단순히 부정적인 것을 뜻하지 않습니다.
>
> **함수의 실행이 외부에 영향을 끼치는 경우**를 사이드 이펙트라고 합니다.
>
> - 실행중에 어떤 객체를 접근해서 변화가 일어나는 행위(라이브러리 I/O, 객체 변경 등)
> - 함수 외부의 State 변화

#### Mutating, Non Mutating?

> mutating의 사전적 의미는 "변화시키다"입니다.
>
> `mutating` keyword는 Value Type(struct, enum 등)과 같은 **immutable한 것들을 muttable**하게 만들어줍니다.
>
> ```swift
> struct Point {
>     var x = 0.0, y = 0.0
>     func moveBy(x deltaX: Double, y deltaY: Double) {
>         x += deltaX
>         y += deltaY
>     }
> }
> var somePoint = Point(x: 1.0, y: 1.0)
> somePoint.moveBy(x: 2.0, y: 3.0)
> print("The point is now at (\(somePoint.x), \(somePoint.y))")
> // Prints "The point is now at (3.0, 4.0)"
> ```



## Reference

- https://swift.org/documentation/api-design-guidelines/
- https://minsone.github.io/swift-internals/api-design-guidelines/?utm_source=soojinro&utm_medium=referral
- https://soojin.ro/blog/english-for-developers-swift
- https://soojin.ro/blog/naming-boolean-variables
- https://soojin.ro/blog/swift-api-design-guidelines-abbr
- https://en.wikipedia.org/wiki/Side_effect_(computer_science)
- https://docs.swift.org/swift-book/LanguageGuide/Methods.html