# **\[파이썬\] 프로그래머스 : 괄호 회전하기 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/76502
<br>


---

### **풀이**

#### **방향성 생각**
* 큐 스택형태
* 입력 문자열의 s의 크기 :  $N=1000$
* 모든 경우에 대해서 순회를 해서 체크를 해주면 된다.

<br>

---

### **전체코드**
```python
from collections import deque

def solution(string):
    
    # 매칭 되면 스택 터뜨리기, 아니면 스택에 추가하기
    def check(sub_string):
        stack = []
        for s in sub_string:
            if stack:
                e = stack[-1]
                if ((e,s) == ('[',']')) or ((e,s)==('{','}')) or (e,s)==(('(',')')):
                    stack.pop()
                else:
                    stack.append(s)
            else:
                stack.append(s)
        # 올바른 문자열은 스택이 다 터져야함
        if stack:
            return 0
        return 1
    
    answer = 0
    Q = deque(string)
    for _ in range(len(string)):
        Q.rotate(-1)
        sub_string = ''.join(Q)
        answer += check(sub_string)
    return answer
```

#### **코멘트**

* 쉬운문제