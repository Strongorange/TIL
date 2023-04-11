# Dart Variables

## Dart VM

JIT, AOT 컴파일러 개발중에는 JIT 배포에서는 AOT
두 컴파일러를 모두 지원해 좋은 개발 경험 제공 가능

## Flutter & Dart의 관계

둘 다 구글이 만들었기 때문에 둘은 밀접한 관계를 가짐
React팀은 JS를 수정할 수 없으나 Flutter팀은 Dart를 수정할 수 있음

# Variables of Dart

## var

var는 타입을 추론해줌 JS와 다르게 선언된 변수에 다른 타입의 변수를 넣을 수 없음  
함수나 메소드 내부에서 지역 변수를 선언할 때 var

## type 지정

class에서 변수나 property를 선얼할 때에는 타입을 지정해서 선언해줌

## dynamic

여러 타입을 가질 수 있는 변수를 선언할 때 사용 **추천되지는 않지만** 사용할 수는 있음
dynamic tempD처럼 명시적으로 선언할 수 있고 var tempD; 처럼 선언할 수도 있음

```dart
    var tempD;
    dynamic tempD2;

    if(tempD is String){
        print(tempD);
    }
```

if문 안에서는 String 메소드를 사용 가능

## null safety

개발자가 null값을 참조하지 못하게 함 참조시 런타임 에러 발생  
dart에서는 기본적으로 변수들은 non-nullable임  
dart에서는 어떤 변수가 null이 될 수 있는지 정해줄 수 있음

```dart
        String? temp;
        temp = null;
        print(temp);
```

temp가 null이 될 수 있다고 명시해줌

사용할땐 js같이 ?를 붙혀서 null 검증을 짧게 할 수 있음

```dart
        String? temp;
        temp = null;
        print(temp?.length);
```

## final

JS의 const와 비슷한 개념으로 한번 선언하면 변경할 수 없음

```dart
    final temp = 1;
    temp = 2; // error

    final String tempSt = "hello";
```

타입 명시릃 해줄 수도 있고 안해줄 수도 있음
안해줘도 dart 스스로 타입을 추론해줌

## late

```dart
    late final String name;
    // do something, go to api
    print(name); // error (unassigned variable)
    name = 'chanhwi';
```

초기값 없이 변수를 먼저 선언할 수 있게 해줌 flutter에서 데이터 fetching에서 유용하게 사용됨

## const

JS의 const와는 다름 JS의 constㄴ는 final과 비슷  
dart의 const는 **compile-time constant**를 만들어줌

```dart
    const API = 12122;
```

temp변수는 compile-time에서 인식되고 컴파일러가 알고있는 값
API에서 오거나 사용자가 화면에서 누르는 값들은 미리 알고있지 못 하므로 var로 선언해야함

max_allowed_price 같은 미리 설정된 값들은 const로 선언해줄 수 있음

## Recap

함수 내부에서는 var로 선언하고 class에서는 type을 명시하는 스타일을 권장하고 있음
