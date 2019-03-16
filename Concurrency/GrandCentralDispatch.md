# Global Central Dispatch

Application에는 Main Thread가 존재하고 있습니다. 이 Main Thread는 User Interface를 책임지고 있습니다. 만약, Data transform이나 Image Processing과 같은 작업이 Main Thread에서 진행되게 된다면, 아마 UI는 버벅이거나 멈출 것입니다.

![image-20190314000412190](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190314000412190.png)

이러한 문제를 해결하기 위하여 Concurreny 아이디어가 존재합니다.

Concurrency는 여러 부분을 동시에 실행하도록 허용합니다. Thread를 생성하여 concurrency를 얻을 수 있습니다.

CPU core는 thread 중 하나를 언제든지 실행할 수 있습니다. 하지만 concurrency의 도입 시 thread의 안정성을 유지하는 것이 어려워집니다. 다른 스레드는 또 다른 스레드에서 작업을 수행하는 동안 코드 불변성을 깨는 효과를 관찰 할 수 있습니다. 이것은 문제가됩니다.

GCD는 concurrency library로 multi-thread 코드를 작성할 수 있게 도와줍니다.

Dispath queue는 작업 항목을 해당 대기열에 제출할 수있게 해주는 구성입니다. Swift 에서는 closure를 이용합니다.

![image-20190314232117275](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190314232117275.png)

Dispatch는 thread와 service를 제공할 것입니다. 

Dispatch가 해당 thread에서 모든 작업을 끝마쳤을 때, 해당 작업 thread는 스스로 찢어질 수 있습니다. 

자신만의 thread를 생성할 수 있고, run loop를 실행할 수 있습니다.

당신은 또한 당신 자신의 쓰레드를 만들 수 있고, 그 쓰레드들에서 실행 루프를 실행할 수 있습니다.

Main thread는 Main run loop와 Main queue 모두를 얻습니다.

![image-20190314232640575](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190314232640575.png)

Dispatch queue는 제출할 수 있는 두가지 방법이 있습니다. 

Asynchronous

Dispatch queue에 다중의 작업 항목들을 넣고 다시 dispatch하면, 해당 작업을 수행할 thread 를 불러옵니다.

Dispatch는 작업 항목을 queue에서 꺼내와 수행합니다. Queue에 존재하는 모든 작업 항목을 끝마쳤을 때, 시스템은 사용했던 thread를 도로 가져갑니다.

![image-20190315072254783](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190315072254783.png)

Synchronous

자신만의 Thread를 가지고 있고, 그 thread는 해당 queue에서 코드를 실행하고 그것이 일어날 때까지 기다리고 싶어합니다. 해당 작업을 발송 대기열에 제출할 수 있지만 그 작업이 차단됩니다. 실행을 요청한 항목이 완료 될 때까지 기다립니다.

이 queue에 asynchronous 작업을 더 추가할 수 있습니다. 그리고 dsipatch는 queue에 잇는 항목들을 서비스하기 위하여 thread를 불러옵니다.

Asynchronous 작업을 수행하다가 synchronous 작업을 수행해야할 시점이 오면, dispatch queue는 대기 중인 thread로 제어권을 전달하고 항목을 실행한 후 dispatch queue의 제어권은 dispatch에 의하여 제어되는 worker thread로 돌아갑니다.

디스패치 대기열은 대기중인 스레드로 제어를 전달하고 해당 항목을 실행 한 다음 디스패치 대기열을 제어하여 디스패치로 제어되는 작업자 스레드로 다시 되돌아갑니다.



위에서 나온 문제에 대한 해결방안으로는 다른 queue에서 데이터 변환을 하고 변환된 데이터를 다시 main thread에 넘겨줍니다. 이렇게하면 main thread가 유휴상태이며 이벤트를 처리하는 동안 해당 작업을 수행할 수 있습니다.

![image-20190315074717097](/Users/igwang-yong/Library/Application Support/typora-user-images/image-20190315074717097.png)

DispatchQueue는 FIFO로 작업을 수행합니다. 즉, queue에 제출한 순서가 dispatch 내에서 수행되는 순서가 됩니다.

이미지 변환을 위한 queue에서 변환을 수행한 후 `DispatchQueue.main` 을 이용하여 데이터를 넘겨줍니다.

```swift
queue.async {
    let smaillImage = image.resize(to: rect)
    
    DispatchQueue.main.async {
        imageView.image = smallImage
    }
}
```

Dispatch main queue는 main thread에서 실행하는 모든 항목들을 서비스합니다. 즉, `DispatchQueue.main` 을 호출한 다음 main queue에서 aync하게 호출하면 해당 코드가 수행되고 UI를 업데이트할 수 있습니다.

Concurrency를 꼭 제어해야합니다. Dipsatch가 사용하는 thread pool은 모든 호출을 완성하기 위하여 호출된 concurrency를 제한합니다.

https://www.raywenderlich.com/5370-grand-central-dispatch-tutorial-for-swift-4-part-1-2

https://medium.com/@gabriel_lewis/threading-in-swift-simply-explained-5c8dd680b9b2

https://developer.apple.com/videos/play/wwdc2017/706/

https://developer.apple.com/videos/play/wwdc2016/720/

https://developer.apple.com/videos/play/wwdc2015/226/

https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html

