# useCallback

## React는 언제 재렌더링되나? 🤔

React는

- 상태가 변경되거나
- 부모 컴포넌트가 리렌더링되면 자식 컴포넌트도 리렌더링된다
  이때 자식 컴포넌트의 함수가 새로 생성되면, 자식 컴포넌트는 리렌더링되지 않더라도 부모 컴포넌트가 리렌더링될 때마다 자식 컴포넌트의 함수도 새로 생성된다. 이는 성능에 좋지 않은 영향을 미칠 수 있다.

</br>

## 기본 설명 ✒️

`useCallback`은 React Hook으로, 메모이제이션된 콜백 함수를 반환한다. 이를 통해 불필요한 렌더링을 방지하고 성능을 최적화할 수 있다. `useCallback`은 종속성 배열을 받으며, 배열 내 값이 변경될 때만 새로운 콜백 함수가 생성된다. 배열이 비어 있으면 콜백은 컴포넌트의 전체 수명 동안 메모이제이션된다.

</br>

## 메모이제이션 📝

`memoization`이란 함수의 결과를 캐싱하는 것으로, **함수의 인자**가 동일할 경우 이전에 계산한 결과를 재사용하는 것을 의미한다. 이를 통해 함수의 실행 시간을 줄일 수 있다.

</br>

## 기본적인 예시 🧪

```javascript
import { useCallback } from "react";

const ExpensiveComponent = ({ onClick }) => {
  console.log("Rendered!");

  return <button onClick={onClick}>Click me!</button>;
};

const ParentComponent = () => {
  const handleClick = useCallback(() => {
    console.log("Button clicked!");
  }, []);

  return <ExpensiveComponent onClick={handleClick} />;
};
```

위 예를보면 `ParentComponent`의 `handleClick`함수는 `useCallback`을 사용해 메모이제이션되었고 `ParentComponent`가 리렌더링되어도, `handleClick` 함수는 메모이제이션된 값을 사용하므로 `ExpensiveComponent`는 리렌더링되지 않는다.

## 응용 예시 🧪

```javascript
function MyComponent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(count + 1);
  }, [count]);

  // ...
  return <button onClick={handleClick}>Click me ({count})</button>;
}
```

`useCallback`을 사용하여 함수를 메모이제이션 할 때, 두 번째 인자로 전달하는 배열에는 함수 내부에서 참조하는 변수들을 포함시켜야한다. 이렇게 함으로써, 해당 변수들의 값이 변경될 때만 새로운 함수를 생성하고, 그렇지 않은 경우에는 이전에 생성된 함수를 재사용할 수 있다.

위 코드에서 `handleClick` 함수는 `count` 상태값을 참조하고 있다. 따라서, `count`를 두 번째 인자로 전달하여 `handleClick` 함수를 적절하게 메모이제이션한다.

`useCallback`은 `useMemo`와 비슷한 개념을 가지고 있습니다. `useMemo`는 값을 메모이제이션하여 불필요한 계산을 방지하는데 사용되고, `useCallback`은 함수를 메모이제이션하여 불필요한 함수 생성을 방지하는데 사용된다.

## 프로젝트에서의 예시 useModal 🧪

```typescript
import { useRecoilState } from "recoil";
import { modalState } from "@/atoms/modalState";
import { useCallback } from "react";

interface OpenModalProps {
  title: string;
  content: JSX.Element | string;
}

/**
 * @description Modal을 사용하기 위한 Hook
 * @returns {object} modalDataState: Modal의 상태를 가지고 있는 객체
 * @returns {function} closeModal: Modal을 닫는 함수
 * @returns {function} openModal: Modal을 열고 데이터를 넣어주는 함수
 */
export const useModal = () => {
  const [modalDataState, setModalDataState] = useRecoilState(modalState);

  /**
   * @description Modal을 닫는 함수
   * @description modalState의 isOpen을 false로 변경하여 모달창을 닫음
   */
  const closeModal = useCallback(
    () =>
      setModalDataState((prev) => {
        return {
          ...prev,
          isOpen: false,
        };
      }),
    [setModalDataState]
  );

  /**
   * @description Modal을 열고 데이터를 넣어주는 함수
   * @param title Modal의 제목
   * @param content {JSX Element} Modal의 내용 컴포넌트가 들어가며 모달창의 컨텐츠가 됨
   */

  const openModal = useCallback(
    ({ title, content }: OpenModalProps) => {
      setModalDataState({
        isOpen: true,
        title,
        content,
      });
    },
    [setModalDataState]
  );

  /**
   * @description Modal의 내용을 변경하는 함수, 모달이 열려있는 상태에서 변경 가능
   * @param title {string} Modal의 제목
   * @param content {JSX Element} Modal의 내용 컴포넌트가 들어가며 모달창의 컨텐츠가 됨
   */
  const changeModal = useCallback(
    ({ title, content }: OpenModalProps) => {
      setModalDataState((prev) => {
        return {
          ...prev,
          title,
          content,
        };
      });
    },
    [setModalDataState]
  );

  return { modalDataState, closeModal, openModal, changeModal };
};
```

> 프로젝트에서 Recoil을 사용하며 Modal을 사용하기 위해 만든 Hook.
> `useCallback`을 사용하여 `closeModal`, `openModal`, `changeModal` 함수를 메모이제이션하여 Modal을 사용하는 컴포넌트가 리렌더링되어도 함수들은 메모이제이션된 값을 사용하므로 Modal 컴포넌트는 리렌더링되지 않는다.
