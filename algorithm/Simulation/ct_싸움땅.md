# **\[파이썬\] 코드트리 싸움땅 (골드2)**
* https://www.codetree.ai/training-field/frequent-problems/problems/battle-ground/description?page=1&pageSize=20
<br>

---

### **풀이**

#### **0\. 방향성 생각**
* 플레이어 : 위치 / 능력치 / 방향
  * 특정 좌표로 이동한 경우, 어떤 플레이어가 있는지 바로 확인해야한다.
  * 플레이어가 많지 않아서 딕셔너리를 안써도 상관 없지만, 딕셔너리가 편해서 딕셔너리 사용
* 총 : 위치 / 공격력 
  * 같은 좌표에 여려 개의 총이 들어간다는 내용이 포함돼있다.
  * 어떤 값을 넣고, 최대값을 뽑아야 하므로 최대힙을 사용한다.
  * 마찬가지로 플레이어가 많지 않아서 딕셔너리를 안쓰고 정렬 후 맨 뒤에서 꺼내도 크게 상관 없을 듯 하다.
  
* 플레이어 이동
  * 환형, 사이클, 반대방향 이런거에서는 모듈러 사용해주기
  * 앞으로 한칸 이동 (벽이면 (인덱스+2)%4 해주기 상우하좌)
  * 0번 p 부터 순서대로 이동 -> 같은 칸에는 최대 2명 (싸움 일어나면 그 칸에는 한명만)
  * 플레이어가 있는 경우
    * 플레이어의 능력치 + 공격력 비교
        * 이긴 사람
          * 능+공 차이만큼 점수 얻기, 같으면 능력치 높은애 승
          * 강한 총으로 교환, 약한 총은 내려놓기
        * 진 사람 : 총 내려놓고 원래 자리로.
          * 원래 자리에 사람 있거나 맵 바깥이면 (인덱스+1)%4
          * 이동 했으면 강한 총 줍기.
    * 플레이어가 없는 경우
        * 강한 총으로 교환, 약한 총은 내려놓기 -> 힙?

<br>


#### **1\. import, 입력, 전역 변수 설정**
```python
import heapq as hq
from collections import defaultdict as dd
N,M,K = map(int,input().split())

# 특정 좌표의 총들을 최대 힙으로 뽑기
guns = dd(list)
for i in range(N):
    for j,gun in enumerate(map(int,input().split())):
        if gun: hq.heappush(guns[(j,i)],-gun)

# player[idx] = [x,y,방향,스탯,총의 공격력]
players = dd(list)
for idx in range(M):
    y,x,d,s = map(int,input().split())
    players[idx+1] = [x-1,y-1,d,s,0]

# locs[(x,y)] = idx
locs = dd(int)
for idx in range(M):
    x,y,d,s,g = players[idx+1]
    locs[(x,y)] = idx+1

dire = [(0,-1),(1,0),(0,1),(-1,0)]
```
- guns[(x,y)] = []에서 heappush를 해준다
- players[idx] = [x,y,방향,스탯,총의 공격력] 이런식으로 저장한다.
  - 초기 총의 공격력은 0이다.
- locs[(x,y)] = idx
  - 특정 위치에 플레이어가 있는지 없는지 빠르기 확인하기 위해 딕셔너리를 사용한다.
<br>


#### **2\. 함수 설정**
```python
# 플레이어가 격자 내에 있는지 확인한다
def inside(x,y):
    if 0<=x<N and 0<=y<N:
        return True
    return False
        
# (x,y)에서 총을 바꾼다.
def change(x,y,g):
    hq.heappush(guns[(x,y)],-g) # 현재 총을 땅에 버리고
    g = hq.heappop(guns[(x,y)]) # 땅에서 가장 좋은 총을 줍는다
    return -g

# 공격자가 이기는지 판단
def atk_win(s,g,es,eg):
    if (s+g > es+eg) or ((s+g == es+eg) and s>es):
        return True
    return False

# 패배자 이동
def loser_move(lidx):    

    lx,ly,ld,ls,lg = players[lidx] # 현재 패배자의 정보 (싸움난위치)
    hq.heappush(guns[(lx,ly)],-lg) # 총을 땅에 버리고
    for i in range(4):
        nld = (ld+i)%4
        dx,dy = dire[nld]
        nlx,nly = lx+dx,ly+dy

        if inside(nlx,nly) and locs[(nlx,nly)] == 0: # 격자 내, 빈 칸이면
            locs[(nlx,nly)] = lidx # 이동한 후,
            players[lidx] = [nlx,nly,nld,ls,change(nlx,nly,0)] # 총을 줍는다.
            return
```
- 주석 참고
<br>


