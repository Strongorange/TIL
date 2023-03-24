# Axios 사용해서 API 모듈화하기

지금까지 API 모듈화를 하지 않고, 각 컴포넌트에서 axios를 사용했다.  
주로 혼자 작업할때는 주석을 달아놓는 식이나 어떤 API를 사용하는지 알기 쉬웠지만  
팀 프로젝트를 진행하면서는 API 모듈화를 해야겠다는 생각이 들었다.

Axios Instance에 대해 예전에 사용해본적이 있었지만 그냥 이런게 있구나 정도로만 생각하고
사용해보지 않아 이번 기회에 적용해봐야겠다고 생각했다.

# API 모듈화를 하는 이유 ❓

> - 프로젝트에서 API 를 사용하는 컴포넌트가 많아지면 많아질수록 axios를 사용하는 코드가 중복되고, 효율성이 떨어지며 유지보수가 어려워진다.
> - API 모듈화를 하면, API를 사용하는 컴포넌트에서는 axios를 사용하는 코드를 작성하지 않아도 되고, API를 사용하는 컴포넌트에서는 API 모듈을 import만 하면 되기 때문에 코드의 가독성이 좋아진다.

</br>
</br>
</br>

# API 모듈화 방법

### API 모듈 폴더 구조 📁

</br>
[참조1 📌](https://velog.io/@hsk10271/TIL-33) 블로그를 참조해 폴더 구조를 만들었다.

> - 📁 apis
>   - 📁 api (api 요청/응답 코드 작성)
>     - 📄 posts.ts (게시글 관련 API)
>   - 📁 services (데이터 정제해주는 함수)
>   - 📁 utils (axios 인스턴스 생성)
>     - 📄 instance.ts (axios 인스턴스 생성)

</br>

### Axios Instance 생성 🏭

axios의 .config 메서드를 이용해 인스턴스를 생성할 수 있다.

```ts
//create axios instance
const baseUrl = "http://localhost:4000/posts/";

const instance = axios.create({
  baseURL: "http://localhost:3000",
  timeout: 1000,
  headers: {
    "Content-Type": "application/json",
  },
});
```

### 인스턴스와 config ⚙️

</br>

axios 인스턴스를 생성할 때, config를 넣어줄 수 있고, 일반적인 Instance 와 인증이 필요할때 사용할 Instance 를 나누었다.

```ts
import axios from "axios";

const baseUrl = "http://localhost:4000/";

const axiosApi = (url: string, options?: object) => {
  const instance = axios.create({
    baseURL: url,
    timeout: 2000,
    ...options,
  });
  return instance;
};

const axiosAuthApi = (url: string, options?: object) => {
  const token = "";
  const instance = axios.create({
    baseURL: url,
    timeout: 2000,
    headers: { Authorization: "Bearer " + token },
    ...options,
  });
  return instance;
};

export const defaultInstance = axiosApi(baseUrl);
export const authInstance = axiosAuthApi(baseUrl);
```

</br>
</br>
</br>

### API 사용하기 📌

walking.ts 에서 게시글 정보를 가져올때 해당 인스턴스를 사용하였다.

```ts
import { defaultInstance } from "@/apis/utils/instances";

export const getWalking = async () => {
  try {
    const response = await defaultInstance.get("/walking");
    return response.data;
  } catch (error) {
    console.log(error);
    alert("에러 발생 db.json 확인 및 콘솔확인");
  }
};

``;
```

# 마무리 🎉

프로젝트가 진행되고 백엔드 API 가 완성되어 본격적으로 API 요청이 많이지면 다시 수정할 내용이 많아질 것 같다.
