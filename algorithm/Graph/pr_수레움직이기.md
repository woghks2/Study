[[Graph]]
# **\[파이썬\] 프로그래머스 : 수레 움직이기 (레벨3)**
* [코딩테스트 연습 - [PCCP 기출문제] 4번 / 수레 움직이기 | 프로그래머스 스쿨 (programmers.co.kr)](https://school.programmers.co.kr/learn/courses/30/lessons/250134)


---

### **풀이**

#### **방향성 생각**
* 맵 크기가 굉장히 작다. 완탐?
* 효율적으로 탐색하기 위해서 bitmask? 벽, 빈칸, 수레 3가지 종류가 존재해서 힘들어보임
* 최대 4x4 size에서 이전에 왔던 위치는 지나갈 수 없고, 상대 수레가 있는 장소에는 이동이 불가능하다.
* 또한, 벽이라는 제약 조건이 있어서 가지치기를 해주면 전체 케이스가 많지 않을 것이라고 판단.
* DFS로 수레를 이동시키며 경로를 저장한다.
* 목적지에 도달했을 경우 이동거리와 이동 경로를 저장한다.
* 빨간 수레의 경로와 파란 수레의 경로를 비교한다.
* 중간에 같은 좌표를 가지거나, 서로 부딪히며 이동하는 경우를 제외한 가장 짧은 탈출시간 리턴한다.
<br>

### **전체코드**
```python
from collections import deque, defaultdict as dd

def solution(maze):
    dire = [(1,0),(0,1),(-1,0),(0,-1)]
    
    h,w = len(maze),len(maze[0])
    inside = lambda x,y: 0<=x<w and 0<=y<h 

    # 수레의 시작과 끝 위치 지정, 벽 위치 저장
    walls = set()
    for i in range(h):
        for j in range(w):
            if maze[i][j] == 1: rs = (j,i)
            elif maze[i][j] == 2: bs = (j,i)
            elif maze[i][j] == 3: re = (j,i)
            elif maze[i][j] == 4: be = (j,i)
            elif maze[i][j] == 5: walls.add((j,i))
            
    # 목적지에 도달하는 경우, 딕셔너리[이동거리] = [이동경로1, 이동경로2, ...]로 저장
    red_done,blue_done = dd(list),dd(list)
    def dfs(now:tuple, end:tuple, V:list, red:bool):
        x,y = now
        # 도착 지점에 도달한 경우 저장하기
        if now == end:
            if red: red_done[len(V)-1].append(V)
            else: blue_done[len(V)-1].append(V)
            return
        # 도착 지점이 아니면 재귀
        for dx,dy in dire:
            nx,ny = x+dx,y+dy
            if inside(nx,ny) and (nx,ny) not in V and (nx,ny) not in walls:
                dfs((nx,ny),end,V+[(nx,ny)],red)
    # 탐색 진행
    dfs(rs,re,[rs],True)
    dfs(bs,be,[bs],False)

    # 최대 이동거리 100으로 설정.
    answer = 100
    
    # t초동안 이동경로 비교
    def check(red,blue,t):
        nonlocal answer
        rloc,bloc = red[0],blue[0] # 시작 위치
        for nrloc,nbloc in zip(red[1:],blue[1:]): # 다음 위치
            # 위치가 겹치거나 / 서로 위치가 바뀌는 경우는 제외
            if (rloc == bloc) or (rloc == nbloc and bloc == nrloc):
                return
            rloc,bloc = nrloc,nbloc
        # 문제 없는경우 정답 업데이트
        answer = min(answer,t)

	# 이동거리, 경로 모음
    for r_dist,reds in red_done.items():
        for b_dist,blues in blue_done.items():
			# 각 경로 매칭시키면서 비교
            for red in reds:
                for blue in blues:
                    # 같은 경우 그냥 비교
                    if r_dist == b_dist:
                        check(red,blue,r_dist)
                    # 경로 길이 다른경우 길이 맞춰주기 (먼저 도착한쪽은 제자리 추가)
                    elif r_dist > b_dist:
                        nblue = blue + [blue[-1]]*(r_dist-b_dist)
                        check(red,nblue,r_dist)
                    # 경로 길이 다른경우 길이 맞춰주기 (먼저 도착한쪽은 제자리 추가)
                    else:
                        nred = red + [red[-1]]*(b_dist-r_dist)
                        check(nred,blue,b_dist)
                
    return answer if answer != 100 else 0
```

#### **코멘트**

* 자료구조 크기 보고 문제 풀이 시작하기.
* 변수 할당을 잘못해서 생각보다 조금 더 걸렸다.