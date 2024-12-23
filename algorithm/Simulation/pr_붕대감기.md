# **\[파이썬\] 프로그래머스 붕대감기 (레벨1)**
* https://school.programmers.co.kr/learn/courses/30/lessons/250137
<br>

---

### **풀이**

#### **방향성 생각**
* iteration을 모두 돌려도 되지만 비효율적이다.
* 시간 차이에서 몫, 나머지를 사용해서 효율적으로 푸는게 정해인듯.
* 공격 - 회복 - 공격 - 회복 순서로 진행된다.
* 회복 시, 이전에 공격을 받은 시간이 필요하다.
* 예외 처리를 해줘야하므로 첫 번째 공격을 먼저 받고
* 공격 / (회복-공격) ... 순으로 진행한다.
* 마지막은 회복 안해도 되는게 살아남으면 바로 리턴해주면 된다.

<br>

---

### **전체코드**
```python
def solution(bandage, health, attacks):
    
    prepare,regen,bonus = bandage
    hp = health
    
    # 공격
    _,dmg = attacks[0]
    hp -= dmg
    if hp <= 0: return -1
        
    for i in range(1,len(attacks)):
 
        # 회복
        dt = attacks[i][0]-attacks[i-1][0]-1
        p,q = divmod(dt,prepare)
        hp += regen*dt + bonus*p   
        if hp > health: hp = health
 
        # 공격
        time,dmg = attacks[i]
        hp -= dmg
        if hp <= 0:
            return -1
 
    return hp
```

#### **코멘트**

* 정답률이 낮길래 풀었는데 초심자용 문제라서 정답률이 낮았다...