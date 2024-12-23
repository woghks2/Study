# **\[파이썬\] 프로그래머스 : 부대복귀 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/132266?language=python3


---

### **풀이**

#### **방향성 생각**
* 모든 부대원이 출발 지점에 도착하는지 체크 -> 도착지에서 BFS 돌려서 각 부대원까지 거리 체크
* 간선 가중치가 모두 1이므로 BFS로 간단하게 풀이 가능

<br>

---

### **전체코드**
```python
from collections import deque, defaultdict as dd

def solution(n, roads, sources, destination):
    
    # 간선 정보 받아주고
    graph = dd(list)
    for a,b in roads:
        graph[a].append(b)
        graph[b].append(a)

    # V에 걸린시간 기록하기
    V = [-1]*(n+1)
    V[destination] = 0
    Q = deque([(destination,0)])
    while Q:
        x,t = Q.popleft()
        for nx in graph[x]:
            if V[nx] == -1:
                V[nx] = t+1
                Q.append((nx,t+1))
    # 각 부대원이 목적지까지 걸리는 시간
    answer = [V[s] for s in sources]
    return answer
```

#### **코멘트**

* 웰노운한 발상이라서 어렵지 않았음.
* 입력 크기를 보면 각 부대원에 대해서 BFS를 돌리는 문제는 아님을 쉽게 알 수 있음.