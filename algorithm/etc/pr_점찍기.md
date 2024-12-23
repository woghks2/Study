# **\[파이썬\] 프로그래머스 : 점 찍기 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/140107
<br>


---

### **풀이**

#### **방향성 생각**
* 원의 방정식 +   그냥 반복문

<br>

---

### **전체코드**
```python
def solution(k,d):
    
    answer = 0
    for x in range(0,d+1,k):
        ylim = (d**2-x**2)**0.5
        answer += ylim//k + 1           
    return answer
```

#### **코멘트**

* 레벨0 수준인뎅