여자친구와 둘이서 쓰던 스마트미러 프로젝트를 포트폴리오에 넣기위해 저장소를 `private`에서 `public`으로 바꾸는김에 `Typescript`를 적용하기로했다.

# 1. TypeScript관련 패키지 설치

> npm install --save typescript @types/node @types/react @types/react-dom @types/jest

# 2. tsconfig.json 생성

> npx tsc --init

다음 명령어로 프로젝트 루트에 `tsconfig.json` 파일을 생성하고 안의 내용에 Typescript 설정을 적어준다.

```typescript
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

다른 프로젝트에서 사용중인 설정대로 설정해주었다.

# 3. js, jsx -> ts, tsx

프로젝트의 `JS` 파일 확장자명을 `TS` 확장자로 변환해준다.
이때 JavaScript 환경에서는 `JSX` 컴포넌트도 `JS` 확장자로 사용해도 아무 문제가 없었지만
TypeScript 환경에서는 `JSX`를 리턴하는 컴포넌트를 `TS` 확장자로 놓으면 타입 오류가 뜨게된다.

# 4. 패키지 버전 확인

프로젝트 실행시 npm 에서 오류가 발생했다.

```javascript
code ERESOLVE
npm ERR! ERESOLVE could not resolve
npm ERR!
npm ERR! While resolving: react-scripts@5.0.1
npm ERR! Found: typescript@5.0.4
npm ERR! node_modules/typescript
npm ERR!   typescript@"^5.0.4" from the root project
npm ERR!   peer typescript@">= 2.7" from fork-ts-checker-webpack-plugin@6.5.2
npm ERR!   node_modules/fork-ts-checker-webpack-plugin
npm ERR!     fork-ts-checker-webpack-plugin@"^6.5.0" from react-dev-utils@12.0.1
npm ERR!     node_modules/react-dev-utils
npm ERR!       react-dev-utils@"^12.0.1" from react-scripts@5.0.1
npm ERR!       node_modules/react-scripts
npm ERR!         react-scripts@"5.0.1" from the root project
npm ERR!   1 more (tsutils)
npm ERR!
npm ERR! Could not resolve dependency:
npm ERR! peerOptional typescript@"^3.2.1 || ^4" from react-scripts@5.0.1
npm ERR! node_modules/react-scripts
npm ERR!   react-scripts@"5.0.1" from the root project
npm ERR!
npm ERR! Conflicting peer dependency: typescript@4.9.5
npm ERR! node_modules/typescript
npm ERR!   peerOptional typescript@"^3.2.1 || ^4" from react-scripts@5.0.1
npm ERR!   node_modules/react-scripts
npm ERR!     react-scripts@"5.0.1" from the root project
npm ERR!
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force, or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
npm ERR!
npm ERR! See /Users/chanhwi/.npm/eresolve-report.txt for a full report.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/chanhwi/.npm/_logs/2023-05-04T09_27_33_174Z-debug-0.log
```

`react-script` 5.0.1 버전에서 사용할 수 있는 `TypeScript` 버전은 3.2.1 이후 최대 4버전 까지인데 현재 설치된 `TypeScript` 버전은 5.0.4 버전이기 때문에 생긴 오류이다.

이를 해결하기 위해 `TypeScript` 버전을 4.9.5 버전으로 다운그레이드 하였다.

> npm i typescript@4.9.5

# 4. 트러블슈팅

![](https://velog.velcdn.com/images/strongorange/post/291a6e9b-9622-467c-8139-2e348619becd/image.png)

이제 프로젝트에서 발생하는 `Typescript` 오류를 즐겁게 수정하면 `TypeScript`로 프로젝트 이사하기는 의외로 간단하게 끝난다!
