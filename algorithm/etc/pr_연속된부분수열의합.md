# **\[파이썬\] 프로그래머스 : 연속된 부분 수열의 합 (레벨2)**
* https://school.programmers.co.kr/learn/courses/30/lessons/178870
<br>


---

### **풀이**

#### **방향성 생각**
* 구간 양 끝을 체크하면서 계속 연산을 해야한다.
* 누적합을 만들어서 바로 계산해버리기
* $N=10^7$에 $NlogN$이라 투포인터로 접근하는 건 어렵지 않게 생각할 수 있다.
<br>

---

### **전체코드**
```python
def solution(sequence, k):
    
    arr = [0]
    for val in sequence:
        arr.append(arr[-1]+val)
        
    leng = len(sequence)
    l,r = 0,0
    answer = []
    while l<leng:

        # 구간 값 같으면 추가
        if arr[r]-arr[l] == k:
            answer.append([r-l,l,r-1])
            l += 1
            continue
        # 구간 값이 크면 좌측을 줄여서 값 줄이기
        elif arr[r]-arr[l] > k:
            l += 1
        # 작으면 우측을 확장해서 새로운 값 탐색
        else:
            r += 1

        if r > leng:
            break
    answer.sort()
    return answer[0][1:]

```

#### **코멘트**

* 쉬운문제