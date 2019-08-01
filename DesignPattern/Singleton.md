# Singleton

- 특정 클래스의 인스턴스가 오직 하나임을 보장하는 객체입니다.
- 글로벌하게 액세스할 수 있는 클래스의 공유 인스턴스를 제공하기 위해 사용합니다.
- e.g. `NSNotificationCenter`, `UIApplication` , ...
- iOS Application은 기본적으로 Multi-threading을 이용하기 때문에, Singleton 객체는 thread-safe하게 만들어주어야합니다.

```swift
class Singleton {
    static let sharedInstance = Singleton()
}
```

## Reference

- https://github.com/ochococo/Design-Patterns-In-Swift
- https://developer.apple.com/documentation/swift/cocoa_design_patterns/managing_a_shared_resource_using_a_singleton
- [https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80%ED%84%B4_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/싱글턴_패턴)

