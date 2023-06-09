# React-Native에서 env 사용하기

[react-native-dotenv 패키지](https://www.npmjs.com/package/react-native-dotenv)
bobChemi 프로젝트를 진행하며 API 키를 숨기기 위해 .env 파일을 사용하려하는데 웹에서 사용하는 방법 그대로는 사용할 수 없었다.  
위의 패키지를 설치 후 `babel.config.js` 파일을 설정해주는 과정에서

## babel.config.js 설정

```json
{
  "plugins": [["module:react-native-dotenv"]]
}
```

공식 문서에는 위와같이 설정방법을 알려줬는데 이미 plugins에 다른 내용이 있어서 검색을 더 해서 아래와같이 수정해줬다.  
설정 과정에서 `moduleName`과 `path`를 설정해주는데 `moduleName`은 `import` 할 때 사용할 이름이고 `path`는 `.env` 파일의 경로를 설정해주는 것이다. `path`는 프로젝트 루트 경로를 기준으로 하기에 프로젝트 루트의 .env 파일을 사용하기 위해 .env 파일의 경로를 설정해줬다.

```javascript
module.exports = {
  presets: ["module:metro-react-native-babel-preset"],
  plugins: [
    [
      "module-resolver",
      {
        root: ["./src"],
        extensions: [
          ".ios.ts",
          ".android.ts",
          ".ts",
          ".ios.tsx",
          ".android.tsx",
          ".tsx",
          ".jsx",
          ".js",
          ".json",
        ],
        alias: {
          "@": "./src",
          "@components": "./src/components",
          "@containers": "./src/containers",
          "@modules": "./src/modules",
          "@scenes": "./src/scenes",
          "@utils": "./src/utils",
          "@screens": "./src/screens",
          "@assets": "./src/assets",
        },
      },
    ],
    [
      "module:react-native-dotenv",
      {
        moduleName: "@env",
        path: ".env",
        safe: false,
        aloowUndefined: true,
      },
    ],
  ],
};
```

## .env 파일 설정

```env
YOUR_API_KEY=abcdefghijk123456
```

## TypeScript 설정

타입스크립트 설정을 하지 않으면 `@env`를 사용할 때 `Cannot find module '@env' or its corresponding type declarations.` 에러가 발생한다.  
이를 해결하기 위해 `env.d.ts` 파일을 생성해주고 아래와 같이 작성해준다.

```typescript
declare module "@env" {
  export const YOUR_API_KEY: string;
}
```

위와 같이 `.env` 파일안의 변수를 타입스크립트에서 사용할 수 있도록 설정해준다.

## 사용

```javascript
import { YOUR_API_KEY } from "@env";
```

## 에러...와 해결?

위 설정대로 잘 작성했는데 `Cannot find module '@env' or its corresponding type declarations.` 에러가 발생했다.  
설치와 설정을 몇번씩 다시 해봤지만 해결이 안되던 와중 `StackOverflow`에서 해결책을 찾았는데 그것은 VSCode를 완전히 종료하고 다시 실행하는 것이었다....  
시도해보면서도 이게 맞나? 싶었는데 진짜 되더라... 사실 이 글도 해당 문제를 만나고 바로 쓴 것은 아닌데 프로젝트를 진행하면서 뭔가 `TypeScript` 관련해서 문제가 없어야하는데 에러를 내보내는 경우 `VSCode`를 재실행하면 해결되는 경우가 있었다.  
에러가 발생하면 일단 기본적인 문제 해결 방법을 시도 후 되야될 것 같은데 안되면 `VSCode`를 재실행해보자.
