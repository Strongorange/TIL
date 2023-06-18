# FlatMap

## 어떻게 알게 되었나

밥케미 프로젝트 진행중 InfiniteQuery를 사용하여 데이터를 불러오면 result 안의 `data`는
각 요청의 결과의 배열이된다.

```typescript
// InfiniteQuery에서 data가 업데이트될때 콘솔로 출력
useEffect(() => {
  if (data) {
    console.log(data.pages);
  }
}, [data]);
```

**첫번째 요청 이후 data.pages**
![](https://velog.velcdn.com/images/strongorange/post/28e5c4ab-fd74-4432-88e2-2eb172a77be7/image.png)

**두번째 요청 이후 data.pages**
![](https://velog.velcdn.com/images/strongorange/post/5cccf492-99b2-4a4c-9ba5-2108c321a469/image.png)

data안의 **nearByRestaurants** 배열의 내용을 한 배열로 만들고 싶었고 `forEach`를 사용해서 작성한 코드를 `Array.map`같은 JS 메소드를 사용해서 처리할 수도 있겠다 싶어서 검색하다가 `Array.flatMap`을 알게되었다.

## 처음에 시도한 방법

처음에 시도한 방법은 빈 배열을 만들고 `data.pages` 배열을 순환하면서 `nearByRestaurants`의 내용을 빈배열에 복사하는 방법을 생각해봤다.

```typescript
useEffect(() => {
  if (data) {
    // 빈 배열을 생성하고
    let allData = [];

    // 모든 페이지를 순환하며 빈 배열에 push
    data.pages.forEach((page) => {
      allData.push(...page.nearByRestaurants);
    });

    setNearByRestaurants(allData);
  }
}, [data]);
```

## FlatMap이란?

[mdn flatMap](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap#map_%EA%B3%BC_flatmap)

> flatMap() 메서드는 먼저 매핑함수를 사용해 각 엘리먼트에 대해 map 수행 후, 결과를 새로운 배열로 평탄화합니다. 이는 깊이 1의 flat 이 뒤따르는 map 과 동일하지만, flatMap 은 아주 유용하며 둘을 하나의 메소드로 병합할 때 조금 더 효율적입니다.

핵심은 `각 엘리먼트에 대해 map 수행 후, 결과를 새로운 배열로 평탄화합니다.` 인 것 같다

### 1 depth만 평탄화

공식문서를 살펴보다가 `한 레벨만 평탄화 됨` 이라는 것을 발견했다.

```javascript
let arr1 = [1, 2, 3, 4];

arr1.map(x => [x * 2]);
// [[2], [4], [6], [8]]

arr1.flatMap(x => [x * 2]);
// [2, 4, 6, 8]

// 한 레벨만 평탄화됨
arr1.flatMap(x => [[x * 2]
```

예전에 spread operator가 1 depth 까지만 깊은 복사를 수행한다는 사실을 모르고 작업하다가 디버깅 지옥을 경험한 적이 있어서 꼭 기억해야할 것 같다.

## 적용하기

다시 위의 코드로 돌아와서

```typescript
useEffect(() => {
  if (data) {
    const allData = data.pages.flatMap((page) => page?.nearByRestaurants);
    setNearByRestaurants(allData);
  }
}, [data]);
```

`data.pages` 배열에 `flatMap`을 사용해 각 요소의 nearByRestaurants를 평탄화하여 배열에 집어넣을 수 있었다!
