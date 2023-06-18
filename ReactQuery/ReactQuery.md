# React-Query

## 왜 사용하게 되었나?

밥 케미 프로젝트를 진행하면서, 서버에서 받아온 데이터를 캐싱하고, 데이터 요청 과정을 수동으로 관리하기보다 라이브러리를 사용하는 것이 좋을 것 같아서 사용하게 되었고 무한 스크롤 기능을 구현했던 기억이 있는데 기억이 나지 않아서... 다시 공부할겸 기록하며 사용하게 되었다.

[공식 홈페이지 문서](https://tanstack.com/query/v3/docs/react/guides/infinite-queries)의 version3 를 참조했으며 현재 TanStack Query 라는 이름으로 이름이 바뀌고 5버전까지 나온 것 같은데 일단 3버전을 공부한 내용을 정리한다.

[7 Tips for Using React Query in Large Projects](https://betterprogramming.pub/7-tips-for-using-react-query-in-large-projects-22ccc49d61c2) 이라는 블로그가 추천하는 구조를 적용해보는 것을 목표로 하고 있다.

QueryClient등 기본적인 설정은 제외하고 공식문서를 바탕으로 공부한 것을 정리한다.

## Query Basics

쿼리는 `고유 키`(Query Key)에 연결된 비동기 데이터 소스에 대한 선언적 종속성입니다. 쿼리는 GET 및 POST 메서드를 포함한 Promise 기반 메서드와 함께 사용하여(`Query Function, 쿼리 함수`) 서버에서 데이터를 가져올 수 있습니다. 만약 메소드가 서버의 데이터를 수정한다면, `Muttions`를 대신 사용하는 것을 추천한다. 구성 요소 또는 커스텀 훅에서 쿼리를 구독하려면 `useQuery` hook을 호출하고 다음을 수행합니다:

- 고유한 쿼리 키
- 다음의 프로미스를 리턴하는 함수
  - 데이터를 Resolve 하거나
    - 에러를 throw

```typescript
import { useQuery } from "react-query";

function App() {
  const info = useQuery("todos", fetchTodoList);
}
```

`todos`에 해당하는 `고유 키`는 내부적으로 데이터를 가져오고, 캐싱하고 어플리케이션에서 데이터를 공유하는데 사용된다.

## Query Key(쿼리 키)

기본적으로 React Query는 `Query Key`를 통해 데이터를 캐싱한다.
`query key`는 단순히 `string`이 될 수도 있고, 쿼리를 식별하기 위한 더 자세한 정보가 있다면
`Array`를 사용할 수 있다.

- 계층적 또는 중첩된 리소스
  - ID, index 를 `쿼리 키`로 사용하는건 일반적
- 쿼리와 추가 파라미터
  - 추가적인 파라미터를 사용하는 건 일반적

```typescript
// An individual todo
useQuery(['todo', 5], ...)
// queryKey === ['todo', 5]

// An individual todo in a "preview" format
useQuery(['todo', 5, { preview: true }], ...)
// queryKey === ['todo', 5, { preview: true }]

// A list of todos that are "done"
useQuery(['todos', { type: 'done' }], ...)
// queryKey === ['todos', { type: 'done' }]

// 밥 케미 프로젝트에서 [string, number, number] 로 구성된 쿼리 키
useInfiniteQuery(
    [RestaurantsHooksEnum.NearByRestaurants, currentLocation.latitude, currentLocation.longitude],
    async ({ pageParam = '' }) => ....
```

### 쿼리 함수에 매개변수에 의존한다면 매개변수를 쿼리 키에 추가

```typescript
function Todos({ todoId }) {
  const result = useQuery(["todos", todoId], () => fetchTodoById(todoId));
}
```

## Query Function(쿼리 함수)

쿼리 함수는 `프로미스 Promise`를 리턴하는 어떤 함수도 될 수 있지만 `resolve`와 `error`를 포함하는 함수일 것을 권장

### 에러 핸들링과 Throw

React-Query가 쿼리 오류를 확인하려면 쿼리 함수가 error를 throw해야하고 throw된 에러는 result의 `error` 상태로 확인할 수 있다.

```typescript
// error 는 thorw된 에러
const { error } = useQuery(["todos", todoId], async () => {
  if (somethingGoesWrong) {
    throw new Error("Oh no!");
  }

  return data;
});
```

`Axios, graphql-request`같은 유틸리티를 이용해 통신을 하는 경우 자동적으로 실패한 요청에 대해 throw를 해주지만 `fetch`를 이용할 경우 thorw를 직접 해줘야한다.

### 쿼리 함수의 변수

`쿼리 키`는 단순히 데이터를 식별하는데 쓰일 뿐만 아니라 쿼리 함수에 전달된어 사용하고 싶을때 사용할 수 있다.

```typescript
function Todos({ status, page }) {
  const result = useQuery(["todos", { status, page }], fetchTodoList);
}

// 쿼리함수에서 쿼리 키, status, page 변수에 접근이 가능하다!
function fetchTodoList({ queryKey }) {
  const [_key, { status, page }] = queryKey;
  return new Promise();
}
```

### Query parameter 대신 Query Object 사용하기

`쿼리 키`, `쿼리 함수`, `config`를 명시적으로 사용할 수 있다.

```typescript
import { useQuery } from "react-query";

useQuery({
  queryKey: ["todo", 7],
  queryFn: fetchTodo,
  ...config,
});
```

## Infinite Query

pagination을 구현하거나 추가적인 데이터를 서버에서 요청할때 `infinite query`를 사용할 수 있다.
`더보기`, `무한 스크롤` 등을 구현할때 자주 사용했는데 튜토리얼 뒤져가면서 동작하는 코드는 만들었는데
공식 가이드를 뜯어보는 것은 처음이다.

### useQuery와의 차이점

`useInfiniteQuery`는 `useQuery`와 몇 가지 다른점들이 존재한다.

- `data`는 이제 infinite query data를 가지는 object이다.
- `data.pages` 배열은 불러온 페이지를 포함한다.
- `data.pageParams` 배열은 데이터를 fetch하는데 사용된 param들을 포함한다.
- `fetchNextPage`와 `fetchPreviousPage` 함수를 사용 가능하다.
- `getNextPageParam`과 `getPreviousPageParam`을 `fetchNextPage`, `fetchPreviousPage` 함수에서 추가 데이터를 fetch할때의 매개변수로 사용 가능하다.
- `hasNextPage` boolean은 `getNextPageParam`이 값을 리턴할때 **true**이고 아닐 경우 **undefined**
- `isFetchingNextPage`, `isFetchingPreviousPage` boolean 상태 사용 가능

### 예시

매개변수 `커서`와 함께 `커서 인덱스`를 기반으로 한 번에 3개의 프로젝트 페이지를 반환하는 API가 있고

```typescript
fetch("/api/projects?cursor=0");
// { data: [...], nextCursor: 3}
fetch("/api/projects?cursor=3");
// { data: [...], nextCursor: 6}
fetch("/api/projects?cursor=6");
// { data: [...], nextCursor: 9}
fetch("/api/projects?cursor=9");
// { data: [...] }
```

`더보기` 기능을 구현한다면

- `useInfiniteQuery`를 사용해 첫번째 데이터를 받아오고
- 다음 쿼리 요청을 위해 `getNextPageParam`안에서 다음 요청을 위한 매개변수를 리턴
- `fetchNextPage` 함수를 실행

> `fetchNextPage` 함수를 실행할때 `getNextPageParam` 함수에서 반환된 `pageParam`을 덮어쓰지 않으려면 paramerter를 사용하지 않는 것이 중요함.

```typescript
import { useInfiniteQuery } from 'react-query'

function Projects() {
  const fetchProjects = ({ pageParam = 0 }) =>
    fetch('/api/projects?cursor=' + pageParam)

  const {
    data,
    error,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    status,
  } = useInfiniteQuery('projects', fetchProjects, {
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  })
```

#### useInfiniteQuery 에서 getNextPageParam

뜯어보자 먼저 `useInfiniteQuery`를 살펴보면

- `쿼리 키`로 'projects'를 가지고
- `쿼리 함수`로 'fetchProjects'를
- `config`에 `getNextPageParam` 함수가 있다
  - `lastPage`는 직전 요청에서 가져온 데이터
    - `pages`는 지금까지 가져온 데이터

`getNextPageParam` 함수에서 **lastPage.nextCursor** 를 리턴하고 있는데

```typescript
fetch("/api/projects?cursor=0");
// { data: [...], nextCursor: 3}
fetch("/api/projects?cursor=3");
// { data: [...], nextCursor: 6}
fetch("/api/projects?cursor=6");
// { data: [...], nextCursor: 9}
fetch("/api/projects?cursor=9");
// { data: [...] }
```

직전에 가져온 데이터(lastPage)에서 nextCursor인 숫자를 의미하고 존재하면 리턴하고 존재하지 않으면 undefined를 리턴한다.

#### 쿼리 함수에서 pageParam

`쿼리 함수`인 `fetchProjects` 함수의 `pageParam`은 위에서 리턴한 값을 의미한다!
위에서 리턴한 값이 없으면 pageParam은 undefined, 존재하면 리턴한 값을 사용해 데이터를 fetch한다.

#### fetchNextPage를 사용하기

이제 작동을 이해했으니 실제로 데이터 요청이 필요한 곳에서 `fetchNextPage`를 실행시키면 데이터를 받아올 수 있다.

#### 구글 주변 장소 API에서 사용하기

밥케미 프로젝트에서 주변 장소를 요청하면 한번에 최대 20개의 결과와 서버에 추가적인 데이터가 있다면 `next_page_token`이라는 토큰을 주고 해당 토큰을 넘겨서 요청을 보내면 추가적인 장소를 최대 20개를 리턴해준다.

이를 위에서 정리한 `InfiniteQuery`를 사용해서 데이터 요청을 처리했다.

**쿼리 함수와 요청 함수**

```typescript
// InfiniteQuery (쿼리 키, 쿼리 함수, config)
const { data, fetchNextPage, isFetchingNextPage, hasNextPage, isFetching } =
  useInfiniteQuery(
    [
      RestaurantsHooksEnum.NearByRestaurants,
      currentLocation.latitude,
      currentLocation.longitude,
    ],
    // 쿼리함수 pageParam은 config의 getNextPageParam이 리턴하는 값.
    // 처음 요청시의 pageParam 은 ''
    async ({ pageParam = "" }) =>
      restaurantsRequest.getNearByRestaurants(
        currentLocation.latitude,
        currentLocation.longitude,
        pageParam
      ),
    {
      // getNextPageParam이 return하는 값이 쿼리 함수의 pageParam이 된다.
      getNextPageParam: (lastPage) => lastPage?.nextPageToken || null,
      refetchOnWindowFocus: false,
    }
  );

// 데이터 요청 함수
import { GOOGLE_MAPS_API_KEY } from "@env";
import axios from "axios";

export const restaurantsRequest = {
  // 현재 위치 주변의 식당 정보 가져오기
  getNearByRestaurants: async (
    lat: number,
    lon: number,
    nextPageToken: string | null = null
  ) => {
    let reqUrl = `https://maps.googleapis.com/maps/api/place/nearbysearch/json?location=${lat},${lon}&radius=1500&type=restaurant&key=${GOOGLE_MAPS_API_KEY}`;
    if (nextPageToken) {
      reqUrl += `&pagetoken=${nextPageToken}`;
    }

    try {
      const res = await axios.get(reqUrl);
      if (res.status === 200) {
        const { results: nearByRestaurants, next_page_token: nextPageToken } =
          res.data;
        return { nearByRestaurants, nextPageToken };
      }
    } catch (error) {
      console.log(error);
    }
  },
};
```

### 여러 컴포넌트에서 쿼리 사용하기

쿼리는 data 이외에도 `fetchNextPage`, `hasNextPage` 등등 다양한 결과들을 리턴한다.
밥 케미 프로젝트에서는 data를 처음 불러오는 컴포넌트와 무한 스크롤을 요청하는 컴포넌트가 달랐는데
이를 위해 동일한 쿼리를 또 다시 작성해야하는 불편함이 있어 커스텀훅을 작성하여 여러 컴포넌트에서 사용하기 편하게 만들었다.

**restaurants.hooks.ts**

```typescript
import { useInfiniteQuery } from "react-query";
import { useRecoilValue } from "recoil";
import { restaurantsRequest } from "@/api/restaurantsRequest";
import { currentLocationAtom } from "@/recoil/atoms/currentLocationAtom";

