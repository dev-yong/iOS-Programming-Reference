# Coding Style

- Method

  - 조동사 + 동사원형 : `didFinish`, `willAppear`, `didComplete`

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

    - supports: ~을 지원하는가?
    - includes: ~을 포함하는가?
    - shows: ~을 보여줄 것인가?
    - allows: ~을 허용할 것인가?
    - accepts: ~을 받아 주는가?
    - contains: ~을 포함하고 있는가?

- ETC

  - 중복 제거

    ```swift
    struct User {
    	//let userID: String
    	let identifier: String
    }
    ```

