# Recoil-Persist

## State의 문제점

React의 State를 다루다보면 항상 마주하는 문제가
새로고침, 다른 페이지로 이동, 브라우저 재실행 등등의 과정에서 State가 유지가 되지 않는다는 것이다.

이를 해결하기 위해 주로 state를 업데이트 할때`localStorage`나 `SessionStorage` 에 값을 저장해놓고 `useEffect`를 사용해서 페이지 표시전에 로컬에 저장된 값을 꺼내와 state에 집어넣어주는 방식으로 작업했는데 별건 아니지만 불필요하게 반복되는 코드를 많이 만드는 느낌이라 다른 방법을 찾아보던 중 `Recoil-Persist`라는 라이브러리를 발견했다.

[NPM](https://www.npmjs.com/package/recoil-persist)

## Recoil-Persist

`Recoil State`를 `Session,Local Storage`와 동기화 시켜주는 라이브러리로 사용시 Recoil State를 Local, Session Storage로 `저장, 불러오기` 해주는 코드를 작성할 필요없이 자동으로 둘이 연동된다.

### 설정

```typescript

import { CurrentProductStateProps } from '@/types/productsTypes';
import { atom } from 'recoil';
// recoil-persist 라이브러리를 불러옴
import { recoilPersist } from 'recoil-persist';

// Next.js에서 storage를 확인
const isClient = typeof window !== 'undefined';
const storage = isClient ? window.sessionStorage : undefined;

// recoil-persist의 기본 설정
const { persistAtom } = recoilPersist({
  key: 'recoil-persist', // 아무 이름이나 사용해도 된다
  storage, // 기본은 LocalStorage로 저장, 현재 코드는 SessionStorage 사용중
});

export const currentProductState = atom<CurrentProductStateProps>({
  key: 'currentProductState',
  default: {
    Recoil 상태값...
  },
  // 이부분에 위에서 설정한 persistAtom 설정
  effects_UNSTABLE: [persistAtom],
});
```

`Recoil State`를 만들어줄때 위의 설정을 거치고나서

```typescript
const [currentProductAtom, setCurrentProductAtom] =
  useRecoilState(currentProductState);

useEffect(() => {
  setCurrentProductAtom({
    tempData,
    productData,
    formattedInputBidPrice,
    handleBidButtonClick,
    handleCustomBidPriceInput,
    isAuctionStarted,
    available,
  });
}, [
  tempData,
  productData,
  formattedInputBidPrice,
  currentPrice,
  isAuctionStarted,
  available,
]);
```

### 저장

위 처럼 Storage에 대한 별 다른 처리 없이 사용하던대로 Recoil State를 저장하고 수정하면

![](https://velog.velcdn.com/images/strongorange/post/3a838a84-3903-4060-8ea7-727a02f4b71d/image.png)

자동으로 `Session Storage`에 저장이된다.

### 새로고침

![](https://velog.velcdn.com/images/strongorange/post/08a7690c-3844-4746-8484-e447e366a1e1/image.png)
새로고침시에도 RecoilState가 죽지않고 그대로 살아있다.

귀찮은 State 유지를 매우 쉽게 해주는 좋은 라이브러리를 찾은 것 같다
