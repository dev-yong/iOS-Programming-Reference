# Style Guide

## Overview

이전에도 한번 정독하였지만, 시간이 지나니 잊혀지게 되어 점점 제멋대로 코딩을 하고 있는 것 같은 기분이 들었습니다. 복습도 하고 정리도 할 겸, Style Guide에 나온 것과 다르게 종종 놓치는 부분과 다시 한번 상기해봐야할 것들에 대해서 정리하였습니다.

[Ray Wenderlich's Swift Style Guide](https://github.com/raywenderlich/swift-style-guide#access-control) 와 [Github's Swift Style Guide](https://github.com/github/swift-style-guide) 를 읽고 작성하였습니다.

## Table of Contents

- [Delegates](#Delegates)
- [Inferred Context and Type Inference](#Inferred-Context-and-Type-Inference)
- [User of Self](#User-of-Self)
- [Final](#Final)
- [Access Control](#Access-Control)
- [Function Declarations](#Function-Declarations)
- [Golden Path](#Golden-Path)
- [Reference](#Reference)

## Delegates

커스텀 delegate method를 만들 때, **이름이 없는 첫 parameter는 delegate source**여야합니다.

```swift
❌ func didSelectName(namePicker: NamePickerViewController, name: String)
✅ func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)

❌ func namePickerShouldReload() -> Bool
✅ func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

## Inferred Context and Type Inference

짧고 명확하게 코드를 작성하기 위하여 Compiler에서 추론된 context를 사용합니다.

```swift
❌ let selector = #selector(ViewController.viewDidLoad)
✅ let selector = #selector(viewDidLoad)

❌ view.backgroundColor = UIColor.red
✅ view.backgroundColor = .red

❌ let message: String = "Click the button"
✅ let message = "Click the button"

❌ let currentBounds: CGRect = computeViewBounds()
✅ let currentBounds = computeViewBounds()
```

## User of Self

Swift는 Object의 properties나 method를 호출할 필요가 없으므로 `self`의 사용을 피합니다. 

단, Compiler가 요구한 경우에는 사용합니다.

> 개인적으로 지키지 않았던 내용입니다.
>
> closure 내에서 아래와 같은 구문을 사용해왔었는데, 일종의 compiler 버그라고 합니다.
>
> https://github.com/apple/swift-evolution/blob/master/proposals/0079-upgrade-self-from-weak-to-strong.md#relying-on-a-compiler-bug
>
> ```swift
> ❌ 
> guard let `self` = self else {
> 	return 
> }
> 
> ✅
> guard let self = self else {
>     return
> }
> ```

## Final

`final` 키워드를 사용하여 method, property, 또는 subscript에 대한 override를 compile time에 error를 보내줘 막을 수 있습니다. (참고 : [The Swift Programming Language - Inheritance](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html))

`final` 키워드를 class나 members에 사용하는 것은 주요 주제로 부터 산만하게 하게 할 수 있고, 필요하지 않을 수도 있습니다. 그럼에도 불구하고 `final`은 가끔 의도를 명확히 할 수 있으며 그럴만한 가치가 있습니다.

## Access Control

모든 access control은 주요 주제로 부터 산만하게 하게 할 수 있고, 필요하지 않을 수도 있습니다. `private` 과 `fileprivate` 을 적절하게 사용한다면 명확성을 추가하고 encapsulation을 촉진할 수 있습니다.

>  Using `private` and `fileprivate` appropriately, however, adds clarity and promotes encapsulation. 음.. promote의 뜻이 애매합니다.

access control을 선행 property specifier로 지정합니다. `@IBAction`, `@IBOutlet`, `@discardableResult` 등과 같은 속성이나 `static` 만이 access control 앞에 올 수 있습니다.

```swift
🤔 fileprivate let message = "Great Scott!"
✅ private let message = "Great Scott!"

class TimeMachine {  
❌  lazy dynamic private var fluxCapacitor = FluxCapacitor()
✅  private dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```

## Function Declarations

긴 함수의 경우, 각 parameter를 작성할 때 새로운 줄과 들여쓰기를 합니다.

```swift
func reticulateSplines(
  spline: [Double], 
  adjustmentFactor: Double,
  translateConstant: Int, comment: String
) -> Bool {
  // reticulate code goes here
}
```

`(Void)`를 사용하지 않습니다. 또한 closure와 function의 output에 대해서는 `()`  대신 `Void`를 사용합니다.

```swift
❌ func updateConstraints() -> () {}
✅ func updateConstraints() -> Void {}


❌ typealias CompletionHandler = (result) -> ()
✅ typealias CompletionHandler = (result) -> Void
```

## Golden Path

조건문에 대하여 코딩할 때, 좌측 여백의 코드는 "golden" 또는 "happy" path여야합니다.

즉, `if` 구문으로 감싸지말고, `guard` 구문을 활용하면 됩니다.

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  guard let context = context else {
    throw FFTError.noContext
  }
  guard let inputData = inputData else {
    throw FFTError.noInputData
  }

  // use context and input to compute the frequencies
  return frequencies
}
```

## Reference

- https://github.com/raywenderlich/swift-style-guide
- https://github.com/github/swift-style-guide
- https://github.com/minsOne/swift-style-guide/blob/master/README_KR.md