# **\[파이썬\] 프로그래머스 : 단어퍼즐 (레벨)**
* https://www.acmicpc.net/problem/번호
<br>


---

### **풀이**

#### **방향성 생각**
* 시간제한이 좀 빡빡한 문제.
* BFS로 시작 단어에서 끝 단어를 만들 수 있는지 확인하기.
* 큐에 string 자체를 넣으면 메모리나 시간적으로 손해
* target string의 특정 인덱스까지 매칭을 시켰으면 방문처리하고 큐에 추가한다.
* 특정 길이의 단어를 방문할 때, 무작위로 방문하는 경우에 탐색하는데 시간이 오래걸린다.
  * strs에 있는 단어들을 길이 별로 구분하기.
  * 중복 방문한 길이는 다시 탐색하지 않게 continue

<br>

---

### **전체코드**
```python
from collections import deque,defaultdict as dd

def solution(strs,target):
    
    # 타겟 길이 T, 큐 Q, 방문처리 V, 길이 분류 table
    T = len(target)
    Q = deque()
    V = set()
    table = dd(list)
    for idx,s in enumerate(strs):
        l = len(s)
        table[l].append(s)
        if s == target[:l]:
            Q.append((l,1))
            V.add(l)
    
    # BFS 탐색
    answer = 1e9
    while Q:
        l,cnt = Q.popleft()
        if l == T:
            answer = min(answer,cnt)
        for nl in table.keys():
            # 현재 검사할 부분 단어
            temp = target[l:l+nl]
            for ns in table[nl]:
                # T보다 길어지면 매칭x
                if l+nl > T:
                    continue
                # 방문하지 않은 길이 매칭이 가능하면 방문처리하고 큐에 추가.
                if l+nl not in V and temp == ns:
                    Q.append((l+nl,cnt+1))
                    V.add(l+nl)
    return answer if answer != 1e9 else -1
```

#### **코멘트**

* 효율성 검사할거면 미리 적어놓든가, 시간제한이라도 주든가.........