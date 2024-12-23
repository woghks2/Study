# **[파이썬] 코드트리 : 타워 부수기 (골드1)**
* [포탑 부수기 | 삼성 SW 역량테스트 기출문제 (codetree.ai)](https://www.codetree.ai/training-field/frequent-problems/problems/destroy-the-turret/submissions?page=1&pageSize=20)
<br>

---

### **풀이**

#### **0\. 방향성 생각**
* 공격/수비 : 힙을 통해서 구현. 최대 100개의 정보에서 $logN$으로 빠르게 뽑는다.
* 레이저 길찾기 : 모듈러를 활용해서 경계선 이동하기. BFS로 탐색
* 레이저 공격
	* 지점을 공격할 수 있을 경우, DFS로 길찾기.
	* 지나온 경로에 모두 DMG//2만큼 빼주고, 공격 지점에는 (DMG//2 + 나머지) 한 번 더 뺀다.
* 폭탄 공격
	* 공격 지점 9칸에 DMG//2만큼 빼주고, 공격 지점에는 (DMG//2 + 나머지) 한 번 더 뺀다.
* 건물 부수기 / 수리
	* 맵 한 번 훑어주면서 좌표 업데이트

#### **1\. import, 전역 변수 설정**
```python
from collections import deque
import heapq as hq

H,W,K = map(int,input().split())
arr = [list(map(int,input().split())) for _ in range(H)]

dire = [(1,0),(0,1),(-1,0),(0,-1)]
splash = [(j,i) for i in (1,0,-1) for j in (1,0,-1)] # 폭탄 : 중심좌표 포함 9칸
inside = lambda x,y : 0<=x<W and 0<=y<H

# 각 좌표 마지막 공격 시점 저장
last_atk = [[0]*W for _ in range(H)]
```
- 4방향 이동, 9방향 데미지 처리, 각 좌표의 마지막 공격 시점 저장
<br>

#### **2공격/수비 타워 찾기**
```python
def find_tower():

    for i in range(H):
        for j in range(W):
            if arr[i][j]:
                # 공격력,마지막 공격시점, 행,열, 좌표 -> 힙으로 구현
                hq.heappush(attackers,(arr[i][j],-last_atk[i][j],-(j+i),-j,(j,i)))
                hq.heappush(defenders,(-arr[i][j],last_atk[i][j],j+i,j,(j,i)))

    # 한 명만 남으면 탈출
    if len(attackers) == 1:
        return (None,None)
    
    # 가장 약한 (공격자) / 가장 강한 (수비자)
    *_,atk_loc = hq.heappop(attackers)
    *_,dfd_loc = hq.heappop(defenders)
    return atk_loc,dfd_loc
```
- 문제 조건에 맞춰서 최대힙/최소힙으로 구현하면 된다.
- 힙에 요소가 한 개만 있을 경우, 문제 조건에 따라 루프 종료.
- 힙에서 꺼내서 공격/수비 타워의 좌표를 찾는다.
<br>

#### **3. BFS**
```python
def bfs(now:tuple, end:tuple):
    
    global find,path
    
    x,y = now
    Q = deque([now])
    V = [[-1]*W for _ in range(H)]
    V[y][x] = 0    
    while Q:
        x,y = Q.popleft()
        
        if (x,y) == end:
            find = V[y][x]
        
        for dx,dy in dire:
            nx,ny = (x+dx)%W,(y+dy)%H
            if V[ny][nx] == -1 and arr[ny][nx]:
                V[ny][nx] = V[y][x] + 1
                Q.append((nx,ny))
    return V
```
- 간선이 같은 그래프에서 BFS로 이동하면, 도착 지점에는 최단 거리가 저장된다.
- 얼마나 걸리는지 전역변수 find에 저장한다. 나중에 DFS 길 찾기 할 때 사용.
- 벽 넘는 좌표들은 모듈러로 구현
<br>

#### **4\. DFS**
```python
def dfs(now:tuple, end:tuple, V:list):
    
    global find,path,visit
    x,y = now
    if path:
        return
    
    if now == end:
        path = V[:]
        return
    
    if visit[y][x] >= find:
        return
    
    for dx,dy in dire:
        nx,ny = (x+dx)%W,(y+dy)%H
        if arr[ny][nx] and (visit[ny][nx] == visit[y][x]+1):
            dfs((nx,ny),end,V+[(nx,ny)])
```
- 우 하 좌 상 순서대로 탐색.
- BFS에서 방문 배열을 토대로 정해진 루트에 맞춰서 간다.
- 우선순위대로 가서 도착하면 가장 먼저 도달하는 경로 V를 저장한다.
<br>

#### 5. 공격
```python
# 주어진 경로를 따라서 공격.
def laser(dmg:int, path:list):
    fight.update(path)
    dmg_2,q = divmod(dmg,2)
    for x,y in path:
        arr[y][x] -= dmg_2
    arr[y][x] -= (dmg_2+q)
    
# 중심 좌표를 기준으로 공격.
def bomb(dmg:int, dfd_loc:tuple):
    dmg_2,q = divmod(dmg,2)
    dfd_x,dfd_y = dfd_loc
    for dx,dy in splash: # 포탄포함 9칸
        nx,ny = (dfd_x+dx)%W, (dfd_y+dy)%H
        if (nx,ny) != atk_loc:
            arr[ny][nx] -= dmg_2
            fight.add((nx,ny))
    arr[dfd_y][dfd_x] -= (dmg_2+q)
```
* 모든 지점에 절반의 데미지(몫)
* 공격 좌표는 절반의 데미지(몫) + a(나머지)를 한 번 더 빼준다.
<br>

#### 6. 업데이트
```python
# 부서짐 / 회복   
def update():
    for i in range(H):
        for j in range(W):
            if arr[i][j] <= 0: 
                arr[i][j] = 0
            else:
                if (j,i) not in fight:
                    arr[i][j] += 1
```
* 어레이를 순회하면서 0보다 작은 타워들은 0으로
* 그렇지 않는 부분들은, 전투에 참여하지 않았으면 수리
<br>

#### 7. 시뮬레이션
```python
for k in range(K):

    # 0. 싸움한 포탑들, 힙 저장
    fight = set()
    attackers,defenders = [],[]

    # 1. 타워 찾기
    atk_loc,dfd_loc = find_tower()
    if (atk_loc,dfd_loc) == (None,None):
        break
    fight.add(atk_loc)

    # 2. 공격 유형 정하기
    path,find = None,False
    ax,ay = atk_loc
    arr[ay][ax] += H+W # 공격력 버프 얻고
    last_atk[ay][ax] = k+1 # 공격지점은 마지막 공격시점 업데이트
    visit = bfs(atk_loc,dfd_loc) # 최단단거리 배열
    dfs(atk_loc,dfd_loc,[]) # path에 경로 업데이트
    
    # 3. 공격하기
    if find: laser(arr[ay][ax],path)
    else: bomb(arr[ay][ax],dfd_loc)

    # 4. 부서짐/정비
    update()
    
answer = 0
for i in range(H):
    for j in range(W):
        answer = max(answer,arr[i][j])
print(answer)
```
* 시뮬레이션 진행 후, 맵을 훑여서 가장 큰 값 출력
<br>

---

### **전체코드**
```python
from collections import deque
import heapq as hq

H,W,K = map(int,input().split())
arr = [list(map(int,input().split())) for _ in range(H)]

dire = [(1,0),(0,1),(-1,0),(0,-1)]
splash = [(j,i) for i in (1,0,-1) for j in (1,0,-1)] # 폭탄 : 중심좌표 포함 9칸
inside = lambda x,y : 0<=x<W and 0<=y<H

# 각 좌표 마지막 공격
last_atk = [[0]*W for _ in range(H)]

def find_tower():

    for i in range(H):
        for j in range(W):
            if arr[i][j]:
                # 공격력,마지막 공격시점, 행,열, 좌표 -> 힙으로 구현
                hq.heappush(attackers,(arr[i][j],-last_atk[i][j],-(j+i),-j,(j,i)))
                hq.heappush(defenders,(-arr[i][j],last_atk[i][j],j+i,j,(j,i)))

    # 한 명만 남으면 탈출
    if len(attackers) == 1:
        return (None,None)
    
    # 가장 약한 (공격자) / 가장 강한 (수비자)
    *_,atk_loc = hq.heappop(attackers)
    *_,dfd_loc = hq.heappop(defenders)
    return atk_loc,dfd_loc

# 레이저 경로를 찾을 수 있는지
def bfs(now:tuple, end:tuple):
    
    global find,path
    
    x,y = now
    Q = deque([now])
    V = [[-1]*W for _ in range(H)]
    V[y][x] = 0    
    while Q:
        x,y = Q.popleft()
        
        if (x,y) == end:
            find = V[y][x]
        
        for dx,dy in dire:
            nx,ny = (x+dx)%W,(y+dy)%H
            if V[ny][nx] == -1 and arr[ny][nx]:
                V[ny][nx] = V[y][x] + 1
                Q.append((nx,ny))
    return V

# 레이저 경로가 있으면 최단 경로 구하기
def dfs(now:tuple, end:tuple, V:list):
    
    global find,path,visit
    x,y = now
    if path:
        return
    
    if now == end:
        path = V[:]
        return
    
    if visit[y][x] >= find:
        return
    
    for dx,dy in dire:
        nx,ny = (x+dx)%W,(y+dy)%H
        if arr[ny][nx] and (visit[ny][nx] == visit[y][x]+1):
            dfs((nx,ny),end,V+[(nx,ny)])
            
# 주어진 경로를 따라서 공격.
def laser(dmg:int, path:list):
    fight.update(path)
    dmg_2,q = divmod(dmg,2)
    for x,y in path:
        arr[y][x] -= dmg_2
    arr[y][x] -= (dmg_2+q)
    
# 중심 좌표를 기준으로 공격.
def bomb(dmg:int, dfd_loc:tuple):
    dmg_2,q = divmod(dmg,2)
    dfd_x,dfd_y = dfd_loc
    for dx,dy in splash: # 포탄포함 9칸
        nx,ny = (dfd_x+dx)%W, (dfd_y+dy)%H
        if (nx,ny) != atk_loc:
            arr[ny][nx] -= dmg_2
            fight.add((nx,ny))
    arr[dfd_y][dfd_x] -= (dmg_2+q)

# 부서짐 / 회복   
def update():
    for i in range(H):
        for j in range(W):
            if arr[i][j] <= 0: 
                arr[i][j] = 0
            else:
                if (j,i) not in fight:
                    arr[i][j] += 1

for k in range(K):

    # 0. 싸움한 포탑들, 힙 저장
    fight = set()
    attackers,defenders = [],[]

    # 1. 타워 찾기
    atk_loc,dfd_loc = find_tower()
    if (atk_loc,dfd_loc) == (None,None):
        break
    fight.add(atk_loc)

    # 2. 공격 유형 정하기
    path,find = None,False
    ax,ay = atk_loc
    arr[ay][ax] += H+W
    last_atk[ay][ax] = k+1
    visit = bfs(atk_loc,dfd_loc)
    dfs(atk_loc,dfd_loc,[])
    
    # 3. 공격하기
    if find: laser(arr[ay][ax],path)
    else: bomb(arr[ay][ax],dfd_loc)

    # 4. 부서짐/정비
    update()
    
answer = 0
for i in range(H):
    for j in range(W):
        answer = max(answer,arr[i][j])
print(answer)
```
<br>

#### **코멘트**

* 첫 DFS 풀이는 47분 걸렸는데, 디버깅 하면 자꾸 다른 테케에서 빵꾸나서 다시 짰음
* BFS로 바꿨다가 또 다른 테케에서 빵꾸나서 둘이 합쳐서 풀이
* DFS에서 방문처리를 안해서 시간이 오래 걸린 듯