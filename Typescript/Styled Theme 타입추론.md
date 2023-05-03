새로운 프로젝트를 생성하고 작업 중 Styled-Component의 `theme` 객체의 내용의 타입추론을 하지 못하는 불편함이 생겼다.

분명 다른 프로젝트에서는 관련 설정을 해뒀던 것 같은데 기억이 확실치 않아 확실히 무조건 다 기록해놔야한다고 느꼈다.

## styled.d.ts 파일에서 theme의 타입 지정해주기

Styled-Component의 `theme`은 기본적으로 `DefaultTheme` 인터페이스를 가지는데 이 `DefaultTheme` 인터페이스에 지금 사용하고있는 인터페이스를 `extends` 하는 식으로 해결을 했다.

### theme.ts

```typescript
interface colorsProps {
  MAIN: string;
}

export interface CustomTheme {
  colors: colorsProps;
}

const colors = {
  MAIN: "#F6EABE",
};

const theme: CustomTheme = {
  colors,
};

export default theme;
```

### styled.d.ts

```typescript
import { CustomTheme } from "./src/styles/theme";

declare module "styled-components" {
  export interface DefaultTheme extends CustomTheme {}
}
```

이후 `theme`객체의 내용을 정상적으로 추론할 수 있어 일일히 theme.ts 파일의 내용을 보지 않아도 된다!