#### **3\. 실행**
```python
score = [0]*(M+1)
for _ in range(K):
    # 플레이어마다 이동
    for idx in range(1,M+1):
        
        x,y,d,s,g = players[idx]
        dx,dy = dire[d]
        nx,ny,nd = x+dx,y+dy,d
        
        # 벽에 막히는 경우 반대 방향으로 이동
        if not inside(nx,ny):
            nx,ny,nd = x-dx,y-dy,(d+2)%4

        locs[(x,y)] = 0
        # 사람이 없는 경우 : (x,y) -> (nx,ny)
        if locs[(nx,ny)] == 0:
            locs[(nx,ny)] = idx
            players[idx] = [nx,ny,nd,s,change(nx,ny,g)] # 이동 후 총 교환
  
        # 사람 있는 경우 : 공격자 : (x,y) -> (nx,ny) / 진사람 : (nx,ny) -> (?,?)
        else:
            players[idx] = [nx,ny,nd,s,g] # 공격자 이동
            eidx = locs[(nx,ny)] # 수비자 번호
            ex,ey,ed,es,eg = players[eidx] # 수비자 정보

            # 능력치 계산
            if atk_win(s,g,es,eg):
                score[idx] += abs((s+g)-(es+eg))
                locs[(nx,ny)] = idx # 이긴 플레이어가 차지
                loser_move(eidx) # 진 사람 이동하고
                players[idx][-1] = change(nx,ny,players[idx][-1]) # 이긴 사람은 총 줍기
            else:
                score[eidx] += abs((s+g)-(es+eg))
                locs[(nx,ny)] = eidx
                loser_move(idx)      
                players[eidx][-1] = change(nx,ny,players[eidx][-1])

print(*score[1:])

```
- 주석 참고
<br>

### **전체코드**
```python
import heapq as hq
from collections import defaultdict as dd
N,M,K = map(int,input().split())

# 특정 좌표의 총들을 최대 힙으로 뽑기
guns = dd(list)
for i in range(N):
    for j,gun in enumerate(map(int,input().split())):
        if gun: hq.heappush(guns[(j,i)],-gun)

# player[idx] = [x,y,방향,스탯,총의 공격력]
players = dd(list)
for idx in range(M):
    y,x,d,s = map(int,input().split())
    players[idx+1] = [x-1,y-1,d,s,0]

# locs[(x,y)] = idx
locs = dd(int)
for idx in range(M):
    x,y,d,s,g = players[idx+1]
    locs[(x,y)] = idx+1
    
dire = [(0,-1),(1,0),(0,1),(-1,0)]
    
# 범위 체크, 상 우 하 좌 순서
def inside(x,y):
    if 0<=x<N and 0<=y<N:
        return True
    return False
        
# (x,y)에서 총을 바꾼다.
def change(x,y,g):
    hq.heappush(guns[(x,y)],-g)
    g = hq.heappop(guns[(x,y)])
    return -g

# 공격자가 이긴 경우
def atk_win(s,g,es,eg):
    if (s+g > es+eg) or ((s+g == es+eg) and s>es):
        return True
    return False

# 패배자 이동
def loser_move(lidx):    

    lx,ly,ld,ls,lg = players[lidx]
    hq.heappush(guns[(lx,ly)],-lg) # 땅에 버리고
    for i in range(4):
        nld = (ld+i)%4
        dx,dy = dire[nld]
        nlx,nly = lx+dx,ly+dy

        if inside(nlx,nly) and locs[(nlx,nly)] == 0:
            locs[(nlx,nly)] = lidx
            players[lidx] = [nlx,nly,nld,ls,change(nlx,nly,0)]
            return

score = [0]*(M+1)
for _ in range(K):
    # 플레이어마다 이동
    for idx in range(1,M+1):
        
        x,y,d,s,g = players[idx]
        dx,dy = dire[d]
        nx,ny,nd = x+dx,y+dy,d
        
        # 벽에 막히는 경우 반대 방향으로 이동
        if not inside(nx,ny):
            nx,ny,nd = x-dx,y-dy,(d+2)%4

        locs[(x,y)] = 0
        # 사람이 없는 경우 : (x,y) -> (nx,ny)
        if locs[(nx,ny)] == 0:
            locs[(nx,ny)] = idx
            players[idx] = [nx,ny,nd,s,change(nx,ny,g)] # 이동 후 총 교환
  
        # 사람 있는 경우 : 공격자 : (x,y) -> (nx,ny) / 진사람 : (nx,ny) -> (?,?)
        else:
            players[idx] = [nx,ny,nd,s,g] # 공격자 이동
            eidx = locs[(nx,ny)] # 수비자 번호
            ex,ey,ed,es,eg = players[eidx] # 수비자 정보

            # 능력치 계산
            if atk_win(s,g,es,eg):
                score[idx] += abs((s+g)-(es+eg))
                locs[(nx,ny)] = idx
                loser_move(eidx)
                players[idx][-1] = change(nx,ny,players[idx][-1])
            else:
                score[eidx] += abs((s+g)-(es+eg))
                locs[(nx,ny)] = eidx
                loser_move(idx)      
                players[eidx][-1] = change(nx,ny,players[eidx][-1])

print(*score[1:])
```

#### **코멘트**

* 확실히 막 짜는거보다는, 구상을 한 번 하고 어떤 자료구조를 쓰고 어떤 함수를 쓸 지 생각하는게 빠르다.
* 처음 코드는 70분 정도 걸렸는데, 테케 6을 통과 못해서 다시 풀이