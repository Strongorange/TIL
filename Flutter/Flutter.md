# Flutter

> Android, iOS, Web, Desktop, Embedded 등 모든 플랫폼에서 동작하는 UI Framework이다.

## 동작 원리

Flutter는 Dart언어로 작성된 코드를 Native Code로 변환하여 실행한다. 이를 위해 `Dart VM`을 사용한다. 즉 운영체제와 직접적으로 통신하지 않는다. 이러한 특징으로 인해 `Dart VM`이 실행되는 플랫폼에 상관없이 동작한다.

## React Native와의 차이점

React Native는 JavaScript로 작성된 코드를 Native Code로 변환하여 실행한다. 이를 위해 `JavaScriptCore`를 사용한다. 즉 운영체제와 직접적으로 통신한다. 이러한 특징으로 인해 `JavaScriptCore`이 실행되는 플랫폼에 상관없이 동작한다.

반면 Flutter는 `Dart VM`을 사용한다. 즉 운영체제와 직접적으로 통신하지 않는다. 이러한 특징으로 인해 `Dart VM`이 실행되는 플랫폼에 상관없이 동작한다.

**React Native**

- OS상에서 사용 가능한 위젯을 사용하고 싶다면

**Flutter**

- 모든 OS상에서 동일한 UI를 사용하고 싶다면
- 세밀한 디자인 요구사항이 존재
- 요소들이나 애니메이션들을 커스텀하고 싶다면

## Widget

> Flutter에서 UI를 구성하는 기본 단위

[공식 위젯 카탈로그](https://docs.flutter.dev/development/ui/widgets)

Widget 생성은 Class생성과 같음

build() 메소드를 통해 Widget을 반환

화면이 Scaffold(구조)를 가져야 함
