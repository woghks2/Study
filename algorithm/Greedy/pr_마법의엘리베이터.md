# **\[파이썬\] 프로그래머스 : 마법의 엘리베이터 (레벨2)**

- https://https://school.programmers.co.kr/learn/courses/30/lessons/148653

<br>

- BFS/다익 -> 범위 넓어서 시간 초과.

  - 간선도 노드마다 20개 가량된다.

- 테케 2번처럼 그리디하게 접근.

- 1의 자리부터 접근해서 0~4이면 빼주고 6~9면 올려준다.
  - 5의 경우에는 다음 자리수도 봐야한다.
  - 재귀를 사용해서 다음 자리를 계속해서 탐색한다.

```python
def solution(x):
    answer = 0

    def dfs(n):
        nonlocal answer

        if not n:
            return

        p,q = divmod(n,10)

        if 0<=q<5:
            answer += q

        elif 5<q<10:
            answer += (10-q)
            p += 1

        elif q == 5:
            answer += 5
            np,nq = divmod(n,100)
            if nq>=50:
                p += 1
        dfs(p)

    dfs(x)
    return answer
```

-
