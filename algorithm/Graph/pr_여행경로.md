# **\[파이썬\] 프로그래머스 여행경로 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/43164
<br>


---

### **풀이**

#### **방향성 생각**
* DFS + 백트래킹
* 티켓 수 백트래킹해주기

<br>

---

### **전체코드**
```python
'''
티켓 수 백트래킹
'''
from collections import defaultdict as dd

def solution(tickets):
    
    # 전체 티켓 수
    T = len(tickets)

    # 티켓 출발 도착 개수 받아오기
    infos = dd(lambda :dd(int))
    for a,b in tickets:
        infos[a][b] += 1
    
    def dfs(path,T):
        nonlocal infos,paths
        
        # 현재 위치
        dep = path[-1]
        
        # 티켓 모두 소모했으면 경로 추가
        if T==0:
            paths.append(path)
            return
            
        # 티켓 정보
        for arv,t in infos[dep].items():
		        # 티켓 있으면 티켓 사용하고 DFS
            if t:
                infos[dep][arv] -= 1
                dfs(path+[arv],T-1)
                infos[dep][arv] += 1
    paths = []
    dfs(['ICN'],T)
    paths.sort()

    return paths[0]
```

#### **코멘트**

* 기본 DFS 백트래킹 문제.
* 탈출 조건만 잘 걸어주기.
* 리스트 + 연산이 코스트가 좀 있다.
* 리스트가 길다고 느껴지면 extend를 사용하기