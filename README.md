# Today I Learned (TIL)

> 하루하루 배운 내용을 정리하는 저장소  
> 참고한 내용을 바탕으로 직접 작성

### ETC

### 공부 할 것

localstorage Recoil 연동 라이브러리, effect 새로고침시 데이터 날라가는 거 방지할떄

socket사용

Flutter 정리

Android 에서는 Settimeout 작동하지 않음

effect

Flat List Styled component

react query 사용해보기
useSWR 사용해보기
Axios Interceptor
HttpOnly 쿠키 핸들링 과정
Spring, 자바
Typescript Key of, 'user in 타입`
proj4

#### RN route

React Navigation v5 이상에서는 route prop을 통해 전달된 parameter에 접근할 수 있습니다. 이 route prop은 현재 화면의 정보, parameter를 포함합니다.

먼저, navigation parameter의 타입을 지정해야 합니다. 이를 위해 ParamListBase를 사용하여 적절한 타입을 지정하면 됩니다.

tsx
Copy code
import { RouteProp } from "@react-navigation/native";

type StackParamList = {
MessagesCRUD: {
key: string;
};
};

type MessagesCRUDScreenRouteProp = RouteProp<StackParamList, "MessagesCRUD">;

interface MessagesCRUDProps {
route: MessagesCRUDScreenRouteProp;
}

const MessagesCRUD = ({ route }: MessagesCRUDProps) => {
const { key } = route.params;

return (
<View>
<Text>MessagesCRUD</Text>
<Text>{key}</Text>
</View>
);
};

export default MessagesCRUD;
위의 코드에서 RouteProp을 사용하여 MessagesCRUD screen의 parameter의 타입을 지정했습니다. 그리고 이를 MessagesCRUDProps에 추가하여 route prop에서 key 값을 받아와 사용할 수 있게 했습니다.

이제 MessagesCRUD screen에서 key 값을 출력할 수 있습니다.
