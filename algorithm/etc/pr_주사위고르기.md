# **\[파이썬\] 프로그래머스 : 주사위 고르기 (레벨3)**

https://school.programmers.co.kr/learn/courses/30/lessons/258709

<br>

---

### **풀이**

#### **방향성 생각**

- 10C5 : 주사위를 반으로 나누는 모든 경우의 수

- 6^10 : 승률 계산하는 경우의 수 (6^5씩 양 플레이어 둘)

- 주사위 수는 1~100까지고, 중복된 수를 줄이기 위해 6^5 리스트 대신 딕셔너리로 관리한다.

<br>

---

### **전체코드**

```python
from collections import Counter

def solution(dice):
    
    n = len(dice)
    r = n//2

    def dfs(score, cnt):
        if cnt == r:
            scores.append(score)
            return
        
        for s in dice[choose[cnt]]:
            dfs(score+s, cnt+1)

    table = {}
    x = (1<<r)-1
    while x<(1<<n):
        # choose dice
        choose = [i for i in range(n) if x&(1<<i)]
        
        # get score
        scores = []
        dfs(0, 0)
        
        # reduce cases
        table[tuple(choose)] = Counter(scores)
        
        # next comb
        nx = (x | (x-1)) + 1
        x = (nx | ((nx&-nx) // (x&-x) >> 1)) - 1

    max_win = 0
    answer = []
    
    # reduce cases
    keys = list(table.keys())[:len(table)//2]
    for key1 in keys:
        key2 = tuple(i for i in range(n) if i not in key1)
        
        # prob
        result = [0,0,0]
        for s1,cnt1 in table[key1].items():
            for s2,cnt2 in table[key2].items():
                cases = cnt1*cnt2
                if s1 > s2:
                    result[0] += cases
                elif s1 < s2:
                    result[2] += cases
                else:
                    result[1] += cases
        
        # update answer
        if result[0] > max_win:
            max_win = result[0]
            answer = key1
        if result[2] > max_win:
            max_win = result[2]
            answer = key2

    return list(map(lambda x:x+1, answer))
```

#### **코멘트**

- nC0부터 nCn까지 짜는 경우에는 그냥 comb를 for i in range로 i에 정보를 담으면 좋다.

- 그렇지 않은 경우에는, gosper hack으로 그냥 next comb를 구하는게 두 줄로 끝나서 좋다.

- 프로그래머스에서는 항상 모듈 단위로 나누고, 동작 검증하기.
  - 전역으로 관리하기 쉽지 않아서 요구사항대로 나누고 꼼꼼하게 짜야한다.
