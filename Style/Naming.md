# Naming

## Overview

함수명, 변수명들을 일정 규칙과 문법을 따라서 만든다면, 이름만을 보고 그것들의 역할을 어느정도 유추가 가능할 것입니다.

단어와 문법 선택에 있어 항상 고민을 해왔던 내용들이 노수진님의 [Swift 개발자처럼 변수 이름 짓기](https://soojin.ro/blog/english-for-developers-swift), [Bool 변수 이름 제대로 짓기 위한 최소한의 영어 문법](https://soojin.ro/blog/naming-boolean-variables) 를 읽고 조금이나마 해소가 되어 정리를 해보았습니다.

## Content

- Method

  - 조동사 + 동사원형 

  - ```swift
    func viewWillAppear(_ animated: Bool) //View가 나타날 것이다.
    func viewDidAppear(_ animated: Bool) //View가 나타났다.
    ```

  - getter : intance를 return하는 method는 해당 instance의 type name으로 시작한다.

  - ```swift
    //UIImage
    func pngData() -> Data?
    //Returns the data for the specified image in PNG format
    ```

  - `fetch`, `request`, `perfrom`

    - `fetch` : 실패하지 않는 작업, 결과를 바로 return

    - ```swift
      //PHAsset - Photos Framework
      class func fetchAssets(withLocalIdentifiers identifiers: [String], options: PHFetchOptions?) -> PHFetchResult<PHAsset>
      ```

    - `request` : 실패할 수 있는 작업, 요청을 거절할 수 있는 경우

    - ```swift
      //CLLocationManager
      func requestAlwaysAuthorization()
      //Requests permission to use location services whenever the app is running.
      ```

    - `perform` or `execute` : 작업의 단위가 closure나 requesst로 wrapping되어있는 경우

    - ```swift
      //RunLoop
      func perform(_ block: @escaping () -> Void)
      ```

  - `mutating`, `nonmutating`

    - `mutating` : 동사원형

      ```swift
      mutating func sort() -> Void //in-place sort
      ```

    - `nonmutating` :  ~ed or ~ing

      ```swift
      func sorted() -> [Element] //정렬된 새 배열을 리턴
      ```

- Value

  - 과거분사 : `requestedData`, `hiddenView`

  - 단수, 복수 

    ```swift
    let album: Album
    let albums: [Album]
    ```

- Bool

  - 조동사 + 동사원형

    - can : "~할 수 있는가?"

      ```swift
      var canBecomeFirstResponder: Bool { get } //UIResponder: first responder가 될 수 있는가?
      ```

    - should, will : "~해야하는가?" or "~할 것인가?"

      ```swift
      var shouldRefreshRefetchedObjects: Bool { get set } //NSFetchRequest: 가져온 값을 refresh 할 것인가?
      ```

  - is +

    - 명사 : "(무엇)인가?" 

     ```swift
      func isDescendant(of view: UIView) -> Bool //UIView: "view의 자식인가?" 
     ```

    - ~ing : "~하는 중인가?"

     ```swift
      var isExecuting: Bool { get } //Operation: "오퍼레이션의 작업이 현재 실행 중인가?"
     ```

    - 형용사(자체)

     ```swift
      var isOpaque: Bool { get set } //UIView: "view가 불투명한가?"
     ```

    - 과거분사

     ```swift
      var isSelected: Bool { get set } //UIView: "view가 선택되어졌는가?"
      var isHidden: Bool { get set } //UiView: "view가 숨겨졌는가?
     ```

    - **절대 is + 동사원형을 사용하지말자**

  - 동사 원형 : 3인칭 단수

- ETC

  - 중복 제거

    ```swift
    struct User {
        //(X) User와 userID의 'user'가 중복되었습니다.
    	let userID: String 	
        
        //(O)
    	let identifier: String	
    }
    ```

## Reference

- https://soojin.ro/blog/english-for-developers-swift
- https://soojin.ro/blog/naming-boolean-variables