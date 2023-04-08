# immmer 라이브러리

## immer.js?

React에서 불변성을 유지하는 코드를 작성하기 쉽게 해주는 라이브러리

## 불변성?

불변성은 메모리 주소를 변경할 수 없는 것을 의미한다.
[참고 🌐](https://github.com/Strongorange/TIL/blob/main/Next.js%20%26%20React/%EB%B6%88%EB%B3%80%EC%84%B1.md)

## 불변성 지키며 상태 업데이트하기

Javascript 객체 기본 메소드 map, filter, concat, slice 등을 사용하여 불변성을 지키며 상태를 업데이트할 수 있다.

**배열에 추가**

```javascript
setUsers(state.array.concat(user));
```

**배열에서 삭제**

```javascript
const onRemove = (id) => {
  // user.id 가 id인 것을 제거
  setUsers(users.filter((user) => user.id !== id));
};
```

**배열에서 수정**

```javascript
const onToggle = (id) => {
  setUsers(
    users.map((user) =>
      user.id === id ? { ...user, active: !user.active } : user
    )
  );
};
```

**객체에서 추가**

```javascript
setState(state => {...state, key: value})
```

[함수 참조](https://kyounghwan01.github.io/blog/React/immer-js/#%E1%84%87%E1%85%AE%E1%86%AF%E1%84%87%E1%85%A7%E1%86%AB%E1%84%89%E1%85%A5%E1%86%BC-%E1%84%8C%E1%85%B5%E1%84%8F%E1%85%B5%E1%84%86%E1%85%A7%E1%86%AB%E1%84%89%E1%85%A5-state-%E1%84%87%E1%85%A1%E1%84%81%E1%85%AE%E1%84%80%E1%85%B5)

위 방법대로 불변성을 지키며 상태를 업데이트하면 코드가 길어지고 복잡해진다. 이때 immer.js를 사용하면 일반 객체나 배열을 다루듯 불변성을 유지하면서 상태를 업데이트 할 수 있다.

## immer.js 사용하기

[공식 GitHub](https://github.com/immerjs/immer)
[홈페이지](https://immerjs.github.io/immer/)

```javascript
const baseState = [
  {
    title: "Learn TypeScript",
    done: true,
  },
  {
    title: "Try Immer",
    done: false,
  },
];
```

공식 홈페이지의 예를 가져왔다 baseState 상태를 변경해보자.

### immer.js 없이 상태 변경하기

```javascript
const nextState = baseState.slice(); // 새로운 배열 생성
nextState[1] = {
  // 첫 번째 요소 교체
  ...nextState[1], // 기존 1번째 요소 복사
  done: true, // 변화를 주고 싶은 값만 덮어씌우기
};

// nextState는 새로운 배열이기 때문에 push를 사용해도 안전하다.
// 임의의 시간에 미래에 동일한 작업을 수행하면
// 불변성 원칙을 위반하고 버그를 발생시킨다.
nextState.push({ title: "Tweet about it" });
```

### immer.js 사용하기

```javascript
import produce from "immer";

const nextState = produce(baseState, (draft) => {
  draft[1].done = true;
  draft.push({ title: "Tweet about it" });
});
```

immer에서 불변성 유지를 이용해 사용할 **produce** 함수의 첫 번째 인자는 수정하고 싶은 객체, 배열이고 두 번째 인자는 첫 번째 인자를 어떻게 업데이트할지 정의하는 **recipe** 함수이다.

공식 홈페이지에서는 이를 아래 사진과 같이 설명하고 있다.
![](https://velog.velcdn.com/images/strongorange/post/8156ade8-dbad-4f5a-ac56-61e7c9ad3521/image.png)

immer를 사용하는 것은 개인 비서를 가지는 것과 같다.  
비서가 letter (현재 상태)를 받고) 변경 사항을 기록할 사본(draft)을 제공한다. 초안을 작성하면 비서가 당신의 초안을 가지고 당신을 위해 진짜 불변의 letter를 작성한다.

## 리액트와 사용하기

```javascript
import React, { useCallback, useState } from "react";
import produce from "immer";

const TodoList = () => {
  const [todos, setTodos] = useState([
    {
      id: "React",
      title: "Learn React",
      done: true
    },
    {
      id: "Immer",
      title: "Try Immer",
      done: false
    }
  ]);

  const handleToggle = useCallback((id) => {
    setTodos(
      produce((draft) => {
        const todo = draft.find((todo) => todo.id === id);
        todo.done = !todo.done;
      })
    );
  }, []);

  const handleAdd = useCallback(() => {
    setTodos(
      produce((draft) => {
        draft.push({
          id: "todo_" + Math.random(),
          title: "A new todo",
          done: false
        });
      })
    );
  }, []);

  return (<div>{*/ See CodeSandbox */}</div>)
}
```

setState 함수안에 produce 함수를 넣어주고 기존 state를 draft로 받아서 사용하면 된다.

### useImmer 훅 사용하기

더 간단하게 사용하기 위해 immer.js에서 제공하는 useImmer 훅을 사용해보자.  
[NPM use-immer](https://www.npmjs.com/package/use-immer)

```javascript
import React, { useCallback } from "react";
import { useImmer } from "use-immer";

const TodoList = () => {
  const [todos, setTodos] = useImmer([
    {
      id: "React",
      title: "Learn React",
      done: true
    },
    {
      id: "Immer",
      title: "Try Immer",
      done: false
    }
  ]);

  const handleToggle = useCallback((id) => {
    setTodos((draft) => {
      const todo = draft.find((todo) => todo.id === id);
      todo.done = !todo.done;
    });
  }, []);

  const handleAdd = useCallback(() => {
    setTodos((draft) => {
      draft.push({
        id: "todo_" + Math.random(),
        title: "A new todo",
        done: false
      });
    });
  }, []);

  // etc

```

위와 같이 useState 대신 useImmer를 사용해서 불변성을 유지하면서 상태를 간단하게 업데이트 할 수 있다.

## immer.js가 유리한 상황

불변성에 관해 공부하고 immer.js를 사용하는 목적과 사용법도 알아보았는데 사실 크게 와닿지 않아서 immer.js를 사용해야 하는 상황을 찾아보았다.

```javascript
const [state, setState] = useState({
  a: {
    b: {
      c: 1,
    },
  },
});
```

위와 같은 상태가 있다고 가정하고 불변성을 유지하며 상태를 변경하려면 다음과 같이 작성해야 한다.

```javascript
setState({
  ...state,
  a: {
    ...state.a,
    b: {
      ...state.a.b,
      c: 2,
    },
  },
});
```

immer.js를 사용하면 다음과 같이 작성할 수 있다.

```javascript
import produce from "immer";

setState(
  produce(state, (draft) => {
    draft.a.b.c = 2;
  })
);
```

useImmer 훅을 사용하는 경우

```javascript
// useImmer 훅 사용해서 state 초기화
const [state, setState] = useImmer({
  a: {
    b: {
      c: 1,
    },
  },
});

const handleClick = () => {
  setState((draft) => {
    draft.a.b.c = 2;
  });
};
```

## 정리

- immer.js는 불변성을 유지하면서 상태를 업데이트할 수 있게 도와주는 라이브러리
- produce 함수를 사용하면 불변성을 유지하면서 상태를 업데이트할 수 있다.
- useImmer 훅을 사용하면 더 간단하게 상태를 업데이트할 수 있다.
- 중첩된 객체 또는 배열을 업데이트할 때는 immer.js를 사용하는 것이 좋다.
