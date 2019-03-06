# S.O.L.I.D

## Table of Contents

- [Bad Design](#Bad-Design)
  - [Rigidity](#Rigidity)
  - [Fragility](#Fragility)
  - [Immobility](#Immobility)

- [The Single Responsibility Principle](#The-Single-Responsibility-Principle)
  - [Responsibility](#Responsibility)
  - [SRP Example](#SRP-Example)
- [The Open Closed Principle](#The-Open-Closed-Principle)
  - [Primary attributes](#Primary-attributes)
  - [Abstraction](#Abstraction)
  - [Strategic Closure](#Strategic-Closure)
  - [OCP Example](#OCP-Example)
- [The Liskov Substitution Principle](#The-Liskov-Substitution-Principle)
  - [Design by Contract](#Design-by-Contract)
  - [LSP Example](#LSP-Example)
- [The Interface Segregation Principle](#The-Interface-Segregation-Principle)
  - [Example](#Example)
- [The Dependency Inversion Principle](#The-Dependency-Inversion-Principle)
  - [Inversion](#Inversion)
  - [Layering](#Layering)

## Bad Design

### Rigidity

- 경직, 딱딱함
- 변화들이 시스템의 많은 부분들에 영향을 끼치기 때문에 바꾸기 어렵습니다.

### Fragility

- 취약성
- 변경을 주었을 때, 시스템의 예상치 못한 부분이 손상됩니다.

### Immobility

- 고정
- 현재의 어플리케이션에서 분리될 수 없기 때문에, 다른 어플리케이션에서 재사용하기 쉽지 않습니다.

## The Single Responsibility Principle

### "A class should have only one reason to change"

> **"한 클래스는 하나의 책임만 갖는다."**

#### Responsibility

- SRP에서는 "변경될 이유(reason to change)"가 될 **책임(responsibility)**을 규정합니다.

  > "A class should have only one reason to change"를 직역하면
  >
  > "한 클래스는 오직 하나의 변경될 이유를 갖습니다." 입니다.
  >
  > 이는 곧, **한 클래스는 오직 하나의 책임을 갖는다.** 로 해석할 수 있습니다.

- 만약 클래스를 변경하는 요인이 하나 이상이라면, 이것은 즉 클래스가 하나 이상의 책임이 있는 것입니다.

#### SRP Example

밑의 10가지 기능들은 Car의 기능들입니다.

```swift
//😨 Violate SRP
class Car {
  func accelerate() {}
  func brake() {}
  func turnLeft() {}
  func turnRight() {}
  func addFuel() {}
  func changeOil() {}
  func rotateTires() {}
  func adjustDriverSeat() {}
  func turnOnAC() {}
  func playCD() {}
}
```

하지만, 3가지의 책임이 존재하고 있습니다. 

- 운전에 대한 책임
  - `accelerate()`, `brake()`, `turnLeft()`, `turnRight()`
- 유지 관리에 대한 책임
  - `addFuel()`, `changeOil()`, `rotateTires()`
- 편의 제공에 대한 책임
  - `adjustDriverSeat()`, `turnOnAC()`, `playCD()`

```swift
//운전을 담당하는 Driving
class Driving {
    func accelerate() {}
    func brake() {}
    func turnLeft() {}
    func turnRight() {}
}
//유지관리를 담당하는 Maintenance
class Maintenance {
    func addFuel() {}
    func changeOil() {}
    func rotateTires() {}
}
//편의 제공을 담당하는 Convenience
class Convenience {
    func adjustDriverSeat() {}
    func turnOnAC() {}
    func playCD() {}
}
//3가지의 책임들을 decouple할 수 있습니다.
//👍 Conform SRP
class Car {
    let driving: Driving 
    let maintenance: Maintenance
    let convenience: Convenience
    
    init(driving: Driving,
        maintenance: Maintenance,
        convenience: Convenience) {
        self.driving = driving
        self.maintenance = maintenance
        self.convenience = convenience
	}
    
    func accelerate() { driving.accelerate() }
    func brake() { driving.brake() }
    func turnLeft() { driving.turnLeft() }
    func turnRight() { driving.turnRight() }
    
    func addFuel() { maintenance.addFuel() }
    func changeOil() { maintenance.changeOil() }
    func rotateTires() { maintenance.rotateTires() }
    
    func adjustDriverSeat() { convenience.adjustDriverSeat() }
    func turnOnAC() { convenience.turnOnAC() }
    func playCD() { convenience.playCD() }
}
```

## The Open Closed Principle

### "You should be able to extend a classes behavior, without modifying it."

> class를 변경하지 않고 행동을 확장할 수 있어야합니다.
>
> ##### 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야한다.

#### Primary attributes

- **확장에는 열려있다**.(Open for Extension)
  - 모듈의 행동은 확장되어질 수 있습니다.
- **변경에는 닫혀있다.**(Closed for Modification)
  - 모듈의 소스 코드는 침범되지 않는다.
  - 모듈에 대한 소스 코드를 변경하는 것은 아무도 허용되지 않는다.

#### Abstraction

- 추상화는 고정적이지만 행동의 제약이 없는 그룹을 나타냅니다.
  - 모듈은 고정적인 추상화를 의존하기 때문에, 수정에 대하여 닫혀 있을 수 있습니다.
  - 그러나, 모듈은 새로운 추상화에 대한 파생물을 만들어 냄으로써 확장될 수 있습니다.

#### Strategic Closure

- 명시적인 폐쇄를 위하여 추상화를 사용합니다.
- 폐쇄를 달성하기 위하여 "Data Driven' 접근법을 이용합니다.

#### OCP Example

 `MortorCycle` 과 SRP에 등장한 `Car`를 가속화하는 함수가 존재합니다.

```swift
func accelateAll(cars: [Car]) {
    cars.forEach{ $0.accelerate() }
}

class MotorCycle {
    let driving = Driving()
    let maintenance = Maintenance()
    
    func accelerate() { driving.accelerate() }
    func brake() { driving.brake() }
    func turnLeft() { driving.turnLeft() }
    func turnRight() { driving.turnRight() }
    
    func addFuel() { maintenance.addFuel() }
    func changeOil() { maintenance.changeOil() }
    func rotateTires() { maintenance.rotateTires() }
}

//😨 Violate OCP
func accelateAll(cars: [Car], motorCycles: [MotorCycle]) {
    cars.forEach { $0.accelerate() }
    motorCycles.forEach { $0.accelerate() }
}
```

`accelateAll(cars: )` 함수는 또다른 탈것(e.g. Bike)이 존재할 경우, 존재하는 함수 자체를 변경시켜야하기 때문에 변경에 닫혀있지 않습니다. 즉, OCP를 만족시키지 못합니다.

OCP를 만족시키기 위하여 추상적인 `Drivable` 를 생성하고, `Car` 와 `MotorCycle` 을 abstract의 파생물로 만들어줍니다.

``` swift
protocol Drivable {
    func accelerate()
    func brake()
    func turnLeft()
    func turnRight()
}

class Car: Drivable {
    ...
}

class MotorCycle: Drivable {
    ...
}

//👍 Conform OCP
func accelateAll(drivables: [Drivable]) {
    drivables.forEach { $0.accelerate() }
}
```

`Drivable` 를 이용하여 모듈이 확장될 수 있으며, 변경에 대하여 폐쇄적이게 되었습니다.

## The Liskov Substitution Principle

### "Derived classes must be substitutable for their base classes."

> 파생 클래스는 Base 클래스로 치환될 수 있어야합니다.
>
> **프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.**

#### Design by Contract

- Class의 Method는 **Precondition** 와 **Postcondition** 을 선언합니다.
  - Method를 수행하려면 Precondition이 참이어야합니다.
  - Precondition이 참이라면, Method는 Postcondition이 참임을 보증합니다.
- 파생물에서 루틴을 재정의할 때, Precondition을 더 약한 것에 의해서만 바꿀 수 있고
  Postcondition을 더 강한 것에 의해서만 바꿀 수 있습니다. 
  - Base class interface 를 통하여 객체를 사용할 때, 사용자는 오직 Base Class의 Precondition과 Postcondition에 대해서만 압니다.
  - 그러므로, **파생된 객체는 사용자가 Base Class가 요구하는 것보다 더욱 강한 Precondition을 따를 것으로 예상해서는 안됩니다**.
  - 또한, **파생된 객체는 Base Class의 Postcondition들을 모두 부합해야합니다**.
    - 즉, 모든 Behavior과 Output은 Base Class에서 설정된 어떤 제약조건들에도 위반되지 않아야합니다.
  - 기본 클래스의 사용자는 파생 클래스의 출력에 대하여 혼동되지 않아야합니다.

#### LSP Example

- Precondition

```swift
class Handler {
    func save(string: String) {
        // Save string in the Cloud
    }
}

class FilteredHandler: Handler {
    override func save(string: String) {
        //😨 Violate LSP - Precondition
        guard string.count > 5 else { return } // Precondition
        super.save(string: string)
    }
}
```

Base Class인  `Handler` 를 사용하는 Client는 파생 Class인 `FilteredHandler` 와 동일한 로직을 에상하고 사용할 것입니다.

하지만, 파생 Class인 `FilteredHandler` 에는 Base Class인 `Handler` 보다 더욱 강한 Precondition을 따르고 있어, LSP를 위반하고 있습니다.

```swift
class Handler {
    func save(string: String, minChars: Int = 0) {
        guard string.characters.count >= minChars else { return }
        // Save string in the Cloud
    }
}

class FilteredHandler: Handler {
    override func save(string: String) {
        super.save(string: string)
    }
}
```

Base Class인 `Handler` 에 Precondition을 설정하여줍니다.

- Postcondition

정사각형 `Square` 는 직사각형 `Rectangle` 의 일종으로 볼 수 있으며, `Rectangle` 이 될 수 있습니다. 

하지만, `Square` 의 **행동**이 `Rectangle` 의 **행동**과 일치하지 않기 때문에, `Square` 는 명백히 `Rectangle` 이 아닙니다.

행동적으로, `Square` 은 `Rectangle` 이 아니며, 소프트웨어에서 중요한 것은 **행동(Behavior)** 입니다

```swift
class Rectangle {
    var width: Float = 0
    var length: Float = 0
 
    var area: Float {
        return width * length
    }
}
 
class Square: Rectangle {
    override var width: Float {
        didSet {
            //😨 Violate LSP - Postcondition
            length = width
        }
    }
}
```

파생 Class인 `Square` 는 Base Class `Rectangle` 의 모든 Postcondition을 부합하지 않기 때문에 LSP를 위반하였습니다.

```swift
func printArea(of rectangle: Rectangle) {
    rectangle.length = 5
    rectangle.width = 2
    print(rectangle.area)
}

let rectangle = Rectangle()
printArea(of: rectangle) // 10
// -------------------------------
let square = Square()
printArea(of: square) // 4
```

`Square` 에 대하여  `printArea(of: )` 를 수행하면,  `width * length` 의 결과 값이 10이 출력되어야하지만 Postcondition이 추가되어 `lengt` 를 `width` 의 값으로 변경해버려 4가 출력됩니다.

`protocol` 을 이용하여 LCP를 따릅니다.

```swift
//👍 Conform OCP
protocol Polygon {
    var area: Float { get }
}
 
class Rectangle: Polygon {
 
    private let width: Float
    private let length: Float
 
    init(width: Float, length: Float) {
        self.width = width
        self.length = length
    }
 
    var area: Float {
        return width * length
    }
}
 
class Square: Polygon {
 
    private let side: Float
 
    init(side: Float) {
        self.side = side
    }
 
    var area: Float {
        return pow(side, 2)
    }
}
```

## The Interface Segregation Principle

### "Make fine grained interfaces that are client specific."

> 클라이언트별로 세분화된 interface를 만듭니다.
>
> **특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.**

- "Fat" 인터페이스를 갖는 클래스는 인터페이스가 응집도가 낮은 클래스입니다.
  - 클래스의 인터페이스는 맴버함수의 그룹으로 나눌 수 있습니다.

#### Example

- Fat interface (Protocol)

```swift
protocol GestureProtocol {
    func didTap()
    func didDoubleTap()
    func didLongPress()
}

class Button: GestureProtocol {
    func didTap() {
        // send tap action
    }
 
    func didDoubleTap() {
        // send double tap action
    }
 
    func didLongPress() {
        // send long press action
    }
}
```

만일, `Button` 이 `didTap()` 만이 필요하다면, 나머지의 것들은 필요가 없습니다.

이럴 경우, 여러 개의 protocol로 쪼개어 줍니다.

```swift
protocol TapProtocol {
    func didTap()
}
 
protocol DoubleTapProtocol {
    func didDoubleTap()
}
 
protocol LongPressProtocol {
    func didLongPress()
}
 
class SuperButton: TapProtocol, DoubleTapProtocol, LongPressProtocol {
    func didTap() {
        // send tap action
    }
 
    func didDoubleTap() {
        // send double tap action
    }
 
    func didLongPress() {
        // send long press action
    }
}
 
class PoorButton: TapProtocol {
    func didTap() {
        // send tap action
    }
}
```

- Fat interface (Class)

```swift
class Video {
    var title: String = "My Video"
    var description: String = "This is a beautiful video"
    var author: String = "Marco Santarossa"
    var url: String = "https://marcosantadev.com/my_video"
    var duration: Int = 60
    var created: Date = Date()
    var update: Date = Date()
}

func play(video: Video) {
    // load the player UI
    // load the content at video.url
    // add video.title to the player UI title
    // update the player scrubber with video.duration
}
```

`Video` 클래스를 구현하였고, `play(video:)` 를 이용하여 재생하고자 합니다. 이 때, `play(video:)` 는 `title`, `description`, `duration` 정보만이 필요합니다. Protocol을 이용하여 이 구조 또한 나누어줍니다.

```swift
protocol Playable {
    var title: String { get }
    var url: String { get }
    var duration: Int { get }
}
 
class Video: Playable {
    var title: String = "My Video"
    var description: String = "This is a beautiful video"
    var author: String = "Marco Santarossa"
    var url: String = "https://marcosantadev.com/my_video"
    var duration: Int = 60
    var created: Date = Date()
    var update: Date = Date()
}
 
func play(video: Playable) {
    // load the player UI
    // load the content at video.url
    // add video.title to the player UI title
    // update
}
```

## The Dependency Inversion Principle

### "Depend on abstractions, not on concretions."

> ##### 추상화에 의존해야지 구체화에 의존하면 안된다.

- 상위 계층의 모듈은 하위 계층의 모듈에 의존하면 안된다. 상위 계층이던 하위 계층이던 추상화에 의존해야합니다.
- 추상화는 세부사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야합니다.

### Inversion

- 전통적인 소프트웨어 개발방법은 상위 계층이 하위 계층에 의존하고, 추상화가 세부사항에 의존하였습니다.
- 잘 설계된 객체 지향 프로그램의 종속 구조는 일반적으로 전통적인 절차적 방법에서 비롯되는 종속 구조에 관해서 "**역전(Inversion)**"됩니다.
  - 잘 설계된 객체 지향 프로그램은 상위 계층이 하위 계층에 의존하지 않고,
  - 세부사항이 추상화에 의존합니다.

### Layering

- 잘 구조화된 객체 지향 아키텍처들은 명확하게 정의된 계층을 가지고 있으며, 각 계층은 잘 정의되고 통제된 인터페이스임에도 불구하고 일관된 서비스 집합을 제공합니다.

## Reference

- [Single Responsibility Principle](https://drive.google.com/file/d/0ByOwmqah_nuGNHEtcU5OekdDMkk/view)
- [Open-Closed Principle](https://drive.google.com/file/d/0BwhCYaYDn8EgN2M5MTkwM2EtNWFkZC00ZTI3LWFjZTUtNTFhZGZiYmUzODc1/view)
- [Liskov Substitution Principle](https://drive.google.com/file/d/0BwhCYaYDn8EgNzAzZjA5ZmItNjU3NS00MzQ5LTkwYjMtMDJhNDU5ZTM0MTlh/view)
- [Interface Segregation Principle](https://drive.google.com/file/d/0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi/view)
- [Dependency Inversion Principle](https://drive.google.com/file/d/0BwhCYaYDn8EgMjdlMWIzNGUtZTQ0NC00ZjQ5LTkwYzQtZjRhMDRlNTQ3ZGMz/view)

- https://github.com/ochococo/OOD-Principles-In-Swift
- https://clean-swift.com/single-responsibility-principle-for-class/
- https://marcosantadev.com/solid-principles-applied-swift/