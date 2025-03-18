# [파이썬] 프로그래머스 : 충돌 위험 찾기 (레벨2)
* [프로그래머스 : 충돌 위험 찾기 (레벨2)](https://school.programmers.co.kr/learn/courses/30/lessons/340211)

<br>


---

### **풀이**

#### **방향성 생각**

* 이동 우선순위가 정해져있어서 도착 위치에 돌때까지 while문으로 루프 돌리기.
  
* 각 위치별로 경로 모두 탐색

* 

<br>

---

### **python**
```python
from collections import defaultdict as dd

def solution(points, routes):
    
    # 각 point끼리 path 모두 구하기
    paths = {}
    for sidx,(sy,sx) in enumerate(points,1):
        path = [(sx,sy)]
        x,y = sx,sy
        for eidx,(ey,ex) in enumerate(points,1):
            path = [(sx,sy)]  
            x,y = sx,sy

            while x!=ex or y!=ey:
                if y!=ey:
                    y += 1 if y < ey else -1
                else:
                    x += 1 if x < ex else -1
                path.append((x,y))

            paths[(sidx,eidx)] = path
    
    # 3개 이상의 경로도 이동할 수 있으니 처리함
    # start -> end 경로를 계속 붙이기
    # 각 로봇마다 move를 구하고 moves에 넣기
    moves = []
    for route in routes:
        move = []
        for i in range(len(route)-1):
            sidx,eidx = route[i:i+2]
            move.extend(paths[(sidx,eidx)])
            if i<len(route)-2:
                move.pop()
        moves.append(move)
    
    # 길이 맞춰주기위해 padding
    max_leng = len(max(moves, key=lambda x:len(x)))
    for move in moves:
        if len(move) < max_leng:
            for _ in range(max_leng-len(move)):
                move.append((0,0))
    
    # 순회하면서 각 시간대별로 겹치는 좌표 구하기
    locs_times = list(zip(*moves))
    answer = 0
    for locs in locs_times:
        crash = dd(int)
        for loc in locs:
            crash[loc] += 1
        for key,val in crash.items():
            if key != (0,0) and val > 1:
                answer += 1
    
    return answer
```

#### **코멘트**

* 나름 빡셈;