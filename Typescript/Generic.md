# **TypeScript Generics**

### **Typescript Generic이란?**

- **Generic**이란 함수나 클래스를 호출하는 쪽에서 지정할 수 있는 타입을 말한다. 이를 통해 함수나 클래스에서 사용할 수 있는 타입의 유연성을 높일 수 있다.

- Generic은 타입 파라메터로서 **`< >`** 안에 정의된다. 예를 들어, **`<T>`**는 generic type parameter이다.
- 일반적으로 T를 사용하며 이는 호출시 **`<User>`** 같은 방법으로 사용한다.
- 함수의 parameter 와는 별개의 타입만을 위한 변수라고 생각하자.
  </br>
  </br>
  </br>

# 프로젝트에서 사용한 예시, post 함수

> axiosInstance 를 사용하여 post 요청을 보내는 함수를 만들었다. 이때, post 함수는 generic type을 사용하여, post 요청을 보낼 때, **`어떤 타입의 데이터를 받을지를 지정할 수 있도록 하였다.`**
>
> post 함수에서 리턴의 타입을 명확하게 명시하지는 않았지만 return 값이 response.data 라는 것을 Typescript가 보고 알아서 타입을 추론해준다.

```
typescriptCopy code
import commonInstance from './commonInstance';

interface User {
  id: number;
  name: string;
  email: string;
}

const createUser = async (user: User): Promise<User> => {
  const url = '/users';
  const response = await post<User>(url, user);
  return response;
}

const post = async <T = any>(
  url: string,
  body?: any,
  config?: AxiosRequestConfig
) => {
  try {
    const response = await commonInstance.post<T>(url, body, config);
    return response.data;
  } catch (error) {
    throw error;
  }
};

// Usage example
const user: User = {
  id: 1,
  name: 'John',
  email: 'john@example.com',
};

createUser(user)
  .then((response) => console.log(response))
  .catch((error) => console.error(error));

```

### **`post` 함수와 Generic 사용 예시**

- **`post`** 함수는 **`<T = any>`** 라는 generic type parameter를 가지고 있다. 이는 함수가 **`T`** 타입의 값을 리턴한다는 것을 의미한다. (만약 타입이 지정되지 않았다면 **`any`** 타입을 리턴한다.)

- **`post`** 함수가 호출될때 generic type parameter를 원하는 리턴 타입으로 지정할 수 있다. 예를 들어, **`post<User>(url, body)`** 는 **`User`** 타입의 값을 리턴한다.

- **`instance.post`** 함수는 **`post`** 함수 내부에서 호출되며 generic type parameter를 가지고 있다. 이 파라메터는 API 호출의 예상되는 리턴 타입을 지정한다. 예를 들어, **`instance.post<User>(url, body)`** 는 예상되는 리턴 타입이 **`User`** 라는 것을 의미한다.

- **`post`** 함수에서 generic을 사용하면 API 호출시 더 큰 유연성과 타입 안전성을 얻을 수 있다.

> 함수의 사용 예시를 살펴보면
>
> **`User`** interface를 생성하고, **`createUser`** 함수를 만들었다. 이 함수는 **`post`** 함수를 사용하여 새로운 유저를 생성하는 POST 요청을 보낸다. **`post`** 함수는 **`User`** 타입의 generic type parameter를 사용하며, 이는 예상되는 리턴 타입이 **`User`** 객체라는 것을 의미한다.
