# **\[파이썬\] 프로그래머스 : 다단계 칫솔 판매 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/77486
<br>


---

### **풀이**

#### **방향성 생각**
* 트리 형태
* DFS를 이용해서 각 판매마다 부모를 타고 올라가면서 수익 전달하기
* 탈출 조건은 root거나 전달 수익이 없는 경우

<br>

---

### **전체코드**
```python
from collections import defaultdict as dd
import sys
sys.setrecursionlimit(10**6)

def solution(enroll, referral, seller, amount):
    
    G = dd(str) # 부모 연결
    for c,p in zip(enroll, referral):
        G[c] = p

    answer = dd(int)
    def dfs(s,a):
        nonlocal G
        if not G[s]:
            return
        if a < 1:
            answer[s] += a
            return
        answer[s] += a-a//10
        dfs(G[s],a//10)
        
    for s,a in zip(seller,amount):
        dfs(s,a*100)
    
    return [answer[name] for name in enroll]
```

#### **코멘트**

* .