# **\[파이썬\] 프로그래머스 : 행렬 테두리 회전하기 (레벨2)**

- https://school.programmers.co.kr/learn/courses/30/lessons/77485

<br>

---

### **풀이**

#### **방향성 생각**

- 덱을 이용해서 테두리 회전 구현하기.

<br>

---

### **전체코드**

```python
from collections import deque

def solution(H,W,commands):

    # array 생성
    arr = [[i*W+j+1 for j in range(W)] for i in range(H)]

    # 0 based로 만들어주기
    queries = []
    for cmd in commands:
        query = list(map(lambda x:x-1,cmd))
        queries.append(query)

    # 쿼리 실행
    answer = []
    for py,px,ny,nx in queries:

        # 위치 index와 value를 담을 덱을 생성
        index = deque()
        value = deque()

        # 테두리를 순서대로 읽어서 덱에 넣는다.
        x,y = px,py
        for dx in range(nx-px):
            index.append((px+dx,y))
            value.append(arr[y][px+dx])
        for dy in range(ny-py):
            index.append((nx,py+dy))
            value.append(arr[py+dy][nx])
        for dx in range(nx-px):
            index.append((nx-dx,ny))
            value.append(arr[ny][nx-dx])
        for dy in range(ny-py):
            index.append((px,ny-dy))
            value.append(arr[ny-dy][px])

        # 최솟값을 정답에 넣어주고
        answer.append(min(value))

        # index는 고정시키고 value를 회전시켜서 회전을 구현한다. % 연산자를 이용해도 무방방
        value.rotate()
        for (x,y),val in zip(index,value):
            arr[y][x] = val

    return answer
```

#### **코멘트**

-
