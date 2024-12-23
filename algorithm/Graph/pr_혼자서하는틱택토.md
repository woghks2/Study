# **\[파이썬\] 프로그래머스 : 혼자서 하는 틱택토 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/160585
<br>


---

### **풀이**

#### **방향성 생각**
* 맵 사이즈가 충분히 작아서 DFS/BFS로 풀 수 있다.
* BFS로 모든 경우의 수를 해싱으로 기록한다.

<br>

---

### **전체코드**
```python
from collections import deque

def solution(board):
    
    # 가로 세로 대각선 체크하기. 각 턴에 맞는 사람이 완성해야함
    def over(s,atk):
        arr = [s[3*i:3*i+3] for i in range(3)]
        arr_tr = list(zip(*arr))
        if atk%2 == 0: x = 'O'
        else: x = 'X'
        if any(set(a for a in arr[i]) == {x} for i in range(3)) \
            or any(set(a for a in arr_tr[i]) == {x} for i in range(3)) \
            or set((s[0],s[4],s[8])) == {x} \
            or set((s[2],s[4],s[6])) == {x}:
            return True
        return False
    
    # 초기 상태에서 BFS
    start = '.'*9
    Q = deque([(start,0)])
    V = set()
    V.add(start)
    while Q:
        s,atk = Q.popleft()
        for i in range(9):
            ns = list(s)
            if ns[i] == '.':
                if atk%2 == 0: ns[i] = 'O'
                elif atk%2 == 1: ns[i] = 'X'
                ns = ''.join(ns)
                if ns not in V and not over(ns,atk+1):
                    Q.append((ns,atk+1))
                    V.add(ns)
    board = ''.join(board)
    return 1 if board in V else 0
```

#### **코멘트**

* 시간 더 줄이려면 start에서 바둑돌의 수 까지만 제한해서 턴 수를 줄일 수 있다.