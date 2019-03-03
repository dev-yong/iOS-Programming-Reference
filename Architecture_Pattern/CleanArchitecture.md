# Clean Architecture

제대로 된 Architecture는 App이 무엇을 위한 것이고, 무엇을 하는 것이지 알 수 있어야합니다.

> ~~"이것이 iOS Application이다"~~를 뜻하는 것이 아닌, 
>
> "이것은 Todo list를 위한 Application이며, Todo에 대한 CRUD를 할 수 있다."를 알아야합니다.

### 의존성 규칙

- 바깥 영역으로 갈수록 HighLevel Software가 됩니다.
  - 바깥 영역은 Mechanism이고, 내부 영역은 Policy입니다.
- **Dependency는 오직 내부**로만 향합니다.
  - 내부의 영역은 외부의 어떤 것도 알아서는 안됩니다.
    - e.g.  `Entity` 는 `UseCase` 에 대해서 알 수 없습니다.

## Reference

- https://github.com/sergdort/CleanArchitectureRxSwift
- https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html
- https://blog.coderifleman.com/2017/12/18/the-clean-architecture/
- https://medium.com/@younghyun/clean-architecture-part-2-the-clean-architecture-3e2666cdce83
- https://academy.realm.io/kr/posts/converting-an-app-to-use-clean-architecture/
- https://www.youtube.com/watch?v=Nsjsiz2A9mg&t=1s
- https://www.youtube.com/watch?v=Nltqi7ODZTM
- https://www.youtube.com/watch?v=o_TH-Y78tt4&t=1113s

