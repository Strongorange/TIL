`PriceCrush` 프로젝트 진행 중 상품 검색, 카테고리별 필터링 기능을 작업하던 중 아직 카테고리별 조회, 검색, 페이지 정보 API가 완성되지 않아 `전체 상품 조회 API` 하나로 작업을 해야하는 상황에서 `Recoil SelectorFamily`를 공부하게되었다.

[Recoil Selector 공부글 🌐](https://velog.io/@strongorange/Recoil-Selector) 에서는 기본적인 `Selector` 기능을 공부했고 이번엔 실제 프로젝트에서 적용한 점을 기록하려한다.

## 서버에서 데이터 가져오기

현재 `전체 상품 조회 API` 에서 받아오는 데이터들은 다음 `ProductFromApi`의 `Array` 형태를 가진다.

```typescript
export type productCategoryType = {
  id: string;
  imgurl: string;
  name: string;
};

export type ProductFromApi = {
  id: "string";
  name: "string";
  start_price: number;
  desc: "string";
  start_date: string;
  end_date: string;
  deletedAt: string | null;
  productCategory: productCategoryType;
};
```

서버에서 받은 데이터를 RecoilState로 담아 보관한다.
`Recoil 상태 초기화`

```typescript
export const productFromServerState = atom<ProductFromApi[]>({
  key: "productFromServerState",
  default: [],
  effects_UNSTABLE: [persistAtom],
});
```

`SelctorFamily`

```typescript
// selectorFamily 가 리턴할 데이터 타입
// 현재 페이징, 카테고리 API가 작업중이라 전체 상품에서 페이징, 카테고리 필터링까지 적용
interface FilteredProductsByCategoryStateProps {
  result: ProductFromApi[];
  totalItems: number;
  currentPage: number;
  lastPage: number;
}

export const filteredProductsByCategoryState = selectorFamily<
  // 제네릭의 첫번째 인자는 리턴 타입
  FilteredProductsByCategoryStateProps,
  // 두번째 인자는 SelectorFamily를 사용할때의 Parameter
  {
    categoryId: string;
    currentIndex: number;
    itemsPerPage: number;
  }
>({
  key: "filteredProductsByCategoryState",
  get:
    ({ categoryId, currentIndex, itemsPerPage }) =>
    ({ get }) => {
      const startIndex = (currentIndex - 1) * itemsPerPage;
      const endIndex = startIndex + itemsPerPage;
      const products = get(productFromServerState);
      if (categoryId === "all") {
        return {
          result: products.slice(startIndex, endIndex),
          totalItems: products.length,
          currentPage: currentIndex,
          lastPage: Math.ceil(products.length / itemsPerPage),
        };
      }
      const filteredProducts = products.filter(
        (product) => product.productCategory.id === categoryId
      );

      const result = filteredProducts.slice(startIndex, endIndex);
      return {
        result,
        totalItems: filteredProducts.length,
        currentPage: currentIndex,
        lastPage: Math.ceil(filteredProducts.length / itemsPerPage),
      };
    },
});
```

위 코드의 filteredProductsByCategoryState는 `selectorFamily`를 사용하여 `parameter`를 받아 함수를 실행시켜 거기에 맞는 값을 리턴해주는 동작을 수행한다.

`컴포넌트에서 사용`

```typescript
 const filteredProducts = useRecoilValue(
    filteredProductsByCategoryState({
      categoryId: currentCategory,
      currentIndex: currentPage,
      itemsPerPage,
    })
```

`useRecoilValue`에 인자들을 넘겨서 사용한다!
