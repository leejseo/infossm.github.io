---
layout: post
title: "Priority Queue Undo Trick"
date: 2026-04-22
author: numbering
tags: [algorithm, data-structure, priority-queue]
---

## 도입

어떤 자료구조 $D$에 대해 다음 세 연산을 지원해야 하는 상황을 생각해 보자.

- **Push(u, p)**: 우선순위 $p$를 가진 업데이트 $u$를 $D$에 적용한다.
- **Pop**: 현재 $D$에 적용된 업데이트 중 우선순위가 가장 높은 것을 취소한다(undo).
- **Query**: $D$에 쿼리를 수행한다.

이 연산들의 조합은 표준 우선순위 큐와 비슷해 보이지만, 핵심 차이는 $D$에 대한 undo를 지원해야 한다는 점이다.  
만약 Pop이 항상 가장 최근에 적용한 업데이트를 취소한다면 단순한 스택 + rollback으로 해결된다. Pop이 FIFO 순서라면 이 블로그에 소개된 [Queue-like Undoing Trick](https://infossm.github.io/blog/2021/03/25/queue-like-undoing/)으로 해결 가능하다.  
하지만 Pop이 임의의 우선순위에 따라 동작한다면? 이것이 **Priority Queue Undo Trick**이 다루는 문제다.

이 글에서는 [Monogon이 Codeforces에 소개한](https://codeforces.com/blog/entry/111117) 해당 기법을 설명하고, C++ 구현과 연습문제 풀이를 함께 다룬다.

## 사전 지식

- **롤백 가능한 자료구조**: 업데이트를 스택에 기록해두고, 스택에서 꺼내며 상태를 되돌리는 구조. 대표적으로 경로 압축 없는 DSU(Union-Find)가 있다.
- **Queue-like Undoing Trick**: FIFO 순서의 undo를 $O(T(n) \log n)$ amortized에 지원하는 기법. edenooo님의 [Queue Undo Trick](https://infossm.github.io/blog/2021/03/25/queue-like-undoing/) 글에 DSU 기반 구현과 연습 문제가 잘 정리되어 있다.

이 글에서 $T(n)$은 자료구조 $D$의 단일 업데이트/롤백 비용을 나타낸다.

## 핵심 아이디어

### 업데이트를 스택으로 관리하기

$D$에 적용된 업데이트들을 내부적으로 스택 형태로 관리한다. 스택의 맨 위(top)가 가장 최근에 적용된 업데이트다.

- 스택의 맨 위 원소를 롤백하는 것은 $O(T(n))$으로 자명하다.
- 우선순위가 높은 업데이트가 스택 중간에 묻혀 있다면, 그 위의 업데이트들을 모두 꺼낸 뒤 대상을 제거하고 나머지를 다시 적용해야 한다.

Pop 한 번에 $k$개의 원소를 처리한다면 비용은 $O(\log n + k \cdot T(n))$이다. 이 $k$가 매번 크다면 전체 복잡도가 나빠진다. 핵심은 **Pop을 수행하면서 이후의 Pop이 저렴해지도록 스택 순서를 재배치**하는 것이다.

### 재배치 규칙 (Stopping Condition)

Pop을 수행할 때 스택에서 $k$개의 원소를 꺼낸다. $k$의 선택 조건은 다음과 같다.

> 현재 스택에 있는 업데이트 중 우선순위 상위 $\lceil k/2 \rceil$개가 모두 상위 $k$개 위치 안에 포함될 수 있도록 하는 **최솟값 $k \ge 1$**을 선택한다.

$k$개를 꺼낸 뒤 재적용 순서는 다음과 같다.

1. $k$개 중 우선순위 상위 $\lceil k/2 \rceil$개를 **우선순위 오름차순**(낮은 것이 먼저 적용되어 스택 깊이 아래로 들어감)으로 먼저 적용한다.
2. 나머지 $k - \lceil k/2 \rceil$개는 **원래 스택 순서**를 유지하며 그 위에 적용한다.

이 배치의 불변식: 재배치 후 스택 상위 $k$개 위치에 전체 업데이트 중 우선순위 상위 $\lceil k/2 \rceil$개가 반드시 포함된다.

## 구현

`DS`는 다음 인터페이스를 갖는 롤백 가능한 자료구조라고 가정한다.

- `DS(int n)`: 크기 $n$으로 초기화
- `void update(UPD u)`: 업데이트 $u$를 적용하고 변경 이력을 내부 스택에 저장
- `void rollback()`: 가장 최근 업데이트를 취소

이 인터페이스만 갖추면 어떤 자료구조든 아래 `PQUndo` 템플릿에 꽂아서 쓸 수 있다.

```cpp
// DS: 롤백 가능한 자료구조, UPD: 업데이트 타입
template<typename DS, typename UPD>
struct PQUndo {
    DS D;
    vector<tuple<UPD, int, int>> stk; // {업데이트, 우선순위, 원래 인덱스}
    set<pair<int, int>> prio_set;     // {우선순위, 원래 인덱스}
    vector<int> pos;                  // 원래 인덱스 -> stk 내 현재 위치

    PQUndo(int n) : D(n) {}

    // O(log n + T(n))
    void push(UPD u, int p) {
        int orig = pos.size();
        D.update(u);
        prio_set.emplace(p, orig);
        stk.emplace_back(u, p, orig);
        pos.push_back((int)stk.size() - 1);
    }

    // amortized O(T(n) log n)
    void pop() {
        int k = 1;
        int max_prio;
        auto it = prio_set.end();
        for (int half = 0; ; half++) {
            --it;
            int depth = (int)stk.size() - pos[it->second];
            if (half == 0) max_prio = it->first;
            k = max(k, depth);
            if (k <= 2 * (half + 1) - 1) break; // ceil(k/2) <= half+1
        }

        vector<tuple<UPD, int, int>> popped;
        for (int i = 0; i < k; i++) {
            D.rollback();
            popped.push_back(stk.back());
            stk.pop_back();
        }
        prio_set.erase({max_prio, get<2>(*find_if(popped.begin(), popped.end(),
            [&](auto& t){ return get<1>(t) == max_prio; }))});

        reverse(popped.begin(), popped.end()); // 오래된 것이 앞으로

        vector<tuple<UPD, int, int>> bottom, top_half;
        for (auto& [u, p, orig] : popped) {
            if (p == max_prio) continue;
            (top_half.size() < (size_t)(k / 2) ? top_half : bottom)
                .emplace_back(u, p, orig);
        }
        sort(top_half.begin(), top_half.end(),
             [](auto& a, auto& b){ return get<1>(a) < get<1>(b); });

        for (auto& [u, p, orig] : bottom) {
            D.update(u); stk.emplace_back(u, p, orig);
            pos[orig] = (int)stk.size() - 1;
        }
        for (auto& [u, p, orig] : top_half) {
            D.update(u); stk.emplace_back(u, p, orig);
            pos[orig] = (int)stk.size() - 1;
        }
    }
};
```

`pop()`의 흐름은 세 단계다. ① `prio_set`을 역방향 순회하며 stopping condition을 만족하는 최소 $k$를 찾는다. ② 상위 $k$개를 롤백하고 최고 우선순위 원소를 제거한다. ③ 나머지 $k-1$개를 bottom half(원래 순서) → top half(우선순위 오름차순) 순으로 재적용한다.

## 시간 복잡도 분석

$n$번의 Push/Pop/Query가 발생할 때 전체 비용의 상한을 보인다.

**정의**: 각 연산 $i$의 비용 $C_i$를 Push이면 1, Pop이면 처리한 원소의 수 $k$, Query이면 0으로 정의한다.

**핵심 보조 정리**: 임의의 $i < j$에 대해 다음이 성립한다.

$$2(j - i) \ge \min(C_i, C_j)$$

**증명 스케치**: Pop $i$가 $k = C_i$개를 처리했다면, 재배치 후 상위 $\lceil k/2 \rceil$개의 고우선순위 업데이트가 스택 상위 $k$개 위치에 모인다. 이 집합을 $U$라 하자. 다음 Pop $j$가 $U$의 원소를 $C_j$개 이상 처리하려면:

- $U$의 원소 중 일부가 Pop $j$ 전에 다시 꺼내졌다면, 그만큼 Push가 더 발생했어야 한다.
- 어느 쪽이든 $j - i > \lceil k/2 \rceil - 1 \ge (1/2)\min(C_i, C_j) - 1$임을 유도할 수 있다.

**총 비용 도출**: $y_c$를 $C_i \ge c$인 연산의 수라 하면 보조 정리로부터 $y_c \le 1 + 2n/c$가 성립한다. 따라서

$$\sum_i C_i = \sum_{c=1}^{n} y_c \le \sum_{c=1}^{n}\left(1 + \frac{2n}{c}\right) = n + 2n H(n) = O(n \log n)$$

여기서 $H(n)$은 조화급수다. 따라서:

$$\text{전체 비용} = \sum_i O(\log n + C_i \cdot T(n)) = O(n \log n + T(n) \cdot n \log n) = O(n \cdot T(n) \cdot \log n)$$

연산당 **amortized $O(T(n) \log n)$** 이다. DSU with rollback을 쓸 경우 $T(n) = O(\log n)$이므로 연산당 $O(\log^2 n)$이다.

## 특수 케이스와 확장

### Queue-like Undoing으로의 환원

Pop이 항상 가장 오래된 업데이트를 제거하는 경우, 각 업데이트의 우선순위를 삽입 순서의 **역순**(나중에 삽입된 것이 우선순위 낮음)으로 배정하면 Priority Queue Undo가 정확히 Queue-like Undoing으로 동작한다.

### 오프라인 임의 삭제

삭제 시점이 미리 주어진 경우, 우선순위를 삭제 시점의 **역순**(늦게 삭제될수록 낮은 우선순위)으로 배정하면 오프라인 임의 삭제 문제가 Priority Queue Undo 문제로 환원된다. Dynamic Connectivity처럼 간선의 추가·삭제가 오프라인으로 주어지고 각 시점의 연결성을 묻는 류의 문제들이 대표적인 예인데, 이런 문제들을 Priority Queue Undo Trick으로도 해결할 수 있다.

### $d$차원 우선순위 일반화

#### 문제 설정

$d$차원 우선순위를 다음과 같이 정의한다.

- **Push(u, $\mathbf{p}$)**: 우선순위 벡터 $\mathbf{p} = (p_1, \ldots, p_d) \in \mathbb{R}^d$를 가진 업데이트 $u$를 적용한다.
- **Pop($i$)**: 현재 적용된 업데이트 중 $i$번째 차원 우선순위 $p_i$가 가장 큰 것을 취소한다.

$d = 1$이면 앞서 다룬 Priority Queue Undo이고, $d = 2$에 우선순위 $(t, -t)$ (삽입 시각 $t$)를 배정하면 양 끝에서 꺼낼 수 있는 **deque-undo**가 된다.

#### 재배치 전략

$d$차원에서 Pop($i$)를 수행할 때 스택에서 $k$개를 꺼낸 뒤 재적용하는데, 재배치 순서를 차원별로 순환(round-robin)하여 정한다.

구체적으로, 재적용할 $k - 1$개의 원소를 위(top)에서 아래(bottom) 순으로 배치할 때:

- 1번째 원소: 전체 중 $p_1$ 최대인 것
- 2번째 원소: 나머지 중 $p_2$ 최대인 것
- 3번째 원소: 나머지 중 $p_3$ 최대인 것
- $d+1$번째 원소: 나머지 중 $p_1$ 최대인 것 (순환)
- …

이 배치는 각 차원 $i$에 대해 다음 불변식을 유지한다.

> 차원 $i$에서 우선순위 상위 $\lceil \alpha k \rceil$개가 스택 상위 $k$개 위치 안에 포함된다.

여기서 $\alpha$는 차원 수 $d$에 의해 결정되는 상수다.

#### Stopping Condition ($d$차원)

Pop($i$)에서 꺼낼 원소 수 $k$는 다음을 만족하는 최솟값으로 정한다.

> **모든** 차원 $j = 1, \ldots, d$에 대해, 차원 $j$ 기준 상위 $\lceil \alpha k \rceil$개가 현재 스택 상위 $k$개 위치 안에 있다.

#### 복잡도 분석 ($d$차원)

**목표**: $\alpha$의 최적값을 구하고 전체 복잡도를 도출한다.

**포텐셜 정의**: 스택에 $n$개의 원소가 있을 때, 각 원소 $e$에 대해 차원 $j$에서 $e$보다 우선순위가 높은 원소 수를 $r_j(e)$라 하자. 포텐셜을

$$\Phi = \sum_{e} \max_{j=1}^{d} r_j(e)$$

로 정의한다.

**Push의 비용**: 원소를 스택 top에 추가하면 $\Phi$는 최대 $n$만큼 증가한다.

**Pop의 분할 비용 분석**: Pop($i$)에서 $k$개를 처리할 때 발생하는 비용과 포텐셜 변화를 따진다.

재배치 후 스택 상위 $k$개 위치를 살펴보자. round-robin 배치에 의해, top부터 $m$번째 위치의 원소는 차원 $(m-1 \bmod d) + 1$에서 남은 원소 중 최대인 것이다. 따라서 top $m$개 중 차원 $j$의 최댓값은 상위 $\lceil m/d \rceil$번째 안에 반드시 포함된다.

즉 재배치 후 어떤 원소 $e$가 스택에서 위에서 $m$번째 위치에 있다면, 모든 차원 $j$에 대해

$$r_j(e) \le k - \lceil m / d \rceil$$

가 성립하므로

$$\max_j r_j(e) \le k - \lceil m/d \rceil.$$

따라서 재배치 후 상위 $k$개 원소의 포텐셜 기여 합은

$$\sum_{m=1}^{k} \left(k - \lceil m/d \rceil\right) \le k^2 - \frac{k^2}{2d} = k^2\left(1 - \frac{1}{2d}\right).$$

반면 Pop 전 이 $k$개의 포텐셜 기여는 최소 $0$이다. 그러나 stopping condition에 의해, Pop 직전에 이 $k$개 중 적어도 하나의 원소 $e^*$에 대해 $\max_j r_j(e^*) \ge k - d\lceil\alpha k\rceil$가 성립한다. 이를 이용하면 pop 한 번의 **amortized 비용**이 $O(d \log n)$임을 보일 수 있다.

**최적 $\alpha$ 도출**: 비용 분석에서 $y_c \le 1 + \beta n / c$ ($\beta$는 상수)를 만족하려면 stopping condition의 $\alpha$가

$$\alpha_{\text{opt}} = 1 - \sqrt{1 - \frac{1}{d}}$$

일 때 $\beta$가 최소화된다. 각 $d$별 값은 다음과 같다.

| $d$ | $\alpha_{\text{opt}}$ | $\beta$ |
|-----|-----------------------|---------|
| 1   | $1/2$                 | $2$     |
| 2   | $1 - 1/\sqrt{2} \approx 0.293$ | $\approx 5.83$ |
| 3   | $\approx 0.184$       | $\approx 9.90$ |
| 4   | $\approx 0.134$       | $\approx 13.93$ |

**최종 복잡도**: $\sum_i C_i = O(\beta \cdot n \log n) = O(d \cdot n \log n)$이므로 전체 복잡도는

$$O(d \cdot n \cdot T(n) \cdot \log n).$$

#### Deque-Undo ($d = 2$) 적용 예시

우선순위 $(t, -t)$ ($t$: 삽입 시각)를 배정하면 Pop(1)은 가장 오래된 업데이트를, Pop(2)는 가장 최근 업데이트를 제거한다. 이는 양방향 큐(deque)에서 앞/뒤를 꺼내는 것과 동일하며, 복잡도는 $O(n \cdot T(n) \cdot \log n)$이다.

## 연습 문제

### [Codeforces 603E - Pastoral Oddities](https://codeforces.com/contest/603/problem/E)

**문제**: 간선을 하나씩 추가하는 온라인 그래프에서, 매 시점마다 모든 연결 성분의 크기가 짝수가 되도록 제거해야 할 간선의 최소 가중치를 출력한다.

**풀이 아이디어**:

모든 연결 성분의 크기가 짝수 ↔ 홀수 크기의 성분이 없다. 간선을 가중치 기준 우선순위로 Push하고, 홀수 크기 성분이 존재하면 최대 가중치 간선을 Pop한다.

```cpp
// 각 시점에서:
new_edge = read();
pq_undo.push(new_edge, new_edge.weight);
while (has_odd_component()) {
    pq_undo.pop(); // 최대 가중치 간선 제거
}
// 홀수 성분이 없으면 0, 있으면 -1
cout << (has_odd_component() ? -1 : current_min_removed_weight) << '\n';
```

전체 복잡도는 $O(n \log^2 n)$이다.

### [LGCPC 2024 예선 D. 보안 점검](https://cross-ratio.tistory.com/49)

**문제 개요**: $N$개의 정점과 $M$개의 간선으로 이루어진 그래프가 주어진다. 각 간선은 중요도 값을 가지며, 각 시점마다 간선 추가, 중요도 증가, 보안 임계값 증가 쿼리가 들어온다. 각 쿼리 후 현재 그래프에서 연결 성분의 총 중요도 합이 임계값을 넘지 않도록 제거해야 할 간선들의 최소 가중치를 출력한다.

**풀이 아이디어**:

현재 중요도 $\le k$인 간선들의 집합 $S$를 유지하면서, $S$로 구성된 그래프의 각 연결 성분의 간선 중요도 합이 해당 성분의 임계값 이하가 되어야 한다. 위반하는 성분이 있으면 $S$에서 가장 높은 중요도의 간선을 제거해야 한다.

이를 자료구조로 모델링하면:
- DSU with rollback로 연결 성분을 관리한다.
- 각 간선을 중요도 = 우선순위로 Push한다.
- 임계값 위반 시 Pop(최고 우선순위 간선 제거)을 반복한다.
- 마지막으로 제거된 간선의 중요도가 답이다.

Priority Queue Undo Trick을 DSU에 적용하면 전체 시간 복잡도는 $O((M + Q) \log^2 N)$이다.

**핵심 코드 (PQ-undo on DSU)**:

```cpp
// pq_undo: PQUndo<DSU, pair<int,int>> 인스턴스
// violation(): 현재 연결 성분 중 임계값 초과 성분 존재 여부

int ans = -1;
for (auto& [weight, u, v] : edges_to_add) {
    pq_undo.push({u, v}, weight);
    while (violation()) {
        ans = last_max_priority();
        pq_undo.pop();
    }
}
cout << (violation_ever ? ans : 0) << '\n';
```

