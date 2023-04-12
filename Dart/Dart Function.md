# Dart Functions

## Fat Arrow

함수에서 곧 바로 값을 리턴할 때 사용

```dart
    int add(int a, int b) => a + b;
```

## named parameter

### Positional Parameter

```dart
    String sayHello(String name, int age, String country) {
        return 'Hello $name, tou are $age, and you come from $country';
    }

    void main() {
        print(sayHello('chan', 800, 'korea'));
    }
```

위와 같이 함수를 호출할 때, 순서대로 값을 넣어주어야 한다.  
하지만 순서로 값을 넣어야한다면 매개변수가 많아질수록 헷갈릴 수 있다.
이럴때 named parameter를 사용하면 된다.

### Named Parameter

```dart
    String sayHello({String name, int age, String country}) {
        return 'Hello $name, tou are $age, and you come from $country';
    }

    void main() {
        print(sayHello(name: 'chan', age: 800, country: 'korea'));
    }
```

이렇게 함수 선언시에 `{}`를 사용하고, 함수 호출시에 `()`안에 `name: 'chan'`과 같이 값을 넣어주면 된다.

Dart는 기본적으로 non-nullable이기때문에 만약

```dart
    sayHello(name: 'chan'));
```

같이 값을 넣어주지 않는 경우에 에러가 발생할 수 있다. 이를 해결하기위해

</br>

#### 인자 기본값을 넣어주기

```dart
    String sayHello({String name = 'chan', int age = 99, String country = 'wakanda'}) {
        return 'Hello $name, tou are $age, and you come from $country';
    }
```

기본값을 넣어주면 함수가 인자가 전달되지 않았을때 기본값을 사용한다.

#### Required 속성 지정해주기

```dart
String sayHello({required String name, required int age, required String country}) {
    return 'Hello $name, tou are $age, and you come from $country';
}
```

이렇게 함수 선언시에 `required`를 사용하면 함수 호출시에 인자를 넣어주지 않으면 에러가 발생하고 IDE에서도 에러를 알려줘 편리하다.

### Optional Positional Parameter

```dart
    String sayHello(String name, int age, [String? country = 'cuba']) => 'Hello $name, tou are $age, and you come from $country';
```

`[ ]`안에 값을 넣어주고 `?`를 붙여주면 optional parameter가 된다.

## QQ Operator

```dart
String capitalizeName(String name) => name.toUpperCase();

int main() {
    capitalizeName('chan'); // CHAN
}
```

Dart는 기본적으로 non-nullable이기 name 인자도 null이 될 수 없다. 하지만 null이 될 수 있는 경우에는 `?`를 붙여주면 된다.

```dart
String capitalizeName(String? name) => name?.toUpperCase();
```

만약 null이 들어오면 'ANON'을 리턴하고 싶다면

```dart
String capitalizeName(String? name) {
    if (name == null) {
        return 'ANON';
    }
    return name.toUpperCase();
}
```

Fat Arrow를 사용하면

```dart
String capitalizeName(String? name) => name == null ? 'ANON' : name.toUpperCase();
```

QQ Operator를 사용하면 더 줄일 수 있다.

```dart
left ?? right // left가 null이면 right를 리턴하고, null이 아니면 left를 리턴
```

```dart
String capitalizeName(String? name) => name?.toUpperCase() ?? 'ANON';
```

## QQ=

```dart
int? a;
a ??= 10; // a가 null이면 10을 할당하고, null이 아니면 아무것도 하지 않음
a ?? = 20;
print(a); // 10
```

## Type Def

```dart
List<int> reverseListOfNumbers(List<int> list) {
    var reversed = list.reversed;
    return reversed.toList();
};
```

리스트를 받아서 역순으로 정렬한 리스트를 리턴하는 함수이다.  
위 함수에서 `List<int>`타입이 중복되서 나오는데 이는 `typedef`를 사용하면 더 간단하게 표현할 수 있다.

```dart
typedef ListOfInts = List<int>;

ListOfInts reverseListOfNumbers(ListOfInts list) {
    var reversed = list.reversed;
    return reversed.toList();
};
```

이를 응용해서 Map도 타입을 정의할 수 있다.

```dart
typedef UserInfo = Map<String, String>; // key, value 모두 String인 Map

String sayHi(UserInfo userInfo) {
    return 'Hello ${userInfo['name']}';
}
```

하지만 이런식으로 구조화된 data의 형태를 지정하고 싶다면 `Class`를 사용하는 것이 더 좋다.

```dart
sayHi({"sdadsaddsadas": 'chan'});
```

위와같이 사용해도 지정한 타입은 만족하기 때문에 에러가 발생하지 않지만 일반적으로 원하지 않는 값이 들어올 수 있고 `Class`를 사용하면 이를 방지할 수 있다.

type def는 조금 더 작은 단위의 타입 alias 지정할 때 사용한다.
