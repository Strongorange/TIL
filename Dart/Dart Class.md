# Dart Class

## Basic

`Flutter`는 모든게 `Class`이다.

```dart
// Class 내에서는 타입을 꼭 명시해줘야한다.
class Player {
    final String name = 'chan'; // 수정 불가능한 변수
    int xp = 150;
}

var player = Player(); // new 키워드는 생략가능
print(player.xp); // 150
player.xp = 300;
print(player.xp); // 300
player.name = 'chan2'; // final 키워드이기 때문에 에러 발생
```

Class내에서의 변수는 타입을 명시해줘야하며 `final` 키워드를 사용하면 수정이 불가능하다.

```dart
class Player {
    final String name = 'chan'; // 수정 불가능한 변수
    int xp = 150;

    void sayHello() {
        print('Hi my name is $name'); // 메소드 안에서는 this 키워드를 사용하지 않아도 된다!!!!!!!.
    }
}
```

Dart의 Class안의 메소드에서는 `this` 키워드를 사용하지 않아도 된다.

## Constructors

### Positional Constructor

위 Player Class는 모든 플레이어가 같은 이름과 경험치를 가지고있다. 이를 해결하기 위해 생성자를 사용한다.

```dart
class Player {
    // 이후에 값을 할당해줄 것이기 때문에 late 키워드를 사용한다.
    late final String name;
    late int xp;

    // 생성자, 생성사는 Class 이름과 동일해야한다.
    Player(String name, int xp) {
        this.name = name;
        this.xp = xp;
    }
}

var player = Player('chan', 150);
var player2 = Player('chan2', 300);
```

위 코드는 가장 기본적인 Class와 Constructor이고 반복되는 부분이 많다. 이를 해결하기 위해 Constructor를 간소화 할 수 있다.

```dart
class Player {
    // late 키워드 삭제
    final String name;
    int xp;

    // 첫번째 인자로 들어오는 것을 name에 할당하고, 두번째 인자로 들어오는 것을 xp에 할당한다.
    Player(this.name, this.xp);
}

var player = Player('chan', 150);
var player2 = Player('chan2', 300);
```

### Named Constructor parameter

위의 Constructor는 위치에 따라서 인자를 넣어줘야한다. 이를 해결하기 위해 Named Constructor를 사용한다.

```dart
class Player {
    // late 키워드 삭제
    final String name;
    int xp;
    String team;
    int age;

    // parameter의 위치가 중요하다.
    Player(this.name, this.xp, thisl.team, this.age);
}
```

함수와 마찬가지로 인자들이 많아진다면 숫서를 헷갈릴 수 있고 가독성이 떨어진다. 이를 해결하기 위해 Named Constructor를 사용한다.

```dart
class Player {
    // late 키워드 삭제
    final String name;
    int xp;
    String team;
    int age;

    // `{}` 안에 있는 인자들은 순서가 상관없다.
    Player({required this.name,required this.xp, required thisl.team, required this.age,});
}

// 생성
player = Player(
    name: 'chan',
    xp: 150,
    team: 'red',
    age: 20,
);
```

## Named Constructor

만약 기본 생성자를 사용하지 않고, 여러 옵션의 다른 생성자를 사용하고 싶다면 Named Constructor를 사용한다.

```dart
class Player {
    final String name;
    int xp;
    String team;
    int age;

    // 기본 생성자
    Player({required this.name,required this.xp, required thisl.team, required this.age,});

    // Named Constructor
    Player.createBluePlayer({required String name, required int age,}) :
    this.age = age,
    this.name = name,
    this.xp = 0,
    this.team = 'blue';


    Player.createRedPlayer({required String name, required int age,}) :
    this.age = age,
    this.name = name,
    this.xp = 0,
    this.team = 'red';
}
```

`:` 을 용하여 Class의 변수들을 초기화 할 수 있다.

### API에서 데이터를 받아 Class로 만들기

```dart
int main() {
    var apiData = [
        {
            'name': 'chan',
            'xp': 150,
            'team': 'red',
        },
        {
            'name': 'chan2',
            'xp': 300,
            'team': 'blue',
        },
         {
            'name': 'chan3',
            'xp': 450,
            'team': 'orange',
        },

    ];

    ]
}
```

