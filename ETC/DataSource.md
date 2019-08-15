# Data Sources

## Overview

- `DataSource`는 UI의 제어를 위임하는 대신, **데이터 제어를 위임**한다는 점을 제외하면 `Delegate`와 같습니다.
  -  `DataSource`는 `NSView` 와 `UIView` objects (가시적인 데이터의 행으로 부터 source를 필요로 하는 Table view와 Outline view)를 보유한 outlet입니다.
  - View의 data source는 일반적으로 delegate의 역할을 하는 동일한 객체일 수도 있지만, 모든 객체일 수도 있습니다.
  - `DataSource` 는 view에 필요로하는 data를 제공하기 위하여, 그리고 advanced implmentation에서 유저가 직접적으로 변경한 데이터를 처리하기 위하여 하나 이상의 informal protocol method를 구현해야한다. 
- `DataSource`는 데이터를 요청하는 객체로 부터 메세지를 받기 위하여 존재해야 하는 객체입니다.
- `DataSource`는 UI객체에 전달하는 객체의 지속성에 대한 책임을 가지고 있습니다.
  - 즉, 그러한 object들의 memory management에 책임이 있습니다.
- View object (outline view, table view와 같은) 가 datasource의 데이터에 접근할 때마다, 데이터를 사용하는 한 객체를 보존한다. 

### Reference

- https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html

