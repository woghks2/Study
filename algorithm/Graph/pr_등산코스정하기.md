# **\[파이썬\] 프로그래머스 : 등산코스 정하기 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/118669
<br>


---

### **풀이**

#### **방향성 생각
* 기본 다익스트라 문제.
* 시작점에서 봉우리에 도착하는 최소 강도를 구한다.
* 현재 경로까지 강도를 힙에 넣어주면서 간선을 이동하면서 최대값을 갱신하는 경우에 최대 강도를 변경한다.
* 입력 크기가 큰 편이라서 각 Node마다 다익스트라를 돌리면 $(NlogN)^2$라서 터져버린다.
* 시작점 gates를 한 번에 힙에 넣고, summits에 도달할 때 마다 정답 후보에 넣는다.
* 마지막에 answer을 정렬해주고 리턴하면 끝
* summits이 $50000$이라서 summits 유무를 list 대신 set으로 관리한다.
* 또, edges의 수가 많아서 더미 데이터가 힙에 쌓일 수 있으므로 제거해준다.

<br>

---

### **전체코드**
```python
import heapq as hq

def solution(N, paths, gates, summits):

    gates,summits = set(gates),set(summits)
    G = [[] for _ in range(N+1)]
    for a,b,cost in paths:
        G[a].append((b,cost))
        G[b].append((a,cost))
    
    answer = []
    heap = []
    for gate in gates:
        hq.heappush(heap,(0,gate))
        V = [1e9]*(N+1)
        V[gate] = 0
        
    while heap:
        t,x = hq.heappop(heap)

        # 더미 데이터 패스
        if t > V[x]:
            continue

        for nx,cost in G[x]:
            
            nt = max(t,cost) # 이동 간선
            if nx in summits: # 봉우리면 나갈 필요가 없다. 올라온 간선으로 내려가면 된다. 힙에 넣지 않고 기록만
                V[nx] = min(V[nx],nt)
                answer.append((nx,nt))
            else:
                if nt < V[nx]:
                    hq.heappush(heap,(nt,nx))
                    V[nx] = nt

    answer.sort(key=lambda x: (x[1],x[0]))
    return answer[0]
```

#### **코멘트**

* 기본 다익