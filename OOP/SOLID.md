# S.O.L.I.D

## Table of Contents

- [The Single Responsibility Principle](#The-Single-Responsibility-Principle)
  - [Responsibility](#Responsibility)
  - [SRP Example](#SRP-Example)
- [The Open Closed Principle](#The-Open-Closed-Principle)
  - [Primary attributes](#Primary-attributes)
  - [Abstraction](#Abstraction)
  - [Strategic Closure](#Strategic-Closure)
  - [OCP Example](#OCP-Example)
- [The Liskov Substitution Principle](#The-Liskov-Substitution-Principle)
- [The Interface Segregation Principle](#The-Interface-Segregation-Principle)
- [The Dependency Inversion Principle](#The-Dependency-Inversion-Principle)

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
    let driving = Driving()
    let maintenance = Maintenance()
    let convenience = Convenience()
    
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

- 확장에는 열려있다.(Open for Extension)
  - 모듈의 행동은 확장되어질 수 있습니다.
- 변경에는 닫혀있다.(Closed for Modification)
  - 모듈의 소스 코드는 침범되지 않는다.
  - 모듈에 대한 소스 코드를 변경하는 것은 아무도 허용되지 않는다.

#### Abstraction

- 추상화는 고정적이지만 행동의 제약이 없는 그룹을 나타냅니다.
  - 모듈은 고정적인 추상화를 의존하기 때문에, 수정에 대하여 닫혀 있을 수 있습니다.
  - 그러나, 모듈은 새로운 추상화에 대한 파생물을 만들어 냄으로써 확장될 수 있습니다.

#### Strategic Closure

- 명시적인 폐쇄를 위하여 추상화를 사용합니다.
- 폐쇄를 달성하기 위하여 "Data Driven' 접근법을 이용합니다.

> 아직은 잘 이해가 되지 않습니다..

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

##### 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.

## The Interface Segregation Principle

##### 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

## The Dependency Inversion Principle

##### 추상화에 의존해야지 구체화에 의존하면 안된다.

> 의존성 주입

## Reference

- https://github.com/ochococo/OOD-Principles-In-Swift
- https://clean-swift.com/single-responsibility-principle-for-class/
- https://marcosantadev.com/solid-principles-applied-swift/