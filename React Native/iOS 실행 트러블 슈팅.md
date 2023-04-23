# iOS 실행 트러블 슈팅

React Native init으로 프로젝트 생성 후 `npm run ios` 명령어를 실행해 iOS 환경에서 테스트를 시도했을때 다음과 같은 오류를 만났다
![](https://velog.velcdn.com/images/strongorange/post/2001c438-8b3c-4dc6-8ea5-952cbad71e76/image.png)
`warn` 부분은 RN이 자동적으로 Podfiles를 설정해줘서 일단 실행상에는 문제가 없다고하여 일단 넘어갔다.
위의 에러는 검색결과 [블로그1](https://choboit.tistory.com/133?category=1268430) [블로그2](https://velog.io/@ghenmaru/react-native%EB%A1%9C-iOS-%EC%95%B1-%EB%A7%8C%EB%93%A4%EA%B8%B0) 를 참조하여 Xcode의
![](https://velog.velcdn.com/images/strongorange/post/b822ca6e-ab3e-4f3f-92c9-05d1683aa864/image.png)
Command Line Tools를 다시 선택해주는 것으로.... 해결이 되었다 도대체 왜 이런게 해결 방안인지는 모르겠지만 이렇게 위의 오류는 해결이되었고 다시 `npm run ios` 명령어를 실행시 또 다른 오류가 발생하였다.

![](https://velog.velcdn.com/images/strongorange/post/6dd9c870-efe3-4a75-b2f4-02527e75d448/image.png)
![](https://velog.velcdn.com/images/strongorange/post/3f4d28f6-5ef6-4d91-955b-a6314e6694d4/image.png)
**BUILD FAILED** 에러를 마주했다.

에러코드 65 에러를 구글링하자 정말 많은 해결법이 나와있었는데 그만큼 65 에러가 생기는 원인은 정말 다양한 것 같다.
수많은 방법을 시도해본 끝에 개인적으로 문제를 해결한 방법은 아주 간단했는데 `/ios` 경로에서 `pod install` 명령어를 사용한 후 build에 성공하였다
