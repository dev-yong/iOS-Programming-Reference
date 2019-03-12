# Class Diagram

## Table of Contents

- [Generalization](#Generalization)
- [Realization](#Realization)
- [Dependency](#Dependency)
- [Association](#Association)
- [Aggregation](#Aggregation)
- [Composition](#Composition)

![](https://upload.wikimedia.org/wikipedia/commons/0/0b/Uml_class_relation_arrows_en.svg.png)

## Generalization

- 일반화

- 슈퍼 클래스와 서브 클래스간의 Inheritance(상속) 관계를 나타냅니다.
  - 서브 클래스는 슈퍼 클래스로 Generalize(일반화) 할 수 있습니다.
  - 슈퍼 클래스를 서브클래스로 Specialize(구체화) 할 수 있습니다.

```swift
class SuperClass {
    func foo() {
    }
}

class ChildClass: SuperClass {
    override func foo() {
	}
}
```

## Realization

- 실체화
- Interface(인터페이스)의 메서드를 실제 기능으로 구현하는 것입니다.

```swift
protocol RealizationProtocol {
    func foo() 
}

class Realization: RealizationProtocol {
    func foo() {
    }
}
```

## Dependency

- 의존
- 어떠한 클래스가 다른 클래스를 참조하는 것을 말합니다.
  - 단, 해당 객체의 참조를 계속 유지하지는 않습니다.

```swift
class Human {
    //해당 객체의 참조를 계속 유지하지는 않는다.
	func use(_ tool: Tool) {
		tool.foo()
	}
}

class Tool {
	func foo() {
	}
}
```

## Association

- 연관
- 다른 객체의 참조를 갖는 필드를 의미합니다.
- 방향성을 갖을 수 있습니다.

```swift
class Person {
    let cars: [Car]
    
    func own(cars: [Car]) {
        self.cars = cars
	}
}
```

## Aggregation

- 집약
- Composition보다 약한 집합을 의미합니다.
  - part가 whole에 대해 독립적입니다.
  - whole 인스턴스는 part 인스턴스를 빌려 사용합니다.
  - whole 인스턴스가 소멸되어도, part 인스턴스도 소멸되지 않습니다.
  - whole 인스턴스가 복사되어도, part 인스턴스도 복사되지 않습니다.

```swift
class Computer {
    let mainboard: MainBoard
    let cpu: CPU
    
    init(mainboard: MainBoard, cpu: CPU) {
        self.mainboard = mainboard
        self.cpu = cpu
	}
}
```

`Computer` 가 메모리에서 사라진다하더라도 `Mainboard` 와 `CPU` 는 사라지지 않습니다.

## Composition

- 합성
- Aggregation보다 더 강한 집합을 의미합니다.
  - part가 whole에 종속적입니다.
  - whole 인스턴스가 part 인스턴스의 저체 수명을 책임집니다.
    - whole 인스턴스가 part 인스턴스를 생성합니다.
    - whole 인스턴스가 소멸되면, part 인스턴스도 소멸됩니다.
    - whole 인스턴스가 복사되면, part 인스턴스도 복사됩니다.

```swift
class Computer {
    let mainboard: MainBoard
    let cpu: CPU
    
    init() {
        self.mainboard = Mainboard()
        self.cpu = CPU()
	}
}
```

`Computer` 가 메모리에서 사라진다면,  `Mainboard` 와 `CPU` 도 사라지게 됩니다.

## Reference 

- http://www.nextree.co.kr/p6753/