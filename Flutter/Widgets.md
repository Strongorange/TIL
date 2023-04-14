## Expanded 위젯

## Flexible 위젯

React Native의 flex 속성같이 화면의 비율을 지정해 위젯의 크기를 지정할 수 있습니다.  
이를 통해 고정된 px값이 아닌 기기마다 동일한 너비의 위젯을 만들 수 있습니다.

## context 에서 ThemeDate 가져와 사용하기

```dart
class App extends StatelessWidget {
  const App({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSwatch(primarySwatch: Colors.blue)
            .copyWith(background: const Color(0xFFE7626C)),
        textTheme: const TextTheme(
            displayLarge: TextStyle(
          color: Color(0xFF232B55),
        )),
        cardColor: const Color(0xFFF4EDDB),
      ),
      home: const Scaffold(
        body: HomeScreen(),
      ),
    );
  }
}
```

가장 상위 App Widget에서 ThemeData를 설정해줍니다.  
ThemeData에는 다양한 속성들이 들어가며 이를 통해 앱 전체의 테마를 설정할 수 있습니다.  
ThemeData를 사용하기 위해서는 context를 통해 가져와야 합니다.

```dart
Scaffold(
        backgroundColor: Theme.of(context).colorScheme.background,
        ...

child: Container(
            decoration: BoxDecoration(
            color: Theme.of(context).cardColor,
```

Theme.of(context)를 통해 ThemeData를 가져올 수 있습니다.

---
