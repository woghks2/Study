# **\[파이썬\] 프로그래머스 : 석유 시추 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/250136
<br>

---
### **풀이**

#### **방향성 생각**
* 기본 BFS문제
* 각 덩어리 별로 BFS를 돌리고 덩어리에 포함된 x좌표에 그 덩어리 크기를 대응시킨다.
* oils[x] = [덩어리크기1, 덩어리크기2, ...]
* x에서 시추를 했을 때 덩어리들의 합이 가장 큰 것을 출력한다.

<br>

### **전체코드**
```python
from collections import deque, defaultdict as dd

def solution(land):
    
    h,w = len(land),len(land[0])
    dire = [(1,0),(0,1),(-1,0),(0,-1)]
    
    V = [[False]*w for _ in range(h)]
    oils = dd(list) # oils[x] = [덩어리1,덩어리2,...] x를 지나는 덩어리 크기
    
    # BFS
    def bfs(x,y):
        count = 1
        cols = set([x])
        V[y][x] = True
        Q = deque([(x,y)])
        while Q:
            x,y = Q.popleft()
            for dx,dy in dire:
                nx,ny = x+dx,y+dy
                if 0<=nx<w and 0<=ny<h and not V[ny][nx] and land[ny][nx]:
                    Q.append((nx,ny))
                    V[ny][nx] = True
                    cols.add(nx)
                    count += 1
        return cols,count
    
    # 맵 순회하면서 덩어리 업데이트하기
    for i in range(h):
        for j in range(w):
            if not V[i][j] and land[i][j]:
                cols,count = bfs(j,i)
                for x in cols:
                    oils[x].append(count)

    # 시추 할 지점의 좌표가 가장 큰 지점에서 시추                    
    return sum(max(oils.values(),key= lambda x:sum(x)))
```

#### **코멘트**

* .