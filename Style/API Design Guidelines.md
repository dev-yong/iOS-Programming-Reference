# API Design Guidelines

## Fundamentals

- 가장 중요한 것은 **사용 시점에서의 명료성**입니다.

- **명료성은 간결성보다 더 중요**합니다

- 모든 선언문에 **Documentation Comments을 작성**해야합니다. (참고 : [Markup Formatting Reference](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_markup_formatting_ref/))

  > 간단한 용어로 API 기능을 설명하지 못한다면, **API 설계에 문제가 있을 가능성이 높습니다.**

## Naming

### Promote Clear Usage

- **모호성을 피하기 위하여 필요한 단어를 포함**합니다.

- ```swift
  extension List {
    public mutating func remove(at position: Index) -> Element
  }
  
  ❌ employees.remove(x) // x를 제거해라? x위치의 것을 지워라? 모호함.
  ✅ employees.remove(at: x) //x위치의 것을 지워라.
  ```

- **불필요한 단어는 생략**합니다.

- ```swift
  ❌ 단순히 타입 정보를 반복하는 단어는 생략
  public mutating func removeElement(member: Element) -> Element?
  allViews.removeElement(cancelButton)
  
  ✅
  public mutating func remove(member: Element) -> Element?
  allViews.remove(cancelButton) // clearer
  ```

- Type constraints가 아니라, **''역할''에 따라 variable, parameter, associated type의 이름을 정합니다.**

- ```swift
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

  만약, associated type이 protocol contraint에 매우 밀접하게 결합되어 protocol 이름이 그 역할인 경우, 해당 protocol 이름에 `Protocol`을 붙임으로써 충돌을 피합니다.

  ```swift
  protocol Sequence {
      associatedtype Iterator : IteratorProtocol
  }
  
  protocol IteratorProtocol { ... }
  ```

  > The `IteratorProtocol` protocol is tightly linked with the `Sequence` protocol. Sequences provide access to their elements by creating an iterator, which keeps track of its iteration process and returns one element at a time as it advances through the sequence.

- Parameter의 역할을 명확히 하도록, **취약한 type 정보를 보완**합니다.

- ```swift
  ❌ 
  func add(_ observer: NSObject, for keyPath: String)
  grid.add(self, for: graphics) // 사용하는 곳이 모호합니다.
  
  ✅ 약하게 정의된 parameter의 앞에 역할을 설명하는 명사를 붙입니다.
  func addObserver(_ observer: NSObject, forKeyPath path: String)
  grid.addObserver(self, forKeyPath: graphics) // clear
  ```

### Strive for Fluent Usage

- method와 function을 **사용하는 곳에서 method와 function이 영어 문법 구문의 형태**가 되는 것을 선호합니다.

- ```swift
  ❌ x.insert(y, position: z)
  ✅ x.insert(y, at: z)          //“x, y를 z에 삽입해라.”
  
  ❌ x.subViews(color: y)
  ✅ x.subViews(havingColor: y)  //“x, 색상 y을 갖는subviews를 반환해라.”
  
  ❌ x.nounCapitalize()
  ✅ x.capitalizingNouns()       //“x, 명사를 대문자화해라”
  ```

  <u>getter</u>

- **factory methods는 “make”로 시작**합니다. 

- ```swift
  x.makeIterator()
  ```

  <u>factory method와 factory method pattern?</u>

- **사이드 이펙트에 따라 함수와 메소드 이름을 지정**합니다.

  - 사이드 이펙트가 없는 것은 명사구로 읽어야 합니다.

    ```swift
    x.distance(to: y)
    ```

  - 사이드 이펙트가 있는 것은 반드시 동사 구문으로 읽어야 합니다. 

  - ```swift
    print(x)
    x.sort()
    x.append(y).
    ```

  - **Mutating/nonmutating** 메소드 쌍을 **일관되게 이름을 지정**합니다.

    - 동사로 자연스럽게 설명이 되는 경우, nonmutating에 ''-ed" 혹은 "-ing" 접미사를 적용합니다.

      | Mutating      | Nonmutating          |
      | ------------- | -------------------- |
      | `x.sort()`    | `z = x.sorted()`     |
      | `x.append(y)` | `z = x.appending(y)` |

    - 명사로 자연스럽게 설명이 되는 경우, nonmutating에 "form" 접두사를 적용합니다.

      | Nonmutating          | Mutating              |
      | -------------------- | --------------------- |
      | `x = y.union(z)`     | `y.formUnion(z)`      |
      | `j = c.successor(i)` | `c.formSuccessor(&i)` |

- **Boolean method와 properties**는 nonmutating일 경우 **receiver에 관한 주장으로 읽어야합니다**.  

  > **Uses of Boolean methods and properties should read as assertions about the receiver** when the use is nonmutating
  >
  > 문장 해석이 매끄럽지 않다.

- ```swift
  //Collection
  x.isEmpty					//x가 비어있는가?
  
  //SKNode
  node1.intersects(node2)		//node1이 node2와 교차하는가?
  ```

- **무언가를 설명하는 프로토콜은 명사로 읽어야 합니다.** (e.g. `Collection`)

- **Capability를 설명하는 프로토콜은 able, ible 또는 ing 접미사를 사용하여 이름을 지정해야 합니다.**(e.g. `Equatable`, `ProgressReporting`).

## Conventions

- **메소드는** 같은 기본 의미를 공유할 때 또는 구분된 도메인에서 수행할 때 **기본 이름을 공유할 수 있습니다.**

  - ```swift
    ❌ 두 index는 다른 의미를 갖기 때문에, 서로 다른 이름이어야합니다.
    extension Database {
      /// Rebuilds the database's search index
      func index() { ... }
    
      /// Returns the `n`th row in the given table.
      func index(_ n: Int, inTable: TableID) -> TableRow { ... }
    }
    ```

    ```swift
    ❌ "return type에 대한 overloading"은 타입 추론의 모호성을 일으킵니다.
    extension Box {
      /// Returns the `Int` stored in `self`, if any, and
      /// `nil` otherwise.
      func value() -> Int? { ... }
    
      /// Returns the `String` stored in `self`, if any, and
      /// `nil` otherwise.
      func value() -> String? { ... }
    }
    ```

# Reference

- https://swift.org/documentation/api-design-guidelines/
- https://minsone.github.io/swift-internals/api-design-guidelines/?utm_source=soojinro&utm_medium=referral
- https://soojin.ro/blog/swift-api-design-guidelines-abbr
- https://soojin.ro/blog/english-for-developers-swift
- https://soojin.ro/blog/naming-boolean-variables

