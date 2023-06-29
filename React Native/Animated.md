# React-Native 애니메이션 적용

프로젝트를 진행하며 기능 완성이 어느정도 끝나고 애니메이션을 적용하려고하니
RN에서 애니메이션을 사용한적이 꽤 오래되어서 다시 공부할겸 정리한다.
프로젝트에서 사용한 내용만을 정리했으며 더 자세한 내용은 [공식문서 🌐](https://reactnative.dev/docs/animated)을 참조

## 기본

### Animated Value

애니메이션에서 사용할 값은 `new Animated.Value(기본값)`을 사용해서 선언한다.

#### useRef

```typescript
const animatedValue = useRef(new Animated.Value(1)).current;
```

이때 `useRef`를 사용하는 이유는 컴포넌트의 라이프사이클 동안 값을 유지하기 위해서이다. **즉 컴포넌트가 다시 렌더링될때 animatedValue를 유지해주기 위해서이다.**

#### useRef가 없으면?

```typescript
const animatedValue = new Animated.Value(1);
```

useRef 없이 위처럼 사용시 컴포넌트가 렌더링되면 animatedValue의 값이 다시 1로 바뀌어 애니메이션이 유지되지 않거나 처음부터 다시 실행되버리는 문제가 발생한다.

### Value를 직접 수정하지 않는다

위의 `animatedValue`는 직접 수정하면 안된다.
공식 문서에서도 나와있듯이 Value는 직접 수정하지 말고 Animated의 메소드를 사용해서 애니메이션을 적용한다.
![](https://velog.velcdn.com/images/strongorange/post/640cc7c7-50cf-4593-b841-c9b46bc6ef89/image.png)

### Animatable Component에만 애니메이션 적용 가능

- Animted.Image
- Animated.ScrollView
- Animated.Text
- Animated.View
- Animated.FlatList
- Animated.SectionList

위의 컴포넌트들은 아래와 같이 사용해서 애니메이션 적용가능

```jsx
<Animated.View>{chilren}</Animated.View>
```

위에 없는 컴포넌트들은 `createAnimatedComponent()`를 사용해서 애니메이터블한 컴포넌트로 생성 가능

## Styled-Component와 사용하기

### createAnimatedComponent로 생성

```typescript
const Box = styled(Animated.createAnimatedComponent(View))``;
```

이때 View를 react-native에서 import 해와야한다.

### 생성된 AnimatedComponent 감싸기

```typescript
const Box = styled.TouchableOpacity``;

const AnimatedBox = Animated.createAnimatedComponent(Box)``;
```

생성되는 것은 확인했으나 eslint 문제인지, typescript 문제인지 prop 값들을 잘 못 읽는 문제가 발생했다.

## 애니메이션 적용하기

애니메이션을 적용하기 위해 `decay, spring, timing` 메소드를 사용할 수 있다. 자세한 내용은 공식 문서를 참고하고 `timing, sequence, loop` 만 작성

### timing

```typescript
static timing(value, config): CompositeAnimation;
```

시간에 따른 이벤트를 적용할때 사용하는 메소드

**timing**의 config의 속성은 다음과 같다

- duration: 애니메이션 지속시간 기본 500ms
- easing: Ease 커브를 조정, 기본은 `Easing.inOut`
- delay: 애니메이션 시작까지 걸릴 시간 기본 0ms
- isInteracton: "interaction handle"을 발생시키는지 여부
- useNativeDriver: 애니메이션을 처리를 플랫폼(Android, iOS)에게 넘길 것인지 선택, true로 설정시 애니메이션 처리가 브릿지를 통하지 않아 성능 향상이 있지만 지원되는 애니메이션에서만 사용 가능

```typescript
Animated.timing(animatedValue, {
          toValue: 1.5,
          duration: 1500,
          useNativeDriver: false,
		}),
```

### sequence

```typescript
static sequence(animations: CompositeAnimation[]): CompositeAnimation;
```

주어진 애니메이션을 순서대로 수행하는 메소드

```typescript
Animated.sequence([
  Animated.timing(animatedValue, {
    toValue: 1.5, // Set the end value of the first part of your sequence
    duration: 1500, // Duration of the first part of your sequence
    useNativeDriver: false,
  }),
  Animated.timing(animatedValue, {
    toValue: 1, // Reset to initial scale
    duration: 1500, // Duration of the second part of your sequence
    useNativeDriver: false,
  }),
]);
```

### loop

```typescript
static loop(
  animation: CompositeAnimation[],
  config?: LoopAnimationConfig
): CompositeAnimation;
```

주어진 애니메이션을 게속해서 실행하는 메소드

```typescript
Animated.loop(
  Animated.sequence([
    Animated.timing(animatedValue, {
      toValue: 1.5, // Set the end value of the first part of your sequence
      duration: 1500, // Duration of the first part of your sequence
      useNativeDriver: false,
    }),
    Animated.timing(animatedValue, {
      toValue: 1, // Reset to initial scale
      duration: 1500, // Duration of the second part of your sequence
      useNativeDriver: false,
    }),
  ])
).start();
```

### start

```typescript
static start(callback?: (result: {finished: boolean}) => void);
```

애니메이션은 반드시 start 메소드를 사용해야 적용된다.

### interpolation

만약 컴포넌트의 사이즈가 1에서 2로 바뀔때 컬러를 바꾸고 싶다면 `parallel` 메소드로 여러 애니메이션을 동시에 실행시켜 바꾸게 할 수도 있지만 `interpolation` 을 이용하면 더 간단하고 예측가능한 코드를 작성할 수 있다.

**Interpolation**의 자세한 설명은 [Interpolation with React Native Animations](https://eveningkid.medium.com/interpolation-with-react-native-animations-853e467fe5c1) 글에서 자세히 되어있다.
요약하면 `Animated.Value` 의 범위에 종속적으로 값이 변하는 값을 만드는 기능이다.

```typescript
const animatedValue = useRef(new Animated.Value(1)).current;

const backgroundColorOfComponent = animatedValue.interpolate({
  inputRange: [1, 1.5], // animatedValue가 timing메소드의 config toValue 값으로 바뀌는 범위
  outputRange: ["orange", "blue"], // animatedValue가 1일때 "orange", 1.5 일때 "blue" 1과 1.5 사이로 값이 움직일때 backgroundColorOfComponent 의 값은 "orange" 와 "blue" 사이에서 자동으로 변함
});
```
