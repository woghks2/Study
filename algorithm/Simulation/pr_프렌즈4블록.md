# [파이썬] 프로그래머스 : 프렌즈 4블록 (레벨2)
* [프로그래머스 : 프렌즈 4블록 (레벨2)](https://school.programmers.co.kr/learn/courses/30/lessons/17679)
<br>


---

### **풀이**

#### **방향성 생각**
* 아래로 떨어지는거 구현하기 불편해서 transpose + 각 row에서 reversed 시켜서 풀이

<br>

---

### **python**
```python
def solution(m, n, board):
    
    # 2x2 박스 체크
    def check(x,y):
        temp = set(arr[yy][xx] for xx,yy in [(x,y),(x+1,y),(x,y+1),(x+1,y+1)])
        return len(temp) == 1 and '#' not in temp
    # 2x2 박스 제거
    def remove(x,y):
        for xx,yy in [(x,y),(x+1,y),(x,y+1),(x+1,y+1)]:
            arr[yy][xx] = '#'

    # 아래로 떨어지는거를 왼쪽으로 밀리는 방식으로 구현 / 좌표계 변환
    arr = [list(row[::-1]) for row in zip(*board)]
    H,W = n,m

    while True:
        
        remove_locs = [(x,y) for y in range(H-1) for x in range(W-1) if check(x,y)]
        
        # 제거할거 없으면 탈출
        if not remove_locs:
            break
        
        # 있으면 제거
        for x,y in remove_locs:
            remove(x,y)
        
        # 떨어지는거 구현
        for y in range(H):
            temp = [val for val in arr[y] if val != '#']
            temp.extend(['#']*(W-len(temp)))
            arr[y] = temp
    
    # #개수 구하기
    answer = 0
    for y in range(H):
        for x in range(W):
            if arr[y][x] == '#':
                answer += 1
    return answer
```

#### **코멘트**

* 문자 6개만 쓰는거 아니었음?

* A to Z까지 쓴다고 나와있어서 #으로 바꿨음...