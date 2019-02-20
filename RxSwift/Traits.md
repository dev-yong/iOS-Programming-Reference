# RxSwift - Traits

## Table of Contents

- Traits
- RxSwift Traits
  - Single
  - Completable
  - Maybe
- RxCocoa Traits

## Traits

> 네이버 사전 : 특징, 특성, 특색

- Traits는 interface의 경계를 넘어 커뮤니케이션을 도와주고 observable sequence property를 보장하는 것을 도와줍니다.

- 일반적인 Observable을 사용할때보다 문맥 상의 의미와 syntatic sugar을 제공하고 use-case를 목표로 삼습니다.

  > Syntatic Sugar는 일을 읽기 쉽게하거나 표현하기 위해 디자인된 프로그래밍 언어 내의 syntax입니다(참고 : https://en.wikipedia.org/wiki/Syntactic_sugar)

  - 이러한 이유로, Traits는 전체적으로 `optional` 입니다.

- Traits는 단일 read-only Observable property의 wrapper struct입니다.

```swift
struct Single<Element> {
    let source: Observable<Element>
}

struct Driver<Element> {
    let source: Observable<Element>
}
...
```

## RxSwift Traits

### Single

- **항상 하나의 element 혹은 하나의 error만을 방출**합니다.
- Side Effect를 공유하지 않습니다.
- `.success(value)` (= `.next` + `.completed`) 혹은 `.error` 만을 방출합니다.
- 일반적인 Observable을 `.asSingler()` 을 이용하여 Single Trait으로 변형할 수 있습니다.

### Completable

- **오직 complete 또는 error만 방출**합니다.
- **Element를 방출하지 않습니다.**
- Side Effect를 공유하지 않습니다.
- 일반적인 Observable을 Completable Trait으로 변형할 수 없습니다.

### Maybe

- Single과 Completable의 중간입니다.
- **단일 element를 방출할 수 있고, element의 방출 없이 complete할 수 있으며, error를 방출**할 수 있습니다.
  - 이 3가지의 event들은 Maybe Trait을 종료합니다.
  - Complete된 Mabye는 종료되었기 때문에 element를 방출할 수 없고, element를 방출한 Maybe는 이 역시 종료되었기 때문에 complete될 수 없습니다.
- Side Effect를 공유하지 않습니다.
- 일반적인 Observable을 `.asMaybe()` 을 이용하여 Maybe Trait으로 변형할 수 있습니다.

## RxCocoa Traits

### Driver

- Driver는 UI 계층에서의 reactive code를 적는 데에 있어 직관적인 방법을 제시하려는 의도를 가지고 있습니다.
- **Error을 방출할 수 없습니다.** = **절대 실패하지 않습니다**.
- **Observe는 main scheduler에서만 발생**합니다.
- Side effect를 공유합니다.
- Driver의 의도된 use-case는 application을 drive하는 sequence를 모델링하는 것입니다.
  - e.g. CoreData modle로부터 UI를 drive합니다.
  - e.g. 다른 UI elements로 부터 value를 이용하여 UI를 drive합니다.

## Reference

- [RxSwift Documentation](https://github.com/ReactiveX/RxSwift/blob/master/Documentation)
- [ReactiveX](http://reactivex.io/)
- [RxSwift - fimuxd](https://github.com/fimuxd/RxSwift)