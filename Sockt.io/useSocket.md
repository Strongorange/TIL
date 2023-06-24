# Socket.io 전역 관리의 필요성

PriceCrush 프로젝트에서는 Socket을 사용하는 컴포넌트가 한개밖에 없어 전역적인 소켓 관리가 불필요했지만 밥케미 프로젝트에서는 매칭 이벤트를 다른 페이지에서도 수신할 필요가 있어  
전역적 소켓 관리가 필요하게 되었다.

# React Context 사용

상태관리 툴로 Recoil을 사용중이라 Recoil을 사용하려고 했지만 오류가 생겨 검색한 결과 Recoil로는 Socket을 담을 수 없다고 해서 React Context 훅을 사용하였다.

## socketContext.ts

```typescript
import { createContext } from "react";
import { io, Socket } from "socket.io-client";

const SOCKET_URL = "http://localhost:4000";

export const socket = io(SOCKET_URL);
export const SocketContext = createContext<Socket>(socket);
```

소켓의 연결 주소로 소켓을 초기화한 뒤 기본값으로 socket을 가지는 SocketContext를 생성해준다.

## App.tsx

```typescript
<SocketContext.Provider value={socket}>{children}</SocketContext.Provider>
```

앱의 루트에서 SocketContext.Provide의 value로 socket 을 지정

## useSocket.ts

위에서 설정한 socket을 여러 컴포넌트에서 사용할 수 있게 커스텀 훅을 작성한다.

```typescript
import { useContext, useEffect } from "react";
import { useRecoilState } from "recoil";
import { SocketContext } from "@/contexts/socketContext";
import { matchingStatesAtom } from "@/recoil/atoms/matchingStatesAtom";

const useSocket = () => {
  const socket = useContext(SocketContext);
  const [matchingState, setMatchingState] = useRecoilState(matchingStatesAtom);

  useEffect(() => {
    socket.on("findMatching", (data) => console.log("매칭 등록 : ", data));

    socket.on("matchFound", (data) => {
      console.log("매칭 완료");
      console.log(data);
      setMatchingState(false);
    });

    socket.emit("test", "테스트중!!!");
  }, [socket]);

  return socket;
};

export default useSocket;
```

기본적으로 앱 어디서나 수신이 필요한 matchFound, findMatching 이벤트를 수신하고있는 socket을 return하는 훅을 만든다.

## 사용

```jsx
import useSocket from "@/hooks/useSocket";

const Component = () => {
  // Socket
  const socket = useSocket();

  // 소켓 사용
  useEffect(() => {
    if (socket && socket.connected) {
      socket.on("추가 이벤트", (data) => console.log(data));

      return () => {
        socket.off("추가 이벤트");
      };
    }
  }, [socket]);
};
```

위처럼 socket을 불러오고 컴포넌트 안에서 사용할 추가 이벤트를 등록하고 취소해 사용할 수 있다.
