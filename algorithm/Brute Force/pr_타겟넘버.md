# **\[파이썬\] 프로그래머스 타겟넘버 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/43165
* 
<br>

---

### **풀이**

#### **방향성 생각**
* 리스트 길이가 20 이하이다. 2**20 = 약 10만이므로 완탐으로 충분히 풀이 가능.
* 현재 숫자 x, 현재 몇 개의 숫자를 사용했는지를 큐에 담아서 BFS로 풀이.

<br>

### **전체코드**
```python
from collections import deque

def solution(numbers,target):
    
    answer = 0
    q = deque([(0,0)])
    while q:
        x,stage = q.popleft()
        
        # 숫자를 모두 사용했을 때, 타겟 넘버가 됐으면 answer += 1
        if stage == len(numbers) and x == target:
            answer += 1
        
        # 숫자를 모두 사용하지 않은 경우, +와 -에 대해서 탐색
        if stage < len(numbers):
            for i in (numbers[stage],-numbers[stage]):
                q.append((x+i,stage+1))
            
    return answer
```

#### **코멘트**

* 기본 BFS 문제