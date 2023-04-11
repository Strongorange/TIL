# Dart Data Types

## Dart의 데이터 기본 타입

- String
- bool
- int
- double
- num

모든 데이터 타입은 object, class로 구현되어 있음

## Lists

JS의 배열

```dart
    List<int> nums = [1,2,3,4,5]; // class에서 타입 명시
    var nums2 = [1,2,3,4,5]; // 가능할때는 var
```

List의 맨 마지막에 쉼표를 붙히면 자동 정렬됨

```dart
    var nums3 = [
        1,
        2,
        3,
        4,
        5,
    ];
```

### collection if

```dart
    var giveMeFive = true;
    var nums4 = [
        1,
        2,
        if(giveMeFive) 5,
    ];
```

처럼 조건문을 넣을 수 있음

## String interpolation

### 단순 출력

```dart
    var name= 'chan';
    var gretting = 'hello $name';

    print(gretting); // hello chan
```

JS의 `${}`과 비슷한 개념 $만 사용해서 변수 출력 가능  
 큰 따옴표 ", 작은 따옴표 ' 모두 사용 가능

### 계산해서 출력

```dart
    var name= 'chan';
    var gretting = 'hello ${name.toUpperCase()}';

    print(gretting); // hello CHAN
```

JS의 `${}`와 같은 개념

## Collection for

```dart
    var oldFriends = ['chan', 'jung', 'min'];
    var newFriends = ['jung' , 'min', 'hyun', for(var friend in oldFriends) "❤️ $friend"];
    print(newFriends); // [jung, min, hyun, ❤️ chan, ❤️ jung, ❤️ min]
```

## Maps

`JS의 Object`

```dart
    var player = {
        'name': 'chan',
        'age': 25,
        'height': 180,
        'xp' : 19.99,
    };
```

Dart에서 모든 것은 Object이기 때문에 이는 Typescript의 any와 비슷한 무엇이든지 될 수 있음

```dart
    Map<int, bool> {
        1: true,
        2: false,
    }
```

처럼 key와 value의 타입을 명시할 수 있음  
API에서 가져오는 데이터 같이 List안에 Map들이 있는 형식도 만들 수 있지만 그런 데이터를 만들때는 class를 사용하는게 좋음

## Set

Set의 모든 값은 unique해야함

```dart
    var set = {1,2,3,4,5};
    Set<int> numbers = {1,2,3,4,5}; // 명시적 선언
    print(set); // {1, 2, 3, 4, 5}
```

```dart
    var set = {1,2,3,4,5,1,2,3,4,5};
    print(set); // {1, 2, 3, 4, 5} // 모든 값이 unique
```
