# **\[파이썬\] 프로그래머스 : 올바른 괄호의 개수 (레벨4)**
* https://school.programmers.co.kr/learn/courses/30/lessons/12929
<br>


---

### **풀이**

#### **방향성 생각**
* DFS로 탐색하면서 중복 방문한 노드는 메모제이션한 값으로 프루닝.

<br>

---

### **전체코드**
```python
def solution(n):
    
    dp = [[-1]*(n+1) for _ in range(n+1)]
    
    def dfs(l,r):
        
        # 맞춰서 도달 했으면 카운팅
        if (l,r) == (0,0):
            return 1
        # r을 더 많이쓰면 올바르지 않음. 괄호는 주어진 개수만 사용해야함
        if l>r or l<0 or r<0:
            return 0
        # 방문한 노드는 메모제이션
        if dp[l][r] != -1:
            return dp[l][r]
        # (썼을때랑 )썼을 때 탐색하기
        answer = dfs(l-1,r) + dfs(l,r-1)
        dp[l][r] = answer
        return answer
        
    return dfs(n,n)
```

#### **코멘트**

* 안어려운데 프로그래머스가 DP 올려치기가 좀 심한듯