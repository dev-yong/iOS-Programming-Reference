# MVVM

## Overview

**View의 추상화**를 만드는 것이 핵심입니다. View를 추상화하게 된다면 **Reusable**하며 **Testable**합니다.

### Model

- 데이터, 비지니스 논리, 서비스 클라이언트 등으로 구성됩니다.

### View

- 시각적인 UI 요소들을 의미합니다.
- 종종 Tool에 의하여 declarative하게 정의됩니다.
- Application에서 View는 UI플랫폼(UIKit)이 제공하는 컨트롤들(UILabel, UITextField, UIButton 등)을 조합해 사용자들에게 시각적으로 접근합니다.
- View는 자신의 상태를 사용자에게 표현할 뿐 아니라, 사용자가 Application에 명령을 내릴 수단을 제공합니다.

### View Model

- View Model이라는 이름은 "뷰의 모형"을 뜻합니다. "뷰의 모형(Model of a View)"은 즉 "추상화된 뷰(Abstraction of the View)"라는 의미입니다.

- View를 추상화하기 위하여 추상화된 **View State**를 유지합니다.
  - 예를 들어, View Model은 읽기와 쓰기가 가능한 문자열 속성을 통하여 텍스트 입력 컨트롤(UITextField)을 추상화합니다.
  - 데이터 목록을 보여주는 컨트롤(UITableView, UICollectionView)에 대해서는 각 요소의 View State가 들어있는 컬렉션이 사용됩니다.

- Model과 연관된 값에 대하여 View에 보여줄 때, 값의 변환을 위해 **Value Converter**를 갖습니다.
  - 모델이 제공하는 정보가 사용자에게 전달될 때, 혹은 그 반대의 경우 값이 그대로 사용되기도 하지만 그렇지 않은 경우도 존재합니다.
  - 예를 들어, `2018-02-11 22:54:01 +0000` 와 같은 값을 `1년 전` 과 같은 표현으로 변환합니다.

- View와 사용자간의 관계(View 상태의 표현 및 사용자 interaction)를 위하여 View Model은 **Command**를 갖습니다.

  - 사용자는 Command를 통하여 Model의 행위를 실행할 수 있습니다.

- **View State**, **Value Converter**, **Command**를 통하여 View Model은 "추상화된 뷰(뷰의 모델)"가 됩니다.
  - 따라서 개념적으로 사용자와 소통하게 됩니다.



개념적 공간에서 

1. 사용자는 View Model의 속성을 이용하여 정보를 입력하고
2. 사용자는 View Model의 Command를 실행시키며
3. View Model은 자신의 속성을 갱신해 Command의 결과와 Appliation 상태변화를 사용자에게 표현합니다.



로그인 화면을 예로 들어봅시다. 

- 로그인 화면을 추상화하는 View Model은 이메일과 비밀번호에 대응하는 쓰기가 가능한 문자열  속성 2가지와
- 로그인 버튼에 대응하는 Command를 갖게 됩니다.
- 만약 서비스(모델)가 로그인 실패 코드를 반환한다면, 이 실패 코드는 Value Converter에 의하여 사용자 친화적 메세지로 변환되고,
- 읽기 전용 문자열 속성을 통하여 출력됩니다.  
- 이제 추상화된 View(View Model)와 물리적인 View(View)를 연결해 줄 수단이 필요합니다.
  - MVC 패턴의 경우 Controller가 View와 Model 사이의 작업흐름을 제어할 것입니다.
  - MVVM 패턴에서는 작업 흐름의 제어보다는 View와 View Model의 상태를 동기화해줄 구성요소가 필요합니다. 바로 이것을 **Data binding**이라고 합니다.



MVVM 패턴은 완성도 높은 Data binding 구조에 의존하게 됩니다.

Data binding으로 인하여 View Model의 상태가 변경되면 View의 상태가 함께 변경이되며, 그 역이 보장됩니다.



추상화는 구체화에 대한 지식을 가지지 않기 때문에 View Model은 View에 대하여 알지 못합니다. 다른 관점에서 보면, View Model이 플랫폼에 대하여 독립적으로 재사용될 수 있음을 뜻합니다. Unit Test를 통하여 검증이 쉬우며 TDD의 도입 또한 용이합니다.

> 추상화(abstraction)
>
> Unit Test, TDD
>
> Dependency Insection, Dependency Inversion principle

## Reference

- https://blog.weirdx.io/post/39547
- https://blogs.msdn.microsoft.com/johngossman/2005/10/08/introduction-to-modelviewviewmodel-pattern-for-building-wpf-apps/



