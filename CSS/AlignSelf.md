# Align-Self

React Native 프로젝트에서 채팅창을 만들다가 채팅 내용이 텍스트의 크기만큼이 아니라 화면 전체 크기만큼 늘어나는 문제가 발생했다.  
FlatList로 채팅을 띄워주고 있어서 FlatList의 `contentContainerStyle`에 `width`를 `fit-content`를 해보니 `fit-content`는 RN에서 사용할 수 가 없었고 `Text`에 속성을 줘보기도하고 여러가지를 시도해봤지만 해결되지 않았다.

**width 길이 만큼 늘어난 채팅**
![](https://velog.velcdn.com/images/strongorange/post/cb0efa62-6cea-450c-8b94-3e28ea7840e5/image.png)

## Align-Items 와 Align-Self

더 검색을 해보고 여러 방법을 적용하며 방법을 찾았는데 그것은 `align-self` 속성이었다.  
`align-self`란 부모의 `align-items` 속성을 무시하고 자신만의 `align-items` 속성을 적용하는 속성이다.

RN 에서는 기본적으로 `flex-direction`이 `column`으로 되어있고 이는 `main-axis`가 `vertical` `cross-axis`가 `horizontal`이다.  
`align-self`는 `cross-axis`를 기준으로 적용되는 속성이고 `flex-start`는 `cross-axis`의 시작점을 기준으로 적용되는 속성이다. 즉 RN에서 `align-items`의 기본값인 `stretch`가 자식인 채팅에 적용되면서 `cross-axis`의 시작점부터 끝까지 늘어나는 것이었다.

`align-self`를 `flex-start`로 적용하니 원하는대로 채팅창이 늘어나지 않았다.

## CSS

`flexbox` 의 `justify-contents`, `align-items` 속성은 안 쓰는 날이 없을정도로 자주 사용하던 속성인데 문제의 해결법이 `align-self`라는 것을 발견하고 많이 당황스러웠다.  
`main-axis` ,`cross-axis`, 개념은 잘 알고있다고 생각했는데 손에 익은 그렇지 않았나보다. 이를 계기로 생각해보니 `flex-grow`, `flex-shrink` 등 무엇인지 알고는 있지만 확실하게 알지는 못한채로 넘어가는 CSS 속성들이 많았던 것 같다.