fromJson() 메소드를 사용하여 API에서 받아온 데이터를 Class로 만들 수 있다.

```dart
class Player {
    final String name;
    int xp;
    String team;

    Player.fromJson(Map<String, dynamic> playerJson) :
    // : 사용해서 property 초기화
    name = playerJson['name'],
    xp = playerJson['xp'],
    team = playerJson['team'];

    sayHello() {
        return 'Hi my name is $name';
    }
}

void main() {
var apiData = [
        {
            'name': 'chan',
            'xp': 150,
            'team': 'red',
        },
        {
            'name': 'chan2',
            'xp': 300,
            'team': 'blue',
        },
         {
            'name': 'chan3',
            'xp': 450,
            'team': 'orange',
        },

    ];

apiData.forEach((playerJson) {
    var player = Player.fromJson(playerJson);
    print(player.sayHello());
 });
    // Hi my name is chan
    // Hi my name is chan2
    // Hi my name is chan3
}
```

## Cascade Notation

반복되는 코드를 줄이기 위해 Cascade Notation을 사용한다.

```dart
void main() {
    var player = Player(name: 'chan', xp: 150, team: 'red', age: 20);
    player.name = 'chan2';
    plyaer.xp = 300;
    plyaer.team = 'blue';
}

// 위 코드를 아래와 같이 줄일 수 있다.

void main() {
    var player = Player(name: 'chan', xp: 150, team: 'red', age: 20)
    ..name = 'chan2'
    ..xp = 300
    ..team = 'blue';
    ..sayHello(); // 이렇게 메소드도 사용할 수 있다.
}
```

클래스 뒤에서 `..` 으로 사용해서 바로 Class 접근가능하다.  
첫번째 `.`는 Class를 두번째 `.`는 Class의 property를 의미한다.

## ENUM

Enum은 상수를 정의할 때 사용한다. 실수를 줄이고 가독성을 높일 수 있다.

```dart
enum Team {
    red,
    blue,
    orange,
}

class Player {
...
Team team; // Team enum을 사용한다.
...
}

// Team. 을 사용해 Team의 상수를 사용할 수 있다.
var player = Player(name: 'chan', xp: 150, team: Team.red, age: 20);
```

## Abstract Class

Abstract Class는 상속을 통해 사용할 수 있는 Class이다.

```dart
abstract class Human {
    // 직접 구현하지 않고, 상속받은 Class에서 구현한다.
    // 리턴 값과 이름만 정의한다.
    void walk();
}

class Player extends Human {
    ...
    // @override를 사용하여 상속받은 Class에서 구현해야한다.
    @override
    void walk() {
        print('Player is walking');
    }
    ...
}

class Coach extends Human {
    @override
    void walk() {
        print('Coach is walking');
    }
}
```

## Inheritance

```dart
class Human {
    final String name;
    Human(this.name);
    void sayHello() {
        print('Hi my name is $name');
    }
}

enum Team {blue, red}

class Player extends Human {
    final Team team;

    @override
    void sayHello() {
        // super를 사용하여 부모 Class의 메소드를 호출한다.
        super.sayHello();
        print('I am a player of $team team');
    }

    Player({
        required this.team,
        required String name,
    }) : super(name); // super를 사용하여 부모 Class의 생성자를 호출한다.
}

var player = Player(team: Team.blue);
```

## Mixin

Mixin이란 생성자가 없는 Class이다.  
클래스에 프로퍼티를 추가할때 유용하다.

```dart
class Strong {
    final double strenthLevel = 1500.99;
}

class QuickRunner {
    void runQuick() {
        print('ruuuuuuun!');
    }
}

class Tall {
    final double height = 1.99;
}

// with를 사용하여 Mixin을 사용해 Clsss의 프로퍼티를 추가한다.
class Player with String, QuciRunner, Tall {
    final String name;
    Player(this.name);
}

class Horse with String, QuickRunner {};
```

extend를 사용하면 상위 클래스가 생기고 super를 사용해서 접근이 가능하지만 Mixin은 단순히 내부 프로퍼티와 메소드들을 가져온다.
Mix인의 조건은 생성자가 없는 클래스이다
