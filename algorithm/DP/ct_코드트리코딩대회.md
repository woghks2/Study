# **\[파이썬\] 코드트리 : 코드트리 코딩 대회 (골드1)**
* https://www.codetree.ai/training-field/search/problems/codetree-coding-contest/description?page=33&pageSize=20
<br>


---

### **풀이**

#### **방향성 생각**
* 모든 케이스를 탐색하기에는 힘들어보임.
* 경우의 수를 줄이기 위해서 리스트 -> dict 사용 (누가 풀었는지는 중요 x, 순서고려 x)
* dict 정보를 바탕으로 DFS로 탐색

<br>

---

### **전체코드**
```python
from collections import defaultdict as dd

N,M = map(int, input().split())
infos = list(map(int, input().split()))

# 최대 집중력보다 크면 x
if max(infos) > M:
    print(-1)
else:
    # 누가 푸는지는 상관 x
    # 상태를 결정짓는 요소는 {집중1:남은, 집중력2:인원} 형태
    answer = 13
    dp = set()

    def dfs(focus,infos):
        global answer
        
        # 탈출조건
        if tuple(focus.items()) in dp:
            return
        dp.add(tuple(focus.items()))
        
        # 다 풀었으면 최소 인원 갱신
        if not infos:
            answer = min(answer,N-focus[M])
            return

        # 백트래킹 형태로
        x = infos.pop()
        for f in list(focus.keys()):
            if focus[f] and f >= x: # 문제하나 풀고
                focus[f-x] += 1 # 집중력 f가진 사람 -> f-x로
                focus[f] -= 1
                if tuple(focus.items()) not in dp:
                    dfs(focus,infos)
                focus[f-x] -= 1
                focus[f] += 1
        infos.append(x)

    focus = dd(int)
    focus[M] = N
    dfs(focus,infos)
    print(answer)
```

#### **코멘트**

* 냅색처럼 생겼는데 냅색으로 안풀려서 그냥 DFS + DP로 풀이
* 탈출 조건 위치, for문 내부 DFS 조건문에 따라서 재귀 깊이가 조금씩 달라져서 코드 동작속도가 달라진다.
  * 항상 틀대로 짠다고 빠르게 동작하지는 않음