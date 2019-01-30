# Carthage

## Overview

Carthage란 무엇인가, Cocoapod과의 차이, Carthage를 적용하는 방법에 대하여 기술하였습니다.

## Table of Contents

- [Dependency Manager](#Dependency-Manager)
- [Cocoapod](#Cocoapod)
- [Carthage](#Carthage)
- [Apply Carthage(feat. RxSwift)](#Apply-Carthage(feat.-RxSwift))
- [.gitignore](#.gitignore)
- [Refrence](#Reference)

# Dependency Manager



# Cocoapod

https://github.com/CocoaPods/CocoaPods



# Carthage

https://github.com/Carthage/Carthage



## Apply Carthage(feat. RxSwift)

#### 1. [Installing Carthage](https://github.com/Carthage/Carthage#installing-carthage)

Carthage를 설치하는 여러 방법이 존재합니다. 이 게시물은 **Homebrew**를 이용한 설치에 대한 내용입니다.

Terminal에서 아래와 같은 코드를 넣으면 carthage의 설치가 끝납니다.

```
$ brew update
$ brew install carthage
```

하지만, 저의 경우 아래와 같은 에러가 나왔습니다.

```
An unexpected error occurred during the `brew link` step
The formula built, but is not symlinked into /usr/local
Permission denied @ dir_s_mkdir - /usr/local/share/fish/vendor_completions.d
Error: Permission denied @ dir_s_mkdir - /usr/local/share/fish/vendor_completions.d
```

위 에러에 대한 해결방법입니다. (참고 : https://stackoverflow.com/questions/47513024/how-to-fix-permissions-on-home-brew-on-macos-high-sierra)

```
$ sudo mkdir /usr/local/Frameworks
$ sudo chown $(whoami):admin /usr/local/Frameworks    
$ brew link carthage
```

#### 2. [Adding frameworks to an application fo iOS, tvOS, or watchOS](https://github.com/Carthage/Carthage#if-youre-building-for-ios-tvos-or-watchos)

1. Cartfile 생성

   적용하고자 하는 프로젝트의 root로 이동한 후, Cartfile을 생성해줍니다.

   ```
   $ touch Cartfile
   ```

2. Cartfile 수정

   Cartfile을 Xcode에서 열어줍니다.

   ```
   $ open -a Xcode Cartfile
   ```

   사용하고자 하는 Carthage를 Cartfile에 추가합니다.

   ```
   //Cartfile
   github "ReactiveX/RxSwift" ~> 4.0
   ```

3. `carthage update` 를 실행하면, 종속성을 가져 와서 각 폴더를 작성하거나 사전 컴파일된 프레임 워크를 다운로드합니다. `carthage update --platform ios` 를 이용하여 iOS만 빌드할 수 있습니다.

4. Carthage/Build 폴더에서 framework를 "Linked Frameworks and Libraries"에 추가해줍니다. 
   ![](images/1.LinkedFrameworksAndLibraries.png)

5. **Run Script Phase** 를 수정합니다.

   ```
   /usr/local/bin/carthage copy-frameworks
   
   $(SRCROOT)/Carthage/Build/iOS/<FRAMEWORK NAME>.framework
   ```

   ![](images/2.RunScriptPhase.png)

6. **Unit test or a framework**

   ![](images/3.UnitTestOrFramework.png)

   ![](images/4.UnitTestOrFramework2.png)

## .gitignore

```
# Created by https://www.gitignore.io/api/xcode,carthage,cocoapods,swiftpackagemanager
# Edit at https://www.gitignore.io/?templates=xcode,carthage,cocoapods,swiftpackagemanager

### Carthage ###
# Carthage
#
# Add this line if you want to avoid checking in source code from Carthage dependencies.
# Carthage/Checkouts

Carthage/Build
Carthage/Checkouts/
```

## Reference

- https://www.raywenderlich.com/416-carthage-tutorial-getting-started
- https://www.mokacoding.com/blog/setting-up-testing-libraries-with-carthage-xcode7/