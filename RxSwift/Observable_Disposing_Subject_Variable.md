# RxSwift - Observable, Disposing, Subject, Variable

## Table of Contents

- [Observable](#Observable)
  - [Basic](#Basic)

  - [Event](#Event)
- [Disposing](#Disposing)
  - [Dispose Bag](#Dispose-Bag)
  - [Take Until](#Take-Until)
- [Subject](#Subject)
  - [PublicSubject](#PublicSubject)
  - [ReplaySubject](#ReplaySubject)
  - [BehaviorSubject](#BehaviorSubject)
- [Variable](#Variable)
- [Reference](#Reference)

## Observable

### Basic

- **모든 `Observable` 은 sequence**입니다.

- Element를 **asynchronous**하게 받을 수 있습니다.

- 어떠한 thread에서 element를 생성하여 `observer.on(.next(nextElement))` 로 보내던 간에, `observer.on` 이 끝나기 전까지는 보낼 수 없습니다.

  - 또한, `.next` 가 끝나기 전까지 `.completed` 혹은 `.error` 를 보낼 수 없습니다.


```swift
someObservable
  .subscribe { (e: Event<Element>) in
      print("Event processing started")
      // processing
      print("Event processing ended")
  }

//❌ 불가능
//Event processing started
//Event processing started
//Event processing ended
//Event processing ended

//✅ 
//Event processing started
//Event processing ended
//Event processing started
//Event processing ended
//Event processing started
//Event processing ended
```

- ` Observable`이 **생성되었더라도, `Observable`은 어떠한 작업도 수행하지 않습니다.**

  - `Observable`은  단지 **sequence가 생성되는 방법과 element 생성에 어떤 parameter가 사용되는지를 정의**합니다.
  - Sequence는 **`subscribe()` 가 호출되었을 때 생성되기 시작**합니다.

```swift
let observable = Observable<String>.create { observerOfString in
       print("Observable created")
       observerOfString.on(.next("😉"))
       observerOfString.on(.completed)
       return Disposables.create()
}
//subscribe()가 없으면 observable이 생성되었어도 아무일도 일어나지 않습니다. 

//observable을 subscrbie 함으로써 sequence가 생성되기 시작합니다.
observable.subscribe({ (event) in
    switch event {
    case .next(let element):
        print("next(\(element))")
    case .error(let error):
        print(error)
    case .completed:
        print("completed")
    }
})
//Observable created
//next(😉)
//completed
```

- 모든 subscriber는 대개 자체적으로 개별적인 element sequence를 생성합니다.
- **Operator들은 기본적으로 stateless**입니다. 
  - Stateless Operator가 Stateful Operator보다 훨씬 많습니다.

### Event

- Sequence는 0개 혹은 그 이상의 element를 갖을 수 있습니다.
- **`error` 또는 `complete` event를 받았을 때, sequecne는 element를 생산할 수 없습니다.**
  - Sequnce가 `completed` 또는 `error` event를 보내면, sequence element들을 계산하기 위한 **모든 내부 자원들은 해방됩니다**.

```swift
enum Event<Element>  {
    case next(Element)      // next element of a sequence
    case error(Swift.Error) // sequence failed with error
    case completed          // sequence terminated successfully
}

class Observable<Element> {
    func subscribe(_ observer: Observer<Element>) -> Disposable
}

protocol ObserverType {
    func on(_ event: Event<Element>)
}
```

## Disposing

> 다시 한번 읽고 정리하자! (https://github.com/ReactiveX/RxSwift/blob/master/Documentation/GettingStarted.md#disposing)
>

- Sequence를 종료하고 resources를 해제하고자 할때, `dispose` 를 호출합니다.
  - `DisposeBag` ,  `takeUntil` , 혹은 또다른 메커니즘을 사용하는 더 좋은 방법들이 있습니다.

```swift
func subscribe(onNext: ((Int) -> Void)? = default, onError: ((Error) -> Void)? = default, onCompleted: (() -> Void)? = default, onDisposed: (() -> Void)? = default) -> Disposable
```

```swift
let scheduler = SerialDispatchQueueScheduler(qos: .default)
let subscription = Observable<Int>.interval(0.3, scheduler: scheduler)
    .subscribe { event in
        print(event)
}

Thread.sleep(forTimeInterval: 2.0)

subscription.dispose()
//dispose()를 호출함으로써 Sequence를 종료합니다.
```

- 위의 코드는 `dispose` 실행 이후에 어떤 것들을 출력할 수 있을까?

  - `scheduler` 가 **Serial Scheduler**(ex. `MainScheduler`)이며 `dispose` 가 **동일한 serial scheduler** 에서 호출이 된다면, 출력이 **불가능**합니다.
  - 그렇지 않다면, 출력이 **가능**합니다.
  - 이러한 프로세스가 다른 scheduler에 있다면 의미가 없습니다.

  > 음.. 계속 읽어보지만 아직 잘 이해가 가지 않습니다. :'(

```swift
let scheduler = SerialDispatchQueueScheduler(qos: .default)
let subscription = Observable<Int>.interval(0.3, scheduler: scheduler)
            .observeOn(MainScheduler.instance)
            .subscribe { event in
                print(event)
            }

Thread.sleep(forTimeInterval: 2.0)

subscription.dispose() // called from main thread
//interval(, scheduler: )은 `scheduler`에 위치하고 있고,
//subscribe와 dispose는 `MainScheduler`에 위치하고 있어
//의미가 없습니다.
```

### Dispose Bag

- `DisposeBag` 의 할당이 해제될 때, `DisposeBag` 에 들어가 있는 disposeable들의 `dispose` 를 호출할 것입니다.
- `DisposeBag` 은 `dispose` method가 존재하지 않습니다.
  - 즉각적인 정리가 필요하다면, 새롭게 할당해주면 됩니다.

```swift
self.disposeBag = DisposeBag()
```

> Q. Driver와 Observable의 차이? (조금 더 이후의 내용)
>
> Q. Observable이 dispose되면 complete를 뿜게 되는가? ❌
>
> Q. Observable이 error 혹은 complete를 뿜게 되면(event가 종료가 된다면), dispose가 되는가? ✅
>
> - onNext: Action to invoke for each element in the observable sequence.
> - onError: Action to invoke upon errored termination of the observable sequence.
> - onCompleted: Action to invoke upon graceful termination of the observable sequence.
> - onDisposed: Action to invoke upon any type of termination of sequence (if the sequence has gracefully completed, errored, or if the generation is canceled by disposing subscription).

### Take Until

- 자동적으로 subscription을 dispose시키기 위한 방법 중 하나입니다.

```swift
let subscription = Observable<Int>.interval(0.3, scheduler: scheduler)
            .takeUntil(rx.deallocated)
            .subscribe { event in
                print(event)
        }
```

## Subject

- `Observer` 임과 동시에  `Observable` 입니다
  - `Observer`이기 때문에 하나 이상의  ` Observable` 을 subscribe할 수 있습니다.
  - `Observable`이기 때문에 reemit함으로서 observe하는 item을 통과 시킬 수 있습니다. 또한, 새로운 item도 emit할 수 있습니다.
- `PublishSubject`, `ReplaySubject`, `BehaviorSubject` 는 Dispose되더라도 Complete event를 자동으로 emit하지 않습니다.

> Subject들의 예시를 위함입니다.
>
> ```swift
> extension ObservableType {
>     
>     /**
>      Add observer with `id` and print each emitted event.
>      - parameter id: an identifier for the subscription.
>      */
>     func addObserver(_ id: String) -> Disposable {
>            return subscribe(onNext: { print("Subscription: ", id, "Event:", $0) }, onCompleted: { print("\(id) is completed") }, onDisposed: {print("\(id) is disposed")})
>        } 
> }
> ```

### PublishSubject

- 값이 없는 상태로 시작하여, 새로운 값만을 subscriber에 emit합니다.
- Sequence가 error 혹은  complete에 의하여 종료될 경우, 기존의 subscribe들에게 종료 Event를 emit합니다. 또한, 이후의 subscribe에게도 종료 Event를 reemit합니다.

![](https://cdn-images-1.medium.com/max/1200/0*Gb5XdAgf__b8G8h2.png)

```swift
let disposeBag = DisposeBag()
let subject = PublishSubject<String>()

subject.addObserver("1").disposed(by: disposeBag)
subject.onNext("🐶")
//Subscription:  1 Event: 🐶
subject.onNext("🐱")
//Subscription:  1 Event: 🐱

subject.addObserver("2").disposed(by: disposeBag)
subject.onNext("🅰️")
//Subscription:  1 Event: 🅰️
//Subscription:  2 Event: 🅰️
subject.onNext("🅱️")
//Subscription:  1 Event: 🅱️
//Subscription:  2 Event: 🅱️

subject.onCompleted()
//1 is completed
//1 is disposed
//2 is completed
//2 is disposed
subject.addObserver("3").disposed(by: disposeBag)
//3 is completed
//3 is disposed
```

#### ReplaySubject

- 모든 subscriber에게 새로운 event를 보내고, bufferSize의 수만큼 이전 event를 새로운 subscriber에게 방출합니다.

- Buffer Size만큼 emit할 최신 element를 Caching하거나 Buffer를 이용합니다. 그 후 해당 Buffer를 새로운 subscriber에게 emit합니다.

  > Buffer에 emit할 것들이 저장되어져 있어, 종료된 ReplaySubject를 subscribe하게 되어도 Buffer Size 값만큼 emit 받을 수 있습니다.

- Sequence가 error 혹은  complete에 의하여 종료될 경우, 기존의 subscribe들에게 종료 Event를 emit합니다. 또한, 이후의 subscribe에게도 종료 Event를 reemit합니다.


![](https://cdn-images-1.medium.com/max/1600/0*uVtsGFtLCDfd9FP3.png)

```swift
let disposeBag = DisposeBag()
let subject = ReplaySubject<String>.create(bufferSize: 1)

subject.addObserver("1").disposed(by: disposeBag)
subject.onNext("🐶")
subject.onNext("🐱")
//Subscription:  1 Event: 🐶
//Subscription:  1 Event: 🐱

subject.addObserver("2").disposed(by: disposeBag)
//bufferSize만큼의 이전 event를 받아옵니다.(1개 만큼의 이전 event를 받아옵니다.)
//Subscription:  2 Event: 🐱
subject.onNext("🅰️")
//Subscription:  1 Event: 🅰️
//Subscription:  2 Event: 🅰️
subject.onNext("🅱️")
//Subscription:  1 Event: 🅱️
//Subscription:  2 Event: 🅱️

subject.onCompleted()
//1 is completed
//1 is disposed
//2 is completed
//2 is disposed
subject.addObserver("3").disposed(by: disposeBag)
//Subscription:  3 Event: 🅱️
//3 is completed
//3 is disposed
```

### BehaviorSubject

- 모든 subscriber에게 새로운 event를 보내고, 가장 최신의 값(혹은 초기값)을 새로운 subscriber에게 방출합니다.
- 반드시 **초기값**이 존재하여야합니다.
- Sequence가 error 혹은  complete에 의하여 종료될 경우, 기존의 subscribe들에게 종료 Event를 emit합니다. 또한, 이후의 subscribe에게도 종료 Event를 reemit합니다.


![](https://cdn-images-1.medium.com/max/1200/0*Vb0_X7aVyfuVd8G4.png)

```swift
let disposeBag = DisposeBag()
let subject = BehaviorSubject(value: "🔴")

subject.addObserver("1").disposed(by: disposeBag)
//Subscription:  1 Event: 🔴
subject.onNext("🐶")
//Subscription:  1 Event: 🐶
subject.onNext("🐱")
//Subscription:  1 Event: 🐱

subject.addObserver("2").disposed(by: disposeBag)
//Subscription:  2 Event: 🐱
subject.onNext("🅰️")
//Subscription:  1 Event: 🅰️
//Subscription:  2 Event: 🅰️
subject.onNext("🅱️")
//Subscription:  1 Event: 🅱️
//Subscription:  2 Event: 🅱️

subject.addObserver("3").disposed(by: disposeBag)
//Subscription:  3 Event: 🅱️
subject.onNext("🍐")
//Subscription:  1 Event: 🍐
//Subscription:  2 Event: 🍐
//Subscription:  3 Event: 🍐
subject.onNext("🍊")
//Subscription:  1 Event: 🍊
//Subscription:  2 Event: 🍊
//Subscription:  3 Event: 🍊

subject.onCompleted()
//1 is completed
//1 is disposed
//2 is completed
//2 is disposed
//3 is completed
//3 is disposed
subject.addObserver("4").disposed(by: disposeBag)
//4 is completed
//4 is disposed
```

## Variable

- `BehaviorSubject` 를 wrapping한 것으로, 현재 값을 **State** 로 보유합니다.
- "**Error가 발생하지 않는다**"를 보장하기 때문에 `.error` 를 사용할 수 없습니다.
- 메모리 할당이 해제되었을 때, 자동적으로 complete되므로 `.completed` 를 사용할 수 없습니다.

```swift
var disposeBag = DisposeBag()
let subject = Variable("🔴")
   
subject.asObservable().addObserver("1").disposed(by: disposeBag)
//Subscription:  1 Event: 🔴
subject.value = "🐶"
//Subscription:  1 Event: 🐶
subject.value = "🐱"
//Subscription:  1 Event: 🐱

subject.asObservable().addObserver("2").disposed(by: disposeBag)
//Subscription:  2 Event: 🐱
subject.value = "🅰️"
//Subscription:  1 Event: 🅰️
//Subscription:  2 Event: 🅰️
subject.value = "🅱️"
//Subscription:  1 Event: 🅱️
//Subscription:  2 Event: 🅱️

subject.asObservable().addObserver("3").disposed(by: disposeBag)
//Subscription:  3 Event: 🅱️
subject.value = "🍐"
//Subscription:  1 Event: 🍐
//Subscription:  2 Event: 🍐
//Subscription:  3 Event: 🍐
subject.value = "🍊"
//Subscription:  1 Event: 🍊
//Subscription:  2 Event: 🍊
//Subscription:  3 Event: 🍊z
```

## Reference

- [RxSwift Documentation](https://github.com/ReactiveX/RxSwift/blob/master/Documentation)
- [ReactiveX](http://reactivex.io/)
- [RxSwift - fimuxd](https://github.com/fimuxd/RxSwift)
- [Learn & Master ⚔️ the Basics of RxSwift in 10 Minutes - Sebastian Boldt](https://medium.com/ios-os-x-development/learn-and-master-%EF%B8%8F-the-basics-of-rxswift-in-10-minutes-818ea6e0a05b)
