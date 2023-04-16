# Socket.IO

[공식문서](https://socket.io/docs/v4/)

클라이언트와 서버간의 **이벤트 기반**, **저지연**, **양방향** 통신을 가능하게 해주는 라이브러리

**WebSocket** 프로토콜 위에 구축되었으며 HTTP 롱폴링, 자동 재연결에 대한 추가 보증을 제공함.

## WebSocket

인터넷 프로토콜 스택의 Application Layer에서 작동하는 프로토콜로, 실시간 양방향 통신을 지원.  
WebSocket은 기본적으로 `TCP/IP` 프로토콜 위에서 작동하며, HTTP 프로토콜을 사용하여 초기 연결을 설정한 다음, WebSocket 프로토콜로 전환합니다. 웹 애플리케이션에서 실시간 채팅, 게임, 주식 거래 등의 기능을 구현할 때 사용

서버

```
import { WebSocketServer } from "ws";

const server = new WebSocketServer({ port: 3000 });

server.on("connection", (socket) => {
  // send a message to the client
  socket.send(JSON.stringify({
    type: "hello from server",
    content: [ 1, "2" ]
  }));

  // receive a message from the client
  socket.on("message", (data) => {
    const packet = JSON.parse(data);

    switch (packet.type) {
      case "hello from client":
        // ...
        break;
    }
  });
});
```

클라이언트

```
import { WebSocketServer } from "ws";

const server = new WebSocketServer({ port: 3000 });

server.on("connection", (socket) => {
  // send a message to the client
  socket.send(JSON.stringify({
    type: "hello from server",
    content: [ 1, "2" ]
  }));

  // receive a message from the client
  socket.on("message", (data) => {
    const packet = JSON.parse(data);

    switch (packet.type) {
      case "hello from client":
        // ...
        break;
    }
  });
});
```

## Socket.io

웹 애플리케이션에서 실시간 양방향 통신을 구현하기 위한 JavaScript 라이브러리입니다. 기본적으로 `WebSocket` 프로토콜을 사용하지만, WebSocket이 지원되지 않는 환경에서는 다양한 대체 통신 방법(예: AJAX long polling, AJAX multipart streaming 등)을 자동으로 적용하여 넓은 범위의 호환성을 제공합니다. Socket.IO는 또한 `이벤트 기반`의 통신을 지원하며, 쉽게 사용할 수 있는 API를 제공하여 웹 애플리케이션 개발자가 실시간 통신 기능을 빠르게 구현할 수 있도록 돕습니다.

서버

```
import { Server } from "socket.io";

const io = new Server(3000);

io.on("connection", (socket) => {
  // send a message to the client
  socket.emit("hello from server", 1, "2", { 3: Buffer.from([4]) });

  // receive a message from the client
  socket.on("hello from client", (...args) => {
    // ...
  });
});
```

클라이언트

```
import { io } from "socket.io-client";

const socket = io("ws://localhost:3000");

// send a message to the server
socket.emit("hello from client", 5, "6", { 7: Uint8Array.from([8]) });

// receive a message from the server
socket.on("hello from server", (...args) => {
  // ...
});
```

WebSocket과 Socket.io는 비슷해보이지만 Socket.io에서는 애플리케이션 실행의 복잡성을 숨기는 기능을 제공해 조금 더 깔끔하게 작성되었음

### 주의

Socket.IO는 모바일 애플리케이션의 백그라운드 서비스에서 사용하게 의도되지 않았음.

Socket.IO 라이브러리는 서버를 향해 TCP 연결을 유지. 모바일 기기의 백그라운드 서비스에 사용시 심한 배터리 드레인현상이 발생

이러한 기능이 필요시 Firebase Cloud Messaging을 사용할 것을 권장하고 있음

### WebSocket 대신 Socket.IO가 필요한 이유

WebSocket은 거의 모든 곳에서 지원되기 때문에 Socket.IO 대신 기본 WebSocket을 사용가능

Socket.IO에서 기본적으로 제공하는 reconnection, acknowledgements, broadcasting 등의 기능을 추가적으로 구현해야하기 때문에 Socket.IO를 사용하는 것이 더 높은 생산성을 가짐

### 사용하기

`서버`

```javascript
// server.js
const express = require("express");
const http = require("http");
const { Server } = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = new Server(server);

io.on("connection", (socket) => {
  console.log("a user connected", socket.id);

  socket.on("disconnect", () => {
    console.log("user disconnected", socket.id);
  });

  socket.on("chat message", (msg) => {
    io.emit("chat message", msg);
  });
});

server.listen(3001, () => {
  console.log("listening on *:3001");
});
```

`(socket) => {...}` 부분의 `socket` 매개변수는 현재 접속한 클라이언트와 관련된 정보와 기능을 가지고있는 객체. 각 클라이언트가 서버에 연결될때마다 이 함수가 호출되어 새로운 `socket` 객체가 생성되고 다음을 가짐

1. `socket.id`: 각 클라이언트의 고유한 식별자, 이를 사용해 특정 클라이언트에게 메세지를 보내거나 클라이언트 관련 정보 추적 가능

2. `socket.emit(eventName, data)`: 클라이언트에게 이벤트를 전송하는 메소드. 이를 사용해 서버에서 클라이언트로 특정 이벤트와 데이터 전송

3. `socket.on(eventName, callback)`: 클라이언트로부터 이벤트를 수신하는 리스너를 설정하는 메소드, 이를 사용해 클라이언트가 보낸 이벤트와 데이터를 처리

4. `socket.join(room), socket.leave(room)`: 클라이언트를 특정 방에 참여시키거나 떠나게하는 메서드, 이를 사용해 클라이언트를 그룹화하고, 특정 방에 있는 클라이언트끼리 메세지 주고받기 가능.

5. `socket.broadcast`: **현재 클라이언트를 제외한** 다른 모든 클라이언트에게 이벤트를 전송. `socket.broadcast.emit('event', data)` 같이 사용

<br/>

`클라이언트`

```javascript
// Chat.js
import React, { useState, useEffect } from "react";
import { io } from "socket.io-client";

const socket = io("http://localhost:3001");

const Chat = () => {
  const [message, setMessage] = useState("");
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    socket.on("chat message", (msg) => {
      setMessages((prevMessages) => [...prevMessages, msg]);
    });

    return () => {
      socket.disconnect();
    };
  }, []);

  const handleSubmit = (e) => {
    e.preventDefault();
    socket.emit("chat message", message);
    setMessage("");
  };

  return (
    <div>
      <ul>
        {messages.map((msg, index) => (
          <li key={index}>{msg}</li>
        ))}
      </ul>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          value={message}
          onChange={(e) => setMessage(e.target.value)}
          placeholder="Type a message"
        />
        <button type="submit">Send</button>
      </form>
    </div>
  );
};

export default Chat;
```

`useEffect`안에서 컴포넌트가 마운트될 때 서버로부터 받은 메시지를 처리하는 이벤트 리스너 등록 및 컴포넌트 언마운트될때 `socket.disconnet()` 호출해 이벤트 정리. 서버로부터 `chat message` 이벤트를 받으면 로직 처리

`handleSubmit` 동작시 `socket.emit`을 통하여 서버에 `chat message` **이벤트**와 사용자가 입력한 메시지 전송.
