# **\[파이썬\] 프로그래머스 : 유사 칸토어 비트열 (레벨3)**
* https://school.programmers.co.kr/learn/courses/30/lessons/148652
<br>


---

### **풀이**

#### **방향성 생각**
* 범위가 $5^{20}$이라서 그냥은 못푼다.
* 규칙 찾아서 푸는 분할정복 / 재귀 형태가 보인다.

<br>

---

### **전체코드**
```python
def solution(n,l,r):
    
    l -= 1
    r -= 1

    def dfs(n,l,r):
        
        # 0번째 칸토어 비트열
        if n == 0:
            return 1
        
        # n-1번째 칸토어 비트열의 길이
        leng = 5**(n-1)
        
        count = 0
        for i in range(5):
            
            # 11011 에서 0 부분은은 계속 0이므로 세지 않음
            if i==2:
                continue
            
            # 구간 [s,e]
            s,e = i*leng, (i+1)*leng-1
            
            # 잘못된 구간  x
            if s>r or e<l:
                continue
            
            # n-1번째 칸토어 비트열 개수 카운팅
            count += dfs(n-1,max(0,l-s),min(leng-1,r-s))
        
        return count
    
    return dfs(n,l,r)
```

#### **코멘트**

* DFS + DP 하다보니까 엄청 빨라진듯