# S.O.L.I.D

## Table of Contents

- [The Single Responsibility Principle](#The-Single-Responsibility-Principle)
- [The Open Closed Principle](#The-Open-Closed-Principle)
- [The Liskov Substitution Principle](#The-Liskov-Substitution-Principle)
- [The Interface Segregation Principle](#The-Interface-Segregation-Principle)
- [The Dependency Inversion Principle](#The-Dependency-Inversion-Principle)

## The Single Responsibility Principle

### **"A class should have only one reason to change"**

> **"한 클래스는 하나의 책임만 갖는다."**

#### Responsibility

- SRP에서는 "변경될 이유(reason to change)"가 될 **책임(responsibility)** 를 규정합니다.

  > "A class should have only one reason to change"를 직역하면
  >
  > "한 클래스는 오직 하나의 변경될 이유를 갖습니다." 입니다.
  >
  > 이는 곧, **한 클래스는 오직 하나의 책임을 갖는다.** 로 해석할 수 있습니다.

- 만약 클래스를 변경하는 요인이 하나 이상이라면, 이것은 즉 클래스가 하나 이상의 책임이 있는 것입니다.

```swift
//자동차는 아래와 같이 여러 책임을 갖게 됩니다.
class Car
{
  //운전을 할 수도 있고
  func accelerate() {}
  func brake() {}
  func turnLeft() {}
  func turnRight() {}
  //유지관리를 할 수도 있으며
  func addFuel() {}
  func changeOil() {}
  func rotateTires() {}
  //편의를 제공할 수도 있습니다.
  func adjustDriverSeat() {}
  func turnOnAC() {}
  func playCD() {}
}
```

```swift
//이 책임을 크게 나누면,
//운전을 담당하는 Driving
class Driving
{
  func accelerate() {}
  func brake() {}
  func turnLeft() {}
  func turnRight() {}
}
//유지관리를 담당하는 Maintenance
class Maintenance
{
  func addFuel() {}
  func changeOil() {}
  func rotateTires() {}
}
//편의 제공을 담당하는 Convenience
class Convenience
{
  func adjustDriverSeat() {}
  func turnOnAC() {}
  func playCD() {}
}
//로 decouple할 수 있습니다.
class Car
{
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

##### 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야한다.

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