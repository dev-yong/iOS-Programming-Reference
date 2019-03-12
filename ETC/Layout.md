# Layout

## Table of Contents

- [`updateConstraints()`](#updateConstraints)
- [`updateConstraintsIfNeeded()`](#updateConstraintsIfNeeded)
- [`setNeedsUpdateConstraints()`](#setNeedsUpdateConstraints)
- [`layoutSubview()`](#layoutSubviews)
- [`layoutIfNeeded()`](#layoutIfNeeded)
- [`setNeedsLayout()`](#setNeedsLayout)

## `updateConstraints()`

- View의 constraints를 업데이트합니다.

- Constraints의 변화를 최적화하기 위하여 이 메소드를 오버라이드합니다.

  > 영향을 받는 변경이 발생한 직후에 즉각적으로 constraint를 업데이트하는 것이 깔끔하고 쉽습니다.
  >
  > 예를 들어, 버튼 탭에 대한 응답으로 constraint를 변경하기를 원할 때, 버튼의 action method에서 직접 변경합니다.
  >
  > Constraints의 변화가 너무 느리거나 View가 중복적인 변화를 생산할 때에만 오버라이드 해야합니다.

- 변화를 스케쥴하려면, View에서  `setNeesUpdateConstraints()` 를 호출합니다.

- 시스템은 layout이 발생하기 이전에 `updateConstraints()` 의 구현을 호출합니다.

  - 커스텀 뷰의 속성이 변경되지 않은 경우, 콘텐츠에 필요한 모든 constraints가 적용되는지 확인할 수 있습니다.

- 구현은 가능한한 효율적이여야합니다.

  - Do not deactivate all your constraints, then reactivate the ones you need. 

    > 모든 제약 조건을 비활성화하지 말고 필요한 제약 조건을 다시 활성화합니다…??

  - Constraints를 추적하고 각 업데이트가 진행되는 동안 유효성을 검사 할 수있는 방법이 있어야합니다.

  - **변경해야 하는 항목만 변경합니다.**

  - 업데이트가 진행되는 동안 앱의 현재 상태에 대해 적절한 constraints를 반드시 갖추어야 합니다.

- **`setNeedsUpdateConstraints()` 를 구현의 내부에 호출하면 안됩니다.**

  - `setNeedsUpdateConstraints()`를 호출하면 또 다른 업데이트 단계가 스케쥴되어 피드백 루프가 생성됩니다.

- **`super.updateConstraints()` 를 구현의 마지막에 호출합니다.**

## `updateConstraintsIfNeeded()`

- **수신 View 및 해당 SubView에 대한 constraints를 업데이트합**니다.
- 새로운 Layout 단계가 뷰에 대해 발생될 때마다, 시스템은 view와 subview의 constraints이 현재 view 계층 구조 및 해당 constraints로 업데이트되도록 이 메소드를 호출합니다.
  - 이 메소드는 시스템에 의해 자동으로 호출되지만 가장 최신의 constraints를 검사해야하는 경우 수동으로 호출 할 수 있습니다.
- **Subclass는 이 메소드를 오버라이드하면 안됩니다.**

## `setNeedsUpdateConstraints()`

- **View의 constraints를 업데이트해야하는 가를 컨트롤**합니다.
- Constraints에 영향을 미치는 식으로 커스텀 View의 속성이 변경이 된다면, 이 메소드를 호출하여 **미래의 어떤 시점에서 constraints를 업데이트해야 함**을 나타낼 수 있습니다.
  - 시스템은 `updateConstriants()` 를 정상적인 레이아웃 단계의 일부로 호출할 것입니다.
- **Constraint의 변화들을 일괄처리하기 위한 최적화 도구로 사용**합니다.
  - Constraints가 필요하기 직전에 모든 constraints를 한번에 업데이트하면, Layout 단계간 View를 여러번 변경할 때 불필요하게 constraints를 재계산하지 않습니다.

## `layoutSubviews()`

- Subclass들은 그들의 subview에 대한 정교한 레이아웃을 실행하기 위하여 이 메소드를 필요에 따라 오버라이드할 수 있습니다.
- Subview의 Autoresigin과 Constraint-based 행위가 원하는대로 동작하지 않을 경우 이 메소드를 오버라이드해야합니다.
- 구현을 통하여 **subview의 frame 사각형을 직접적으로 설정**할 수 있습니다.
- **이 메소드를 직접적으로 호출하면 안됩니다.**
  - Layout을 강제적으로 업데이트하고 싶다면, 다음 드로잉 업데이트 이전에  `setNeedsLayout()` 을 호출하면 됩니다.
  - View들의 Layout을 즉각적으로 업데이트하고 싶다면, `layoutIfNeeded()` 를 호출하면 됩니다.
  - 이 메소드가 호출 될때  `updateConstraintsIfNeed()` 또한 호출됩니다.

## `layoutIfNeeded()`

- **View의 Layout을 즉각적으로 업데이트를 강압**합니다.
- Auto Layout을 사용 중일 때, Layout Engine은 constraints의 변화를 충족시키기 위해 필요에 따라 view의 위치를 업데이트합니다.
- Root View로부터 메세지를 받는 View를 이용하여, Root부터 시작하여 View의 Subtree들을 배치합니다.
- 만약 Layout 업데이트가 팬딩상태인 것이 없을 경우, layout을 변경하거나 layout 관련 callback을 호출하지 않고 종료합니다.
- **synchronous합니다.**

## `setNeedsLayout()`

- Receiver의 현재 Layout을 무효화하고 다음 업데이트 사이클 동안 Layout 업데이트를 발생시킵니다.
- View의 subview들에 대한 레이아웃을 조정하려면 Main thread에서 이 메소드를 호출하면 됩니다.
- 이 메소드는 요청을 기록하고 즉각적으로 반환합니다.
- **즉각적인 업데이트를 강압하지 않고 대신 다음 업데이트 사이클을 기다립니다.**
  - 그 때문에, View들을 업데이트하기 이전에, 여러 View들의 Layout을 무효화할 수 있습니다.
  - 이 동작을 통해 모든 Layout 업데이트를 하나의 업데이트 주기로 통합 할 수 있습니다.
    - 일반적으로 성능향상에 도움이 됩니다.

## Reference

- https://developer.apple.com/documentation/uikit/uiview/1622482-layoutsubviews
- https://developer.apple.com/documentation/uikit/uiview/1622507-layoutifneeded
- https://developer.apple.com/documentation/uikit/uiview/1622601-setneedslayout
- https://developer.apple.com/documentation/uikit/uiview/1622512-updateconstraints
- https://developer.apple.com/documentation/uikit/uiview/1622595-updateconstraintsifneeded
- https://developer.apple.com/documentation/uikit/uiview/1622450-setneedsupdateconstraints
- https://stackoverflow.com/questions/20609206/setneedslayout-vs-setneedsupdateconstraints-and-layoutifneeded-vs-updateconstra
- https://www.objc.io/issues/3-views/advanced-auto-layout-toolbox/