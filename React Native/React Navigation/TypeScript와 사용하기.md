# Type Checkiing withe TypeScript

[공식문서](https://reactnavigation.org/docs/typescript/)

## Navigator의 타입 체크하기

`Navigator(Native Stack, Bottom Tab...)`의 Screen에 대한 타입을 지정해주어야한다.

```typescript
type RootStackParamList = {
  Home: undefined;
  Profile: { userId: string };
  Feed: { sort: "latest" | "top" } | undefined;
};
```

`undefined`는 `params`가 없는 경우를 의미한다. 이제 타입을 설정했으니 `Navigator`에 타입을 지정해줘야하는데 이는 `createXNavigator`로 Navigator를 생성할때 `Generic`으로 지정해주면 된다.

```typescript
import { createStackNavigator } from "@react-navigation/stack";

const RootStack = createStackNavigator<RootStackParamList>();
```

이렇게 `RootStack`을 생성하면 `RootStack`의 `Screen`에 대한 타입이 지정되고

```jsx
<RootStack.Navigator initialRouteName="Home">
  <RootStack.Screen name="Home" component={Home} />
  <RootStack.Screen
    name="Profile"
    component={Profile}
    initialParams={{ userId: user.id }}
  />
  <RootStack.Screen name="Feed" component={Feed} />
</RootStack.Navigator>
```

처럼 사용하면 각각 `Screen`에 대한 `params`의 타입이 지정된다.

> type 대신 interface를 사용시 올바르지 않은 route이름도 허용하게되어 버그를 유발할 수 있으니 type을 사용하자

## Screen의 타입 체크하기

`Screen`의 타입을 체크하기 위해서 `navitation` prop과 `route` prop에 주석을 달아줘야한다(need to annotate the `navigation` prop and the `route` prop received by a screen)

이를 위해서 `navigator`는 generic type을 export하는데 이를 통해 `navigation`과 `route`의 타입을 지정해줄 수 있다.

NativeStack Navigator의 Screen에 대한 타입을 지정해보자

```typescript
import type { NativeStackScreenProps } from "@react-navigation/native-stack";

type RootStackParamList = {
  Home: undefined;
  Profile: { userId: string };
  Feed: { sort: "latest" | "top" } | undefined;
};

// Screen의 navigation과 route의 타입을 지정해준다.
type Props = NativeStackScreenProps<RootStackParamList, "Profile">;
// Navigator가 id를 가지는 경우
type Props = NativeStackScreenProps<RootStackParamList, "Profile", "MyStack">;
```

`type`은 3가지 제네릭을 받는다.

- 이전에 만든 Navigator의 ParamList 객체 타입(The param list object we defined earlier)
- Screen이 속한 route의 이름(The name of the route the screen belongs to)
- Navigator가 id를 가진다면 ID(The ID of the navigator (optional))

이를 통해 `navigate`, `push`등을 사용할때 route의 이름과 params의 타입을 체크할 수 있다.  
현재 route의 이름은 `route.params`에 있는 params를 확인하고 `setParams`를 호출할때 사용된다.

```typescript
function ProfileScreen({ route, navigation }: Props) {
  // ...
}
```

`navigation`이나 `route`의 타입은 `Props`를 사용해 다음과 같이도 사용할 수 있다.

```typescript
type ProfileScreenNavigationProp = Props["navigation"];

type ProfileScreenRouteProp = Props["route"];
```

다른 방법으로 `navigation`과 `route`의 타입을 따로 지정해줄 수도 있다.  
`navigation`의 타입 체크를 위해서 `navigator`에서

```typescript
import type { NativeStackNavigationProp } from "@react-navigation/native-stack";

type ProfileScreenNavigationProp = NativeStackNavigationProp<
  RootStackParamList,
  "Profile"
>;
```

`route`의 타입 체크를 위해서 `navigator`에서

```typescript
import type { RouteProp } from "@react-navigation/native";

type ProfileScreenRouteProp = RouteProp<RootStackParamList, "Profile">;
```

## Nesting Navigators
