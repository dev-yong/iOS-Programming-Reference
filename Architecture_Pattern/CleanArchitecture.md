# Clean Architecture

- [특징](#특징)
- [Layer](#Layer)
  - [Dependency Rule](#Dependency-Rule)
  - [Entity](#Entity)
  - [UseCase](#UseCase)
  - [Interface Adapter](#Interface-Adapter)
  - [Framework and Driver](#Framework-and-Driver)

### "Architecture is about intent" 

> "아키텍쳐는 의도를 표현하는 것입니다."

- 제대로 된 Architecture는 App이 무엇을 위한 것이고, 무엇을 하는 것이지 알 수 있어야합니다.

  > ~~"이것은 iOS Application이다"~~를 뜻하는 것이 아닌, 
  >
  > "이것은 Todo list를 위한 Application이며, Todo에 대한 CRUD를 할 수 있다."를 알아야합니다.

![Architecture the lost years](https://hugo.ferreira.cc/wp-content/uploads/2012/07/tumblr_m7wjx4QuLX1qz82meo1_1280.png)

- 경계(||)를 이용하여 Low level detail로부터 High level policy를 보호함을 나타냅니다.
  - I/O들을 경계(||)를 통하여 소스 코드의 의존성을 역전시켜야 합니다.
- Controller는 사용자의 요청을 받아 Request Model(DTO, Command)를 만들고 Boundary<\<I>>를 통하여 경계(||)의 내부로 전달합니다.
- Interactor는 Boundary<\<I>>를 구현하고 있고, Controller에서 넘겨준 Request Model을 사용하여 Entity의 함수를 호출합니다.
- Interactor는 Entity Gateway<\<I>>를 통하여 상태를 불러오거나 저장합니다.
- Entity GateWay Implementaton은 Entity Gateway<\<I>>를 구현하고 있고, 데이터베이스 의 조회 및 저장 작업 등을 수행합니다.
- Interactor는 요청을 처리한 후, Response Model을 만들고 Boundary<\<I>>를 통하여 경계(||)의 외부로 전달합니다.
- Presentor는 Boundary<\<I>>를 구현하고 있으며, Interactor가 전달한 Response Model을 가공하고, View Model의 데이터를 채워줍니다.



- Application의 의도는 Use Case를 통하여 드러나게 됩니다.

  - 따라서, Application의 Architecture는 Use Case들입니다.

- UI, Database, Framework 등은 Detail입니다.

  - Database는 저장소일 뿐, Business Rule과는 관련이 없습니다. 

    > The database is a detail

### Use Case

- Application의 의도를 나타냅니다.
- I/O (e.g. UI, Database, Framework 등)을 모릅니다.

### Interactor

- Application에 종속적인 Business Rule을 다룹니다.

### Domain

- Application과 무관한 Business Rule을 다룹니다.



## 특징

- 프레임워크 독립적
- 테스트 용이함
  - 비지니스 규칙은 UI, 데이터베이스, 서버 등 기타 외부 요인 없이 테스트 가능합니다.
- UI 독립적
  - 시스템의 나머지 부분을 변경할 필요 없이 UI를 쉽게 변경할 수 있습니다.
- 데이터베이스 독립적
  - 어떠한 데이터베이스로도 바꿀 수 있습니다.
  - 비지니스 규칙은 데이터베이스에 얽매이지 않습니다.
- 외부 기능 독립적
  - 비지니스 규칙은 외부 세계에 대하여 아무것도 모릅니다.


## Layer

![The Clean Architecture](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)

### Dependency Rule

- 바깥 영역으로 갈수록 HighLevel Software가 됩니다.
  - 바깥 영역은 Mechanism이고, 내부 영역은 Policy입니다.
- **Dependency는 오직 내부**로만 향합니다.
  - 내부의 영역은 바깥 영역의 어떠한 것도 알아서는 안됩니다.
  - 바깥 영역의 어떠한 것도 내부 영역에 영향을 주어서는 안됩니다.
- 내부 계층으로 향할수록 추상화의 수준이 상승합니다.
  - 내부로 이동해가면서 소프트웨어는 추상화되고 구수준의 정책을 캡슐화 합니다.
- 가장 바깥 영역은 저수준의 구체적인 상세정보를 담습니다.

### Entity

- Enterprise Business Rule을 캡슐화합니다.
  - 단지 하나의 Application을 작성할 뿐이라면, 해당 Application의 Business Object가 됩니다.
- 가장 일반적이면서 고수준의 규칙을 캡슐화합니다.
- Business Object는 오염되지 않아야합니다.
  - 바깥 영역의 것이 변경되더라도 바뀌지 않습니다.
- 어플리케이션에 비종속적인 Business Rule들을 Entity Object에 넣고, Interactor가 Entity Object를 컨트롤합니다.
- 입력값과 출력값을 다른 UseCase에 전달할 방법을 정합니다.
  - 해당 방법은 인터페이스를 사용합니다.

### UseCase

- UseCase 계층은 Application 고유의 Business Rule을 포함하며 시스템의 모든 UseCase를 캡슐화하고 구현합니다.
- UseCase들은 Entity로 향하는 혹은 Entity로 부터 나오는 데이터 흐름을 조정합니다.
- UseCase 계층의 변경이 보다 내부의 영역인 Entity에 영향을 주지 않아야 하며, 보다 외부의 영역인 Database, UI, Framework의 변경으로부터 영향을 받지 않아야합니다.

### Interface Adapter

- Interface Adapter 계층의 소프트웨어는 UseCase와 Entity에서 사용하기 편한 형식을 외부의 것에서 사용하기 편한 형식으로 변환해주는 adapter의 집합입니다. (e.g. Database, web etc)
- 예를 들어, GUI의 MVC Architecture를 완전히 내포하는 레이어입니다.
  - Model은 Controller에서 UseCase로 전달된 후, UseCase에서 View로 되돌아가는 DataStructure일 가능성이 높습니다.

### Framework and Driver

- 가장 바깥 계층으로, Database나 Web Framework 등과 같은 툴들 과 프레임워크들로 구성되어져 있습니다.



### Reference

- [The Clean Architecture - Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

- [The Clean Architecture - Uncle Bob (한글 번역)](https://blog.coderifleman.com/2017/12/18/the-clean-architecture/?utm_medium=social&utm_source=gaerae.com&utm_campaign=개발자스럽다)

- [Robert C. Martin - Clean Architecture and Design](https://amara.org/ko/videos/0AtjY87egE3m/url/1216370/?tab=video)

- [안드로이드에 Clean Architecture 적용하기](https://academy.realm.io/kr/posts/clean-architecture-in-android/)

- [아키텍처와 의존성](https://blog.appkr.dev/learn-n-think/clean-architecture-and-dependency/)

  

- [Introducing Clean Swift Architecture (VIP)](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf)

  

  

- https://github.com/sergdort/CleanArchitectureRxSwift

- https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html

- https://blog.coderifleman.com/2017/12/18/the-clean-architecture/

- https://medium.com/@younghyun/clean-architecture-part-2-the-clean-architecture-3e2666cdce83

- https://academy.realm.io/kr/posts/converting-an-app-to-use-clean-architecture/

- https://www.youtube.com/watch?v=Nsjsiz2A9mg&t=1s

- https://www.youtube.com/watch?v=Nltqi7ODZTM

- https://www.youtube.com/watch?v=o_TH-Y78tt4&t=1113s

