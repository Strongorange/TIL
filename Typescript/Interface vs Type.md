# Interface vs Type

지금까지 Typescript를 써오면서 객체에는 Interface, 유니온 타입에는 type을 쓰는 식으로 사용하다가 프로젝트를 진행하며 다음 상황을 발견했다.

![](https://velog.velcdn.com/images/strongorange/post/762f5902-c082-4d95-9947-33d47ab971b4/image.png)

![](https://velog.velcdn.com/images/strongorange/post/21cd8bfc-0b53-4cd3-989e-db14de0420ec/image.png)

위와 같이 객체를 정의할때 type으로 정의하면 마우스 호버시에 키 값과 타입을 볼 수 있고 interface는 보이지 않는다.  
이 때문에 interface로 정의된 객체의 속성을 확인하기 위해서 command(control) + 클릭으로 해당 interface가 선언된 파일을 열어서 확인하며 사용했는데 이럴거면 type을 쓰는게 낫지 않을까 싶어서 interface와 type의 차이점을 비교해보기로했다.




## 확장성
공식문서에 확장성에 관해 다음과 같은 내용이있다.
> Type aliases and interfaces are very similar, and in many cases you can choose between them freely. Almost all features of an interface are available in type, the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

대부분의 기능은 type과 interface 모두에서 사용할 수 있지만 중요한 차이는 type 과 interface의 확장성에 있다


`interface`는 다른 `interface`를 확장할 수 있어 코드의 재사용성이 높고 
`type`은 | , &을 ㅣㅇ용해서 확장할 수 있지만 확장이나 조합이 덜 직관적이다

```typescript
interface A {}
interface B extends A {}

type C = {};
type D = C & {};

```

## 선언 병합
`interface`는 같은 이름으로 여러번 선언하면 선언된 내용이 자동으로 병합된다.
`type`의 경우 선언 병합이 불가능

```typescript
interface User {
  name: string;
}

interface User {
  age: number;
}

// User: { name: string; age: number; }

type UserType = {
  name: string;
};

// 아래 코드는 오류 발생
// type UserType = {
//   age: number;
// };

```

이 외에도 정말 많은 차이들이 존재하겠지만
[참고1](https://stackoverflow.com/questions/37233735/interfaces-vs-types-in-typescript)
[참고2](https://levelup.gitconnected.com/mastering-typescript-a-guide-to-choosing-between-type-and-interface-c31d3527693b?gi=e6edeef65e4c) 
를 참고하여 기본적으로 interface를 사용할 수 있는 상황에서는 interface를 사용하기로 결정했다. 

## interface 미리보기
VSC에서 마우스 호버시 미리 보는 기능은 키보드 단축키에 Peek Type Definition 설정을 적용해 방법을 추가했다.

![](https://velog.velcdn.com/images/strongorange/post/145a5168-1ff9-445a-8fa3-a84aa66eadba/image.png)