export enum RestaurantsHooksEnum {
  NearByRestaurants = "nearByRestaurants",
}

export const useRestaurantsQuery = () => {
  const currentLocation = useRecoilValue(currentLocationAtom);
  const { data, fetchNextPage, isFetchingNextPage, hasNextPage, isFetching } =
    useInfiniteQuery(
      [
        RestaurantsHooksEnum.NearByRestaurants,
        currentLocation.latitude,
        currentLocation.longitude,
      ],
      async ({ pageParam = "" }) =>
        restaurantsRequest.getNearByRestaurants(
          currentLocation.latitude,
          currentLocation.longitude,
          pageParam
        ),
      {
        getNextPageParam: (lastPage) => lastPage?.nextPageToken || null,
        refetchOnWindowFocus: false,
      }
    );
  return { data, fetchNextPage, isFetchingNextPage, hasNextPage, isFetching };
};
```

**Home.tsx**

```typescript
...
const { data } = useRestaurantsQuery()
...
```

**NearByRestaurants.tsx**

```typescript
const NearByRestaurants = ({ route }: NearByRestaurantsProps) => {
  // Recoils
  const nearByRestaurants = useRecoilValue(nearByRestaurantsAtom);

  // React-Query
  const { fetchNextPage, hasNextPage, isFetchingNextPage } =
    useRestaurantsQuery();

  const renderRestaurantCard = ({
    item,
    index,
  }: {
    item: any;
    index: number;
  }) => {
    return <RestaurantCard item={item} index={index} />;
  };

  return (
    <View style={{ flex: 1, backgroundColor: "blue", width: "100%" }}>
      <FlatList
        style={{ width: "100%", flex: 1 }}
        data={nearByRestaurants}
        renderItem={renderRestaurantCard}
        contentContainerStyle={{ width: "100%", backgroundColor: "#e8eff5" }}
        ItemSeparatorComponent={() => <View style={{ height: 10 }} />}
        // 스크롤의 끝에 가까워질때 hasNextPage가 true이면 fetchNextPage를 실행시켜 데이터 fetching
        onEndReached={() => hasNextPage && fetchNextPage()}
        ListFooterComponent={() =>
          isFetchingNextPage ? (
            <ActivityIndicator size={32} color={theme.colors.primary} />
          ) : null
        }
      />
    </View>
  );
};
```

## 마무리

처음에는 데이터를 신선하게 유지하기위해 react-query에 입문했다. 이후 페이지네이션, 더보기를 구현하기 위해, 앱 개발하면서 무한 스크롤을 구현하기 위해 사용하곤 했는데 원하는 기능의 튜토리얼을 따라하다가 막히는 부분은 급급하게 구글과 StackOverflow에서만 찾아보고 시간을 들여 공식문서를 보며 공부했던 기억은 없었다.

특히 `쿼리 키`가 `쿼리 함수`의 매개변수로 넘어간다는 것과 `InfinityQuery`는 구현해놓고 안쓰다보면
잊어버리고를 반복하곤 했는데 이번에 공부하고 정리하며 동작 원리를 잘 익힌 것 같다.

또 커스텀 훅을 사용해 컴포넌트간 공유가 쉬운 커스텀 훅의 사용을 제안한 글 서두의 블로그 방법은 꼭 react-query 뿐만 아니라 다른 부분에도 적용할 수 있을 것 같다!
