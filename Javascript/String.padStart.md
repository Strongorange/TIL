# String.PadStart

**`.padStart()`** 메서드는 문자열의 길이를 특정 수치로 맞추기 위해 사용한다. 첫번째 인자로 전달된 길이에 도달하기 위해 현재 문자열의 앞부분을 두번째 인자로 전달된 문자로 채운다

```javascript
const str = "123";
const paddedStr = str.padStart(5, "0");

console.log(paddedStr); // "00123"
```

**`padStart()`** 메서드를 사용하여 useDiff 훅에서 1분, 5분 같이 표시되는 시간을 01분, 05분으로 표시하도록 사용했다.
</br>

<details>
    <summary>useTimeDiff 훅</summary>

```typescript
import { useState, useEffect } from "react";

export const useTimeDiff = (referenceTime: string): string => {
  const [timeDiff, setTimeDiff] = useState("");

  useEffect(() => {
    const intervalId = setInterval(() => {
      const diff = new Date(referenceTime).getTime() - new Date().getTime();
      const days = Math.floor(diff / (1000 * 60 * 60 * 24));
      const hours = Math.floor((diff / (1000 * 60 * 60)) % 24);
      const minutes = Math.floor((diff / (1000 * 60)) % 60);
      const seconds = Math.floor((diff / 1000) % 60);

      setTimeDiff(
        `${days.toString().padStart(2, "0")}:${hours
          .toString()
          .padStart(2, "0")}:${minutes.toString().padStart(2, "0")}:${seconds
          .toString()
          .padStart(2, "0")}`
      );
    }, 1000);

    return () => clearInterval(intervalId);
  }, [referenceTime]);

  return timeDiff;
};
```

</details>
