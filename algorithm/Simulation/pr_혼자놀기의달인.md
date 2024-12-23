# **\[파이썬\] 프로그래머스 : 혼자 놀기의 달인 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/131130
<br>


---

### **풀이**

#### **방향성 생각**
* 그냥 간단한 구현
* 다음 연결 카드를 찾는 과정에서 재귀로 깔끔하게 짤 수도 있을 듯

<br>

---

### **전체코드**
```python
def solution(cards):
    
    # play를 통해서 카드 연결하고 그룹 찾기
    def play(x,V):
        group = []
        while not V[x]:
            V[x] = True
            group.append(x)
            x = cards[x]-1
        return group
    
    L = len(cards)
    answer = 0
    for i in range(L):
        V = [False]*L
        group1 = play(i,V)
        if len(group1) == L:
            continue
        
        for j in range(L):
            if not V[j]:
                group2 = play(j,V)
                answer = max(answer, len(group1)*len(group2))
    
    return answer
```

#### **코멘트**

* 