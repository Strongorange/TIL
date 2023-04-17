# Recoil Selector

## Recoil Selector란?

Recoil 상태의 파생 값을 계산하거나 반환하는데 사용되는 순수 함수이며, 인자로 받은 **상태**를 기반으로 새로운 값을 계산한다. 이렇게 계산된 값은 다른 컴포넌트에서 재사용할 수 있으며 이를 통해 상태의 로직을 중앙화할 수 있다.

## 왜 사용할까?

처음에 Selector 개념에 대해 알게 되었을 때, 상태를 직접 사용하는 것과 상태를 기반으로 계산된 값을 사용하는 것의 차이가 무엇인지 이해하기 어려웠다. 그래서 상태를 직접 사용하는 것과 상태를 기반으로 계산된 값을 사용하는 것의 차이를 알아보고, 왜 Selector를 사용해야 하는지 알아보자.

1. 상태를 직접 사용하는 경우, 상태가 변경될 때마다 컴포넌트가 리렌더링되어 성능이 저하될 수 있다.
2. 상태를 직접 사용하는 경우, 상태를 사용하는 컴포넌트가 여러 개일 경우, 상태가 변경될 때마다 모든 컴포넌트가 리렌더링되어 성능이 저하될 수 있다.

### 예시

사용자 목록을 관리하는 웹 애플리케이션의 각 사용자는 다음과 같은 정보를 가진다

- ID
- 이름
- 나이
- 국가

애플리케이션에서 다음과 같은 기능을 구현하려고 한다.

1. 전체 사용자 목록 표시
2. 성인 사용자 목록 표시
3. 특정 국가의 사용자 목록 표시

이런 경우 Selector를 사용하면 효과적으로 상태와 파생 값을 관리할 수 있다. 먼저 사용자 목록을 저장하는 Atom을 정의하면 다음과 같다.

```js
const usersState = atom({
  key: "usersState",
  default: [
    {
      id: 1,
      name: "Kim",
      age: 20,
      country: "Korea",
    },
    {
      id: 2,
      name: "John",
      age: 18,
      country: "USA",
    },
    {
      id: 3,
      name: "Nakamura",
      age: 30,
      country: "Japan",
    },
  ],
});
```

이제 성인 사용자 목록과 특정 국가의 사용자 목록을 계산하는 Selector를 정의해보면

```js
const adultUsersSelector = selector({
  key: "adultUsersSelector",
  get: ({ get }) => {
    const users = get(usersState);
    return users.filter((user) => user.age >= 18);
  },
});

const usersByCountrySelector = selectorFamily({
  key: "usersByCountrySelector",
  get:
    (country) =>
    ({ get }) => {
      const users = get(usersState);
      return users.filter((user) => user.country === country);
    },
});
```

selector는 파생된 상태를 만들기 위한 함수이며 상태를 입력으로 받고 그 상태에 따라 계산된 결과를 반환한다.

selectorFamily는 selector를 만들기 위한 함수이며, selector를 만들 때 사용하는 `인자를 받아` selector를 반환한다.

이제 컴포넌트에서 이 Selector를 사용하면 다음과 같다.

```js
import { useRecoilValue } from "recoil";
import { adultUsersSelector, usersByCountrySelector } from "./userSelectors";

function AdultUsers() {
  const adultUsers = useRecoilValue(adultUsersSelector);

  // adultUsers를 사용하여 성인 사용자 목록을 렌더링합니다.
}

function UsersByCountry({ country }) {
  const usersByCountry = useRecoilValue(usersByCountrySelector(country));

  // usersByCountry를 사용하여 해당 국가의 사용자 목록을 렌더링합니다.
}
```

## 정리

Recoil에서 상태를 관리할 때, 상태를 직접 사용하는 것과 상태를 기반으로 계산된 값을 사용하는 것의 차이를 알 수 있었다. 상태를 직접 사용하는 것은 상태가 변경될 때마다 컴포넌트가 리렌더링되어 성능이 저하될 수 있으며, 상태를 기반으로 계산된 값을 사용하는 것은 상태를 사용하는 컴포넌트가 여러 개일 경우, 상태가 변경될 때마다 모든 컴포넌트가 리렌더링되어 성능이 저하될 수 있다. 이런 경우 Selector를 사용하면 효과적으로 상태와 파생 값을 관리할 수 있다.

또한 Selector는 캐싱되기 때문에, 상태가 변경되지 않는 한 Selector는 한 번만 실행된다. 이를 통해 상태가 변경되지 않는 한 Selector는 한 번만 실행되기 때문에 성능을 향상시킬 수 있다.
