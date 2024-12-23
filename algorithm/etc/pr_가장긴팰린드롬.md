# **\[파이썬\] 프로그래머스 : 가장 긴 팰린들모 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/12904
<br>


---

### **풀이**

#### **방향성 생각**
* 가장 긴 팰린드롬을 찾는거라 가장 긴 팰린드롬부터 찾아준다.
* 포인터를 이용해서 양 끝 팰린드롬 비교한다.

<br>

---

### **전체코드**
```python
def solution(s):
    L = len(s)
    for i in range(L,0,-1):  # 팰린드롬 길이
        for j in range(L-i+1):  # 시작 위치
            l,r = j,j+i-1
            flag = True
            while l<r:
                if s[l] == s[r]: # 같으면 좁혀주고
                    l += 1
                    r -= 1
                else: # 다르면 실패
                    flag = False
                    break
            if flag:
                return i
    return 1
```

#### **코멘트**

* 쉬운문제