# GCD

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

Dispatch queue에 다중의 작업 항목들을 넣고 다시 dispatch하면, 해당 작업을 수행할 thread 가 생깁니다.



https://www.raywenderlich.com/5370-grand-central-dispatch-tutorial-for-swift-4-part-1-2

https://medium.com/@gabriel_lewis/threading-in-swift-simply-explained-5c8dd680b9b2

https://developer.apple.com/videos/play/wwdc2017/706/

https://developer.apple.com/videos/play/wwdc2016/720/

https://developer.apple.com/videos/play/wwdc2015/226/

https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html

