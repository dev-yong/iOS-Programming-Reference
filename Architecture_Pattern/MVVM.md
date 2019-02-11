# MVVM

## Overview

**View의 추상화**를 만드는 것이 핵심입니다. View를 추상화하게 된다면 **Reusable**하며 **Testable**합니다.

#### Model

- 데이터, 비지니스 논리, 서비스 클라이언트 등으로 구성됩니다.

#### View

- 선언적으로 구성된 UI 요소들을 의미합니다.
- Application에서 View는 UI플랫폼(UIKit)이 제공하는 컨트롤들(UILabel, UITextField, UIButton 등)을 조합해 사용자들에게 시각적으로 접근합니다.
- View는 자신의 상태를 사용자에게 표현할 뿐 아니라, 사용자가 Application에 명령을 내릴 수단을 제공합니다.

#### View Model

- View Model이라는 이름은 "뷰의 모형"을 뜻합니다. "뷰의 모형"은 즉 "추상화된 뷰"라는 의미입니다.
- View를 추상화하기 위하여 추상화된 **View State**를 유지합니다.
  - 예를 들어, View Model은 읽기와 쓰기가 가능한 문자열 속성을 통하여 텍스트 입력 컨트롤(UITextField)을 추상화합니다.
  - 데이터 목록을 보여주는 컨트롤(UITableView, UICollectionView)에 대해서는 각 요소의 View State가 들어있는 컬렉션이 사용됩니다.
- Model과 연관된 값에 대하여 View에 보여줄 때, 값의 변환을 위해 **Value Converter**를 갖습니다.
  - 모델이 제공하는 정보가 사용자에게 전달될 때, 혹은 그 반대의 경우 값이 그대로 사용되기도 하지만 그렇지 않은 경우도 존재합니다.
  - 예를 들어, `2018-02-11 22:54:01 +0000` 와 같은 값을 `1년 전` 과 같은 표현으로 변환합니다.
- View와 사용자간의 관계(View 상태의 표현 및 사용자 interaction)를 위하여 View Model은 **Command**를 갖습니다.
  - Command를 통하여 사용자는 Model의 행위를 실행할 수 있습니다.
- **View State**, **Value Converter**, **Command**를 통하여 View Model은 "추상화된 뷰"가 됩니다.
  - 따라서 개념적으로 사용자와 소통하게 됩니다.

> 추상화(abstraction)이 뜻하는 바가 무엇일까?

## Reference

- https://blog.weirdx.io/post/39547



