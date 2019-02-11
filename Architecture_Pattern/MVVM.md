# MVVM

## Overview

**View의 추상화**를 만드는 것이 핵심입니다. View를 추상화하게 된다면 **Reusable**하며 **Testable**합니다.

#### Model

- 데이터, 비지니스 논리, 서비스 클라이언트 등으로 구성됩니다.

#### View

- 선언적으로 구성된 UI 요소들을 의미합니다.
- Application에서 View는 UI플랫폼(UIKit)이 제공하는 컨트롤들(UILabel, UITextField, UIButton 등)을 조합해 사용자들에게 시각적으로 접근합니다.

#### View Model

- View Model이라는 이름은 "뷰의 모형"을 뜻합니다. "뷰의 모형"은 즉 "추상화된 뷰"라는 의미입니다.

- View를 추상화하기 위하여 추상화된 **View State**를 유지합니다.

  > 예를 들어, View Model은 읽기와 쓰기가 가능한 문자열 속성을 통하여 텍스트 입력 컨트롤(UITextField)을 추상화합니다.





