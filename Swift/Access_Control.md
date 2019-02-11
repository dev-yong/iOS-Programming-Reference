# Access Control

## Overview

사내 Library 구현에 대하여 고려하던 도중, Access Control에 대한 지식이 부족하고 느껴 복습을 위하여 정리하였습니다.

## Table of Contents

- [Modules and Source Files](#Modules-and–Source-Files)
- [Access Level](#Access-Level)
  - [Custom Types](#Custom-Types)
  - [Function Types](#Function-Types)
  - [Enumeration Types](#Enumeration-Types)
- [Subclassing](#Subclassing)
- [Constants, Variables, Properties, and Subscripts](#Constants, Variables, Properties,-and-Subscripts)
  - [Getters and Setters](#Getters-and-Setters)
- [Initializers](#Initializers)
  - [Default Initializers](#Default-Initializers)
  - [Default Memberwise Initializers for Structure Types](#Default-Memberwise-Initializers-for-Structure-Types)

## Modules and Source Files

Swift의 access control model은 **module** 과 **source file** 에 기초합니다.

**Module** 은 code distribution의 단일 단위입니다. framework 또는 apllication은 단일 단위로 빌드되고 옮겨지며  `import`  키워드를 이용하여 다른 module에 import시킬 수 있습니다.

**Source File** 은 module 내에 있는 단일 Swift source code 파일입니다.

## Access Level

Access Level은 entity가 정의된 source file에 상대적이며, 또한 source file이 속한 module에 대하여 상대적입니다.

크게 `open`, `public`, `internal`, `fileprivate`, `private` 로 구성되어 있으며 아래와 같은 원칙하에 사용됩니다.

**"No entity can be defined in terms of another entity that has a lower (more restrictive) access level."** (Access Level이 더 낮은 다른 entity의 관점에서 어떤 entity도 정의할 수 없다.)

#### `open`

- class와 class member에 대해서 가능 / struct, enum은 불가능
- 정의된 module 내의 모든 source file 및 해당 module을 import한 다른 module의 source file에서 접근 가능
- `open class` 는 정의된 module 뿐만아니라 import 한 다른 module에서도 subclass를 만들 수 있음
- class member는 정의된 module 뿐만아니라 subclass에 의하여 import 한 다른 module에서도 override될 수 있음

#### `public`

- 정의된 module 내의 모든 source file 및 해당 module을 import한 다른 module의 source file에서 접근 가능
- 정의된 module에서만 subclass를 만들 수 있음
- class member는 정의된 모듈내에서만 subclass에 의하여 override될수 있음

#### `internal`

- default access level
- 정의된 module 내의 모든 source file에서 접근 가능
- 정의된 module에서만 subclass를 만들 수 있음
- class member는 정의된 모듈내에서만 subclass에 의하여 override될수 있음

#### `fileprivate`

- 정의된 source file 내에서만 접근 가능
- 정의된 module에서만 subclass를 만들 수 있음
- class member는 정의된 모듈내에서만 subclass에 의하여 override될수 있음

#### `private`

- 정의된 선언부 내에서 접근가능, 동일한 source file 내의 선언에 대한 extension 내에서만 접근 가능
- 정의된 module에서만 subclass를 만들 수 있음
- class member는 정의된 모듈내에서만 subclass에 의하여 override될수 있음

### Custom Types

Type의 access control level은 해당 type의 member들의 기본 access level에 영향을 끼칩니다.

```swift
public class SomePublicClass {                  // explicitly public class
    public var somePublicProperty = 0            // explicitly public class member
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

class SomeInternalClass {                       // implicitly internal class
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

fileprivate class SomeFilePrivateClass {        // explicitly file-private class
    func someFilePrivateMethod() {}              // implicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

private class SomePrivateClass {                // explicitly private class
    func somePrivateMethod() {}                  // implicitly private class member
}
```

### Function Types

함수는 가장 낮은 access level을 따라갑니다. SomePrivateClass가 `private` 으로 가장 낮으니, 함수의 access level도 `private` 으로 지정해야합니다.

```swift
❌ func someFunction() -> (SomeInternalClass, SomePrivateClass) {}
✅ private func someFunction() -> (SomeInternalClass, SomePrivateClass) {}
```

### Enumeration Types

enumeration의 case마다 다른 access level을 부여하는 것은 불가능하며 enumeration의 access level을 따라갑니다.

Raw Value와 Associated Value의 경우, enumeration의 access level보다 높거나 같아야합니다.

## Subclassing

subclass는 superclass보다 높은 access level을 갖을 수는 없지만, class member를 override 한 경우에는 가능합니다. 또한, class member가 보다 낮은 access level의 superclass member를 호출하는 것은 허용된 access level context 내에서는 유효합니다.

```swift
public class A {
    fileprivate func someMethod() {}
}

internal class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}
```

## Constants, Variables, Properties, and Subscripts

Constant, variable, property, 또는 subscript는 해당 type의 access level보다 낮은 level로 구성합니다.

```swift
private var privateInstance = SomePrivateClass()
```

### Getters and Setters

setter 에게 getter보다 낮은 access level을 부여함으로서, read-write 범위를 제한할 수 있습니다.

```swift
struct TrackedString {
    private(set) var numberOfEdits = 0
    var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
}
```

`TrackedString` 과  `value` 는 기본 access level인 `internal` 를 받습니다.

그러나, `numberOfEdits` 는 `private(set)`  를 이용하여 setter에 낮은 access level을 부여하였습니다. 이를 통하여 선언부 내 혹은 동일한 source file 내의 선언에 대한 extension 내에서만 `numberOfEdits` 의 값을 set할 수 있습니다.

## Initializers

custom initializer는 access level를 같거나 보다 낮은 것에 대하여 지정할 수 있습니다. 

단, **required** 인 경우에는 동일한 access level을 가져야합니다.

### Default Initializers

Default Initializer는 `public` 으로 정의되어져 있지 않다면, initialize하는 type과 동일한 access level을 갖습니다. `pulbic` 으로 type이 지정되어져 있다면, default initializer는 `internal` 로 간주됩니다.

### Default Memberwise Initializers for Structure Types

만약 struct의 stored property들 중 하나라도 private라면 default memberwise initializer는 private로 간주된다. 반면, initailizer는 internal의 접근 수준을 가진다.

```swift
❌ 
struct User {
    var id: String
    private var pw: String
}

User(id: "ID", pw: "pw") //Default Memberwise Initializers
//'User' initializer is inaccessible due to 'private' protection level

✅ 
struct User {
    var id: String
    private var pw: String
    init(id: String, pw: String) { //Initializer, internal
        self.id = id
        self.pw = pw
	}
}

User(id: "ID", pw: "pw") 
```

## Refence

- https://docs.swift.org/swift-book/LanguageGuide/AccessControl.html
- http://minsone.github.io/mac/ios/swift-access-control-summary