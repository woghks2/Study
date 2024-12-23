# **\[파이썬\] 프로그래머스 : 배달 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/12978
<br>


---

### **풀이**

#### **방향성 생각**
* 다익 기본

<br>

---

### **전체코드**
```python
import heapq as hq

def solution(N, road, K):
    
    graph = [[] for _ in range(N+1)]
    for a,b,cost in road:
        graph[a].append((b,cost))
        graph[b].append((a,cost))
    
    V = [float('inf')]*(N+1)
    V[1] = 0
    heap = [(0,1)]
    while heap:
        t,x = hq.heappop(heap)
        
        for nx,cost in graph[x]:
            nt = t+cost
            if nt < V[nx]:
                V[nx] = nt
                hq.heappush(heap,(nt,nx))
                
    return sum([v<=K for v in V])        

```

#### **코멘트**

* 쉬운 문제