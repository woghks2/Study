# **\[파이썬\] 프로그래머스 n+1 카드게임 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/258707
<br>

---

### **풀이**

#### **방향성 생각**
* 각 원소가 겹치지 않음 -> 두 자연수로 n+1를 만드는 경우는 유일함 (a가 정해지면 나머지는 n+1-a)
* 두 장을 뽑아서 기존의 패와 매칭할 수 있으면 코인을 사용해서 패에 넣기
매칭할 수 없으면 temp에 넣어놓기.
* hand 내에서 2장 내기
* 안되는 경우 hand 와 temp를 매칭해서 2장 내기. temp 내에서 코인 하나를 사용하기
* 안되는 경우 temp 내에서 매칭해서 2장 내기. temp 내에서 코인 두개를 사용하기
그렇지 않은 경우 제출 불가하므로 스탑.

<br>

---

### **전체코드**
```python
def solution(coin, cards):
    answer = 1
    n = len(cards)
    hand = set(cards[:n//3])
    stack = cards[n//3:]
    temp = set()
    
    for i in range(len(stack)//2):
        for x in stack[2*i:2*i+2]: # 두개씩 뽑고
            if n+1-x in hand and coin: # 짝이 패에 있으면 구매
                coin -= 1
                hand.add(x)
            else: # 없으면 temp 리스트에
                temp.add(x)
 
        check1 = False      
        for x in hand: # 1. hand 내에서 매칭
            if n+1-x in hand:
                match = True
                hand.remove(x)
                hand.remove(n+1-x)
                answer += 1
                check1 = True
                break
        
        if not check1:
            check2 = False
            for x in hand: # 2. hand, temp 매칭
                if n+1-x in temp and coin:
                    coin -= 1
                    answer += 1
                    hand.remove(x)
                    temp.remove(n+1-x)
                    check2 = True
                    break
            if not check2:
                for x in temp: # 3. temp 내에서 매칭
                    if n+1-x in temp and coin >= 2:
                        coin -= 2
                        answer += 1
                        temp.remove(x)
                        temp.remove(n+1-x)
                        break
                else: # 4. 게임오버
                    return answer
        
    return answer
```

#### **코멘트**

* 현장에서 바로 풀기에는 조금 까다롭지 않았나...