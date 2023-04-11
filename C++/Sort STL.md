## 숫자 받고 좌우로 반전 (앞이 0이라면 생략)

1. string 타입의 데이터로 숫자를 받고
2. reverse(string.begin(), string.end()) 로 좌우 반전, 이 과정에서 앞의 0 자동으로 생략
3. stoll (string to long long) stoi (string to int) 등으로 형 변환

## CompareFunction

bool 비교함수의 리턴값이 의미하는 바는

- true: a, b중 a가 먼저 오는 것을 의미
- false: a, b중 b가 먼저 오는 것을 의미

## unique

unique(vector.begin(), vector.end()) 로 중복값 제거가 가능하지만
단독으로 사용시 중복값을 벡터의 끝 쓰레기 영역으로 보냄
[블로그](https://dpdpwl.tistory.com/39)

## erase

vector.erase(unique(vector.begin(), vector.end()), vector.end()) 로 유니크하게 정렬

## emplace_back, push_back

push_back 보다 효과적 pair를 넣을때 emplace_back(a, b) 같이 사용
push_back({a, b}) 처럼 사용

## 문자열 자르기 substr

substr(시작 인덱스, 마지막 인덱스)
substr(인덱스) - 인덱스부터 끝까지 자르기
