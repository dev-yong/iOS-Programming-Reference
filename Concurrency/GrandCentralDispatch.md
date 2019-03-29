# Global Central Dispatch

## Table of Contents

- [Concurrency](#Concurrency)

- [Dispatch Queue](#Dispatch-Queue)

  - [Serial Queue](#Serial-Queue)
  - [Concurrent Queue](#Concurrent-Queue)

  - [Main Dispatch Queue](#Main-Dispatch-Queue)
  - [Global Dispatch Queue](#Global-Dispatch-Queue)
    - [Quality of Service](#Quality-of-Service)

## Parallelism and Concurrency

- Concurrency는 structure에 관한 것인 반면 parallelism은 

### Parallelism

- 멀티 코어 디바이스에서 사용합니다.
- 여러개의 thread를 "동시"에 수행합니다.
- 반면, 멀티 코어 디바이스는 **parallelism** 를 통하여 여러개의 thread를 동시에 시수행합니다.

### Concurrency

- 싱글 코어 디바이스에서 사용합니다.
- **Time Slicing** 을 이용하여 
  1. 하나의 Thread를 수행하고
  2. Context Switch를 실행 후
  3. 또다른 Thread를 수행합니다.

![Image](https://koenig-media.raywenderlich.com/uploads/2014/01/Concurrency_vs_Parallelism.png)

## Asynchronous and Synchronous

### Asynchronous

### Synchronous

## Dispatch Queue

- 작업 단위를 queue에 전송하면 GCD는 FIFO(First in, First out) 순으로 작업을 실행합니다.
- GCD는 concurrency library로 multi-thread 코드를 작성할 수 있게 도와줍니다.
- Dispatch queue는 **thread safe** 합니다.

- 즉, 여러 thread들이 동시에 dispatch queue에 접근할 수 있습니다.

- **Dispatch queue** 에 code 블럭 혹은 work item을 추가할 수 있고, 어떠한 thread에서 이것들을 실행할 것인지를 결정합니다.
- 시스템과 이용가능한 시스템 리소스를 기반으로 하여 얼마나 많은 parallelism이 필요한지 결정합니다.
  - **Parallelism은 concurrency를 필요로합니다.**
  - 하지만, **concurrency는 parallelism을 보장하진 않습니다.**

### Serial Queue

- **주어진 시간에 대하여 오직 하나의 task만이 실행**됩니다.

![Serial](https://koenig-media.raywenderlich.com/uploads/2014/09/Serial-Queue-Swift-480x272.png)

### Concurrent Queue

- **동일한 시간에 여러개의 task를 실행**할 수 있습니다.
- FIFO순으로 실행하기 때문에,  task를 추가한 순서대로 시작하도록 보장됩니다.
  - 단, **task의 종료에 대한 순서는 알 수 없습니다.**

![Concurrent](https://koenig-media.raywenderlich.com/uploads/2014/09/Concurrent-Queue-Swift-480x272.png)

### Asynchronous

### Synchronous

### Main Dispatch Queue

- **Main thread에서 실행**됩니다.
- **Serail Queue**입니다.
- **UI에 대한 모든 것을 전담**합니다.

### Global Dispatch Queue

- 전체 시스템에서 공유하는 **Concurrent Queue** 입니다.

#### Quality of Service

- User-Interactive
- User-Initiated
- Utility
- Background



Asynchronous

Dispatch queue에 다중의 작업 항목들을 넣고 다시 dispatch하면, 해당 작업을 수행할 thread 를 불러옵니다.

Dispatch는 작업 항목을 queue에서 꺼내와 수행합니다. Queue에 존재하는 모든 작업 항목을 끝마쳤을 때, 시스템은 사용했던 thread를 도로 가져갑니다.

![image-20190315072254783](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190315072254783.png)

Synchronous

자신만의 Thread를 가지고 있고, 그 thread는 해당 queue에서 코드를 실행하고 그것이 일어날 때까지 기다리고 싶어합니다. 해당 작업을 발송 대기열에 제출할 수 있지만 그 작업이 차단됩니다. 실행을 요청한 항목이 완료 될 때까지 기다립니다.

이 queue에 asynchronous 작업을 더 추가할 수 있습니다. 그리고 dsipatch는 queue에 잇는 항목들을 서비스하기 위하여 thread를 불러옵니다.

Asynchronous 작업을 수행하다가 synchronous 작업을 수행해야할 시점이 오면, dispatch queue는 대기 중인 thread로 제어권을 전달하고 항목을 실행한 후 dispatch queue의 제어권은 dispatch에 의하여 제어되는 worker thread로 돌아갑니다.

디스패치 대기열은 대기중인 스레드로 제어를 전달하고 해당 항목을 실행 한 다음 디스패치 대기열을 제어하여 디스패치로 제어되는 작업자 스레드로 다시 되돌아갑니다.



![image-20190316215725397](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190316215725397.png)



Syncronous

Subsystem 사이의 serialize state를 위하여 synchronous 실행을 사용할 수 있습니다. Serial queue와 dispatch queue들은 자연적으로 연속적(serial)입니다. 그리고 이것을 mutual exclusion property로 사용할 수 있습니다.

```swift
var count: Int {
    queue.sync { self.connections.count }
}
```

deadlock을 발생시킬 수 있으니 주의해야합니다.

![image-20190316223442267](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190316223442267.png)

global 변수는  atomic하게 초기화 됩니다.  하지만 클래스 property와 lazy property는 atomic 하지 않습니다.

"There is no such thing as a benign race."

Synchronization 포인트를 잊는 다면, 크래쉬를 일으키거나 유저의 데이터를 손상시킬 수 있습니다.

```swift
class Foo {
    private let internalState: Int
    private let internalQueue: DispatchQueue
    var state: Int {
        get {
            return internalQueue.sync { internalState }
        }
        set {
        	internalQueue.sync { internalState = newState }
		}
    }
}
```

## Reference

https://www.raywenderlich.com/5370-grand-central-dispatch-tutorial-for-swift-4-part-1-2

https://developer.apple.com/videos/play/wwdc2017/706/

https://developer.apple.com/videos/play/wwdc2016/720/

