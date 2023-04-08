# Lottie Animation

## Lottie Animation?

Lottie는 애니메이션을 JSON 형태로 만들어서 사용할 수 있게 해주는 라이브러리이다.
[공식홈페이지](https://lottiefiles.com/)

### 장점

- 애니메이션을 JSON 형태로 만들어서 사용할 수 있어서, 애니메이션을 만드는데 있어서 편리하다. (만들일은 없을 듯 하다.)
- Gif 대비 용량이 작다.
- 어떤 기기에서나 작동하고 크기를 조절해도 해상도가 낮아지지 않는다. (Gif는 해상도가 낮아지는 문제가 있다.)
- 정말 다양한 애니메이션들이 있다. (일부 무료)

## 사용하기 (React)

- [공식홈페이지](https://lottiefiles.com/)에서 원하는 애니메이션을 찾아 JSON 형식으로 다운로드
- npm install lottie-web [NPM](https://www.npmjs.com/package/lottie-web) | npm install lottie-react [NPM](https://www.npmjs.com/package/lottie-react)

### lottie-web

```jsx
import React, { useEffect, useRef } from "react";
import lottie from "lottie-web";

const Lottie = () => {
  const container = useRef(null);

  useEffect(() => {
    lottie.loadAnimation({
      container: container.current,
      renderer: "svg",
      loop: true,
      autoplay: true,
      animationData: require("../lottie/animation.json"),
    });
  }, []);

  return <div className="lottie" ref={container}></div>;
};
```

### lottie-react

#### Component

```jsx
import React from "react";
import Lottie from "lottie-react";
import animationData from "../lottie/animation.json";

const Lottie = () => {
  const options = {
    animationData: animationData,
    loop: true,
    autoplay: true,
  };

  return (
    <div className="lottie">
      <Lottie options={options} />
    </div>
  );
};
```

#### useLottie

```jsx
import React from "react";
import { useLottie } from "lottie-react";
import animationData from "../lottie/animation.json";

const Lottie = () => {
  const options = {
    animationData: animationData,
    loop: true,
    autoplay: true,
  };

  const { View } = useLottie(options);

  return (
    <div className="lottie">
      <View />
    </div>
  );
};
```
