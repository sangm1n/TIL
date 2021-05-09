---
title: ［파이썬 / Python］ 코딩 테스트를 위한 파이썬 라이브러리
tags: ['Python']
categories: ['Etc']
date: 2020-11-21
---

## 유용한 라이브러리
### 내장 함수
파이썬에는 굳이 `import` 명령어를 사용하지 않아도 기본적으로 쓸 수 있는 내장 함수가 있다. 

- 입출력 관련 : input() / print()
- 최대 / 최소 : max() / min()
- 수식 계산 : eval()

### itertools
`itertools`는 반복되는 데이터를 처리하도록 도와준다. 가장 유용하게 사용할 수 있는 클래스는 permutations와 combinations이다.

- permutations : 순열 (뽑아서 나열)
```python
from itertools import permutations

data = ['A', 'B', 'C']
result = list(permutations(data, 3))

# result : [('A', 'B', 'C'), ('A', 'C', 'B'), ('B', 'A', 'C'),  ('B', 'C', 'A'), 
#         ('C', 'A', 'B'), ('C', 'B', 'A')]
```

- combinations : 조합 (순서 고려 X)
```python
from itertools import combinations

data = ['A', 'B', 'C']
result = list(combinations(data, 2))

# result : [('A', 'B'), ('A', 'C'), ('B', 'C')]
```

### heapq
`heapq`는 다익스트라 알고리즘 뿐만 아니라 다양한 알고리즘에서 우선순위 큐를 구현하고자 사용된다. 파이썬의 힙은 기본적으로 최소 힙으로 구성되어 있다.

- 삽입 : heapq.heappush()
- 삭제 : heapq.heappop()

### bisect
`bisect`는 이진 탐색을 쉽게 구현할 수 있다. 특히 정렬된 배열에서 특정한 원소를 찾는 경우 매우 효과적이다. 

- bisect_left(a, x) : 리스트 a에 데이터 x를 삽입할 가장 왼쪽 인덱스를 찾는 메서드
- bisect_right(a, x) : 리스트 a에 데이터 x를 삽입할 가장 오른쪽 인덱스를 찾는 메서드

```python
from bisect import bisect_left, bisect_right

a = [1, 2, 4, 4, 8]
x = 4
bisect_left(a, x)
bisect_right(a, x)

# result : 2, 4
```

### collections
사실 위에 있는 heapq나 bisect는 써본 적이 없어 생소하지만 `collections` 라이브러리는 자주 사용했다. 특히 `deque`를 많이 썼는데 시간복잡도가 *O(1)* 로 굉장히 빠르다.

#### deque
- 삽입 : append()
- 삭제 : popleft()

#### Counter
`Counter`는 리스트가 주어졌을 때 해당 객체 내부의 원소가 몇 번씩 등장했는지를 알려준다. 말 그대로 빈도 수를 카운트해준다고 보면 된다.

### math
- 팩토리얼 : math.factorial()
- 제곱근 : math.sqrt()
- 최대공약수 : math.gcd(a, b)
- 기타 : math.pi / math.e

---

#### 참고
- *이것이 취업을 위한 코딩 테스트다*