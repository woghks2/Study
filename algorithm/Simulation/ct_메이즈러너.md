# **\[파이썬\] 코드트리 메이즈러너 (골드3)**
* https://www.codetree.ai/training-field/frequent-problems/problems/maze-runner/description?page=1&pageSize=20
<br>

---
## 문제
M명의 참가자가 미로 탈출하기 게임에 참가하였습니다.

미로의 구성은 다음과 같습니다.

미로는 N×N 크기의 격자입니다. 각 위치는 (r,c)의 형태로 표현되며, 아래로 갈수록 r이 증가, 오른쪽으로 갈수록 c가 증가합니다. 좌상단은 (1,1)입니다.
미로의 각 칸은 다음 3가지 중 하나의 상태를 갖습니다.
빈 칸
참가자가 이동 가능한 칸입니다.
벽
참가자가 이동할 수 없는 칸입니다.
1이상 9이하의 내구도를 갖고 있습니다.
회전할 때, 내구도가 1씩 깎입니다.
내구도가 0이 되면, 빈 칸으로 변경됩니다.
출구
참가자가 해당 칸에 도달하면, 즉시 탈출합니다.
1초마다 모든 참가자는 한 칸씩 움직입니다. 움직이는 조건은 다음과 같습니다.

두 위치 (x1,y1), (x2,y2)의 최단거리는 ∣x1−x2∣+∣y1−y2∣로 정의됩니다.
모든 참가자는 동시에 움직입니다.
상하좌우로 움직일 수 있으며, 벽이 없는 곳으로 이동할 수 있습니다.
움직인 칸은 현재 머물러 있던 칸보다 출구까지의 최단 거리가 가까워야 합니다.
움직일 수 있는 칸이 2개 이상이라면, 상하로 움직이는 것을 우선시합니다.
참가가가 움직일 수 없는 상황이라면, 움직이지 않습니다.
한 칸에 2명 이상의 참가자가 있을 수 있습니다.
모든 참가자가 이동을 끝냈으면, 다음 조건에 의해 미로가 회전합니다.

한 명 이상의 참가자와 출구를 포함한 가장 작은 정사각형을 잡습니다.
가장 작은 크기를 갖는 정사각형이 2개 이상이라면, 좌상단 r 좌표가 작은 것이 우선되고, 그래도 같으면 c 좌표가 작은 것이 우선됩니다.
선택된 정사각형은 시계방향으로 90도 회전하며, 회전된 벽은 내구도가 1씩 깎입니다.
K초 동안 위의 과정을 계속 반복됩니다. 만약 K초 전에 모든 참가자가 탈출에 성공한다면, 게임이 끝납니다. 게임이 끝났을 때, 모든 참가자들의 이동 거리 합과 출구 좌표를 출력하는 프로그램을 작성해보세요.

### 입력
입력 형식
첫 번째 줄에 N, M, K가 공백을 사이에 두고 주어집니다.

다음 N개의 줄에 걸쳐서 N×N 크기의 미로에 대한 정보가 주어집니다.

0이라면, 빈 칸을 의미합니다.
1이상 9이하의 값을 갖는다면, 벽을 의미하며, 해당 값은 내구도를 뜻합니다.
다음 M개의 줄에 걸쳐서 참가자의 좌표가 주어집니다. 모든 참가자는 초기에 빈 칸에만 존재합니다.

다음 줄에 출구의 좌표가 주어집니다. 출구는 빈 칸에만 주어집니다.

N: 미로의 크기 (4≤N≤10)
M: 참가자 수 (1≤M≤10)
K: 게임 시간 (1≤K≤100)
### 출력
출력 형식
게임 시작 후 K초가 지났거나, 모든 참가자가 미로를 탈출했을 때, 모든 참가자들의 이동 거리 합과 출구 좌표를 출력합니다.

<br>


---

### **풀이**

#### **0\. 방향성 생각**

* 사각형 찾기 : 브루트 포스 (맵이 별로 안크다)
* 미로 회전 : list(zip(*리스트)) + [::-1]로 90도 회전
* 모듈화시켜서 미로, 플레이어 정보를 계속 업데이트 해주기
* 플레이어 정보를 2D로 표현해서 풀면서 위치 쉽게 파악하기
* 플레이어를 2D에만 놓으면 비효율적이므로, 딕셔너리를 추가해서 플레이어만 다루기
  

#### **1\. import, 전역 변수 설정, 초기 세팅**
```python
from collections import defaultdict as dd

# 미로 정보 : 벽(1이상) / 출구(-1)
N,M,K = map(int,input().split())
maze = [[0]*(N+1)]
for _ in range(N):
    maze.append([0]+list(map(int,input().split())))

# 플레이어 위치 : 리스트 형태 / 딕셔너리 형태
player_list = [[0]*(N+1) for _ in range(N+1)]
player_dict = dd(int)
for _ in range(M):
    py,px = map(int,input().split())
    player_dict[(px,py)] += 1
    player_list[py][px] += 1

# 출구 정보
ey,ex = map(int,input().split())
maze[ey][ex] = -1
dire = [(0,1),(0,-1),(1,0),(-1,0)] # 상하우좌

# 거리 / 내부 체크
dist = lambda x1,y1,x2,y2 : abs(y2-y1)+abs(x2-x1)
inside = lambda x,y: 1<=x<=N and 1<=y<=N
```
- maze는 padding을 해준 상태로 진행한다.
<br>


#### **2\. 플레이어 이동**
```python
def move(player_dict):   
    global step,escape
    new_list = [[0]*(N+1) for _ in range(N+1)]
    new_dict = dd(int)
    for (px,py),a in tuple(player_dict.items()): # (px,py)에 a명
        if a >= 1:
            d = dist(px,py,ex,ey)
            for dx,dy in dire:
                npx,npy = px+dx,py+dy
                if inside(npx,npy) and maze[npy][npx] <= 0: # 내부/벽아닌경우
                    nd = dist(npx,npy,ex,ey)
                    if nd < d: # 짧아지면 이동
                        if (ex,ey) != (npx,npy):
                            new_list[npy][npx] += a
                            new_dict[(npx,npy)] += a
                        else:
                            escape += a
                        step += a
                        break
            else:
                new_list[py][px] += a
                new_dict[(px,py)] += a
    return new_list,new_dict
```
- 특정 좌표의 플레이어가 이동하는 경우, 미로 내부에 있고 다음 위치가 벽이 아닌 경우 이동 거리를 계산하고 짧아지는 경우에 이동한다.
- 출구가 아닌 경우, 새로운 player list와 player dict에 정보를 업데이트한다.
- 출구인 경우 escape로 탈출한 인원 수를 체크해준다.
- 이동하는 경우에는 인원수 a만큼 step에 더해준다.
<br>


#### **3\. 정사각형 찾기**
```python
def rect(player_dict):
    for l in range(1,N+1):
        for i in range(1,N+1-l):
            for j in range(1,N+1-l):
                if j<=ex<=j+l and i<=ey<=i+l:
                    for (px,py),a in player_dict.items():
                        if a >= 1 and j<=px<=j+l and i<=py<=i+l:
                            return (j,i,l+1)
```
- 브루트포스로 그냥 찾는다.
- 플레이어와 출구가 항상 코너에 위치하지 않으므로 브루트포스가 찾기 편하다.
<br>

#### **4\. 회전**
```python
def rotate(array):
    temp = []
    for i in range(size):
        temp.append(array[y+i][x:x+size])
    temp = list(zip(*temp)) # 전치
    for i in range(size):
        temp[i] = list(temp[i][::-1])
    return temp
```
- list(zip(*리스트))를 넣으면 리스트를 transpose
- 전치된 list를 다시 ::-1로 바꾸면 시계방향으로 회전시킬 수 있다.
- 시뮬레이션 문제에서 리스트의 크기가 크지 않은 경우에 편리하게 사용할 수 있다.
<br>

#### **5\. 벽 부수기**
```python
def destroy(array):
    for i in range(y,y+size):
        for j in range(x,x+size):
            if array[i][j] >= 1:
                array[i][j] -= 1
    return array
```
- maze 정보를 넣어서 1 이상인 경우 1씩 감소
<br>

#### **6\. 정보 업데이트**
```python
def update(maze,player_list):
    new_dict = dd(int)
    for i in range(1,N+1):
        for j in range(1,N+1):
            if maze[i][j] == -1:
                ex,ey = j,i
            if player_list[i][j] >= 1:
                new_dict[(j,i)] += player_list[i][j]
    return ex,ey,maze, new_dict    
```
- 회전으로 인해 변경된 출구정보, 새로운 플레이어 정보를 받는다.
<br>

#### **7\. 시뮬레이션**
```python
escape = 0
step = 0 # 이동 거리
exit_loc = (ey,ex) # 출구 위치
for k in range(K):
    
    player_list, player_dict = move(player_dict)
    if escape == M:
        break
        
    x,y,size = rect(player_dict)
    # 회전
    sub_maze = rotate(maze)
    sub_player_list = rotate(player_list)
    for i in range(size):
        maze[y+i][x:x+size] = sub_maze[i]      
        player_list[y+i][x:x+size] = sub_player_list[i]
    maze = destroy(maze)
    
    ex,ey,maze,player_dict = update(maze, player_list)

print(step)
print(ey,ex)
```
- 중간에 리스트를 넣고 회전시킬 때, 회전시킨 부분만 반환하므로 업데이트 해준다.
  
---

### **전체코드**
```python
from collections import defaultdict as dd

# 미로 정보 : 벽(1이상) / 출구(-1)
N,M,K = map(int,input().split())
maze = [[0]*(N+1)]
for _ in range(N):
    maze.append([0]+list(map(int,input().split())))

# 플레이어 위치 : 리스트 형태 / 딕셔너리 형태
player_list = [[0]*(N+1) for _ in range(N+1)]
player_dict = dd(int)
for _ in range(M):
    py,px = map(int,input().split())
    player_dict[(px,py)] += 1
    player_list[py][px] += 1

# 출구 정보
ey,ex = map(int,input().split())
maze[ey][ex] = -1
dire = [(0,1),(0,-1),(1,0),(-1,0)] # 상하우좌

# 거리 / 내부 체크
dist = lambda x1,y1,x2,y2 : abs(y2-y1)+abs(x2-x1)
inside = lambda x,y: 1<=x<=N and 1<=y<=N

def move(player_dict):   
    global step,escape
    new_list = [[0]*(N+1) for _ in range(N+1)]
    new_dict = dd(int)
    for (px,py),a in tuple(player_dict.items()): # (px,py)에 a명
        if a >= 1:
            d = dist(px,py,ex,ey)
            for dx,dy in dire:
                npx,npy = px+dx,py+dy
                if inside(npx,npy) and maze[npy][npx] <= 0: # 내부/벽아닌경우
                    nd = dist(npx,npy,ex,ey)
                    if nd < d: # 짧아지면 이동
                        if (ex,ey) != (npx,npy):
                            new_list[npy][npx] += a
                            new_dict[(npx,npy)] += a
                        else:
                            escape += a
                        step += a
                        break
            else:
                new_list[py][px] += a
                new_dict[(px,py)] += a
    return new_list,new_dict

def rect(player_dict):
    for l in range(1,N+1):
        for i in range(1,N+1-l):
            for j in range(1,N+1-l):
                if j<=ex<=j+l and i<=ey<=i+l:
                    for (px,py),a in player_dict.items():
                        if a >= 1 and j<=px<=j+l and i<=py<=i+l:
                            return (j,i,l+1)

def rotate(array):
    temp = []
    for i in range(size):
        temp.append(array[y+i][x:x+size])
    temp = list(zip(*temp)) # 전치
    for i in range(size):
        temp[i] = list(temp[i][::-1])
    return temp

def destroy(array):
    for i in range(y,y+size):
        for j in range(x,x+size):
            if array[i][j] >= 1:
                array[i][j] -= 1
    return array

def update(maze,player_list):
    new_dict = dd(int)
    for i in range(1,N+1):
        for j in range(1,N+1):
            if maze[i][j] == -1:
                ex,ey = j,i
            if player_list[i][j] >= 1:
                new_dict[(j,i)] += player_list[i][j]
    return ex,ey,maze, new_dict

escape = 0
step = 0 # 이동 거리
exit_loc = (ey,ex) # 출구 위치
for k in range(K):
    
    player_list, player_dict = move(player_dict)
    if escape == M:
        break
        
    x,y,size = rect(player_dict)
    # 회전
    sub_maze = rotate(maze)
    sub_player_list = rotate(player_list)
    for i in range(size):
        maze[y+i][x:x+size] = sub_maze[i]      
        player_list[y+i][x:x+size] = sub_player_list[i]
    maze = destroy(maze)
    
    ex,ey,maze,player_dict = update(maze, player_list)

print(step)
print(ey,ex)

```

#### **코멘트**

- 풀기 전에 모듈화, 자료구조 등 생각해서 풀기.
- 모듈화를 해야 디버깅을 할 때, 금방 찾을 수 있다.
- 삼성 구현에서 특히 list, dict을 잘 써야 시간을 줄일 수 있다.
- 플레이어가 동일한 위치에 있는 경우 여러 번 이동하는 경우를 줄일 수 있다.
- 이 문제에서는 크게 차이가 없긴 한데 **마법사 상어와 복제** 문제에서는 차이가 꽤 있었다.