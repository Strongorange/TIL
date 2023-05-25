# Styled-Components 와 React Native, TypeScript 사용시 import 오류

## 에러

<img width="913" alt="image" src="https://github.com/Strongorange/TIL/assets/74127841/66fed406-41eb-4ed9-91d2-4f6efb407e9b">
React Native 에서 Styled-Components와 TypeScript를 사용시 항상 위 사진처럼 `Could not find a declaration file for module `styled-components/native`... 에러가 발생한다.

구글링을 해봐도 `@types/styled-components-react-native` 를 설치하라는 글만 보였는데 설치해도 해결되지 않았다.

## 해결방법

> `npm i  -D @types/styled-components-react-native` 설치후 `tsconfig.json` 설정

`tsconfig.json`

```json
{
  "extends": "@tsconfig/react-native/tsconfig.json",
  "compilerOptions": {
    /**  ...  **/
    // types 속성을 추가해준다
    "types": ["@types/styled-components-react-native"]
  }
  /** ... **/
}
```

## 해결

![image](https://github.com/Strongorange/TIL/assets/74127841/0e07fecb-1781-4a63-98c2-5d642f360857)

에러가 해결되었다.
