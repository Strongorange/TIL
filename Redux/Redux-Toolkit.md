# Redux-Toolkit

기본 Redux만 사용하고 한동안 Recoil만 사용하다가 Redux를 다시 사용해보려하니 Redux-Toolkit을 사용하라는 조언을 받았다.
Redux와 어떤 차이가 있는지 간단하게 정리해보았다. [공식문서 🌐](https://redux-toolkit.js.org/introduction/getting-started)

## Redux-Toolkit의 이점

- redux 설정의 복잡성 간소화
- redux를 위한 패키지 양이 늘어나는 문제 해결
- 많은 boilterplate 간소화

Redux-Toolkit은 다음과 같은 주요 기능을 제공한다.

- configureStore: Redux 개발자 도구 확장 기능을 자동으로 설정하고 미들웨어를 설정하는 간편한 방법을 제공
- createAction, createReducer, createSlice: 액션 타입, 액션 생성자, 리듀서를 더 쉽게 작성
- createAsyncThunk: 비동기 로직을 간단하게 작성

## Redux와 Redux-Toolkit의 차이점

Redux는 액션 타입, 액션 생성자, 리듀서를 수동으로 작성해야 하지만 Redux-Toolkit은 createSlice라는 함수를 사용하여 이러한 요소들을 자동으로 생성한다.

> **Redux**

```javascript
// actions.js
export const increment = () => ({ type: "counter/increment" });
export const decrement = () => ({ type: "counter/decrement" });

// reducer.js
const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case "counter/increment":
      return state + 1;
    case "counter/decrement":
      return state - 1;
    default:
      return state;
  }
};

// store.js
import { createStore } from "redux";
import counterReducer from "./reducer";
const store = createStore(counterReducer);
```

> **Redux-Toolkit**

```javascript
import { createSlice, configureStore } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: 0,
  reducers: {
    increment: (state) => state + 1,
    decrement: (state) => state - 1,
  },
});

export const { increment, decrement } = counterSlice.actions;

const store = configureStore({
  reducer: counterSlice.reducer,
});
```

확실히 코드의 양이 많이 줄었다.

이외에도 Redux 사용시에도 비동기 처리는 사용해보지 않았지만 Redux-Toolkit은 비동기 로직 처리에 유용한 createAsyncThunk 기능도 제공한다고한다.
