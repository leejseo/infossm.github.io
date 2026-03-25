---
layout: post
title: "Power Projection of Set Power Series"
date: 2026-03-23
author: qwerasdfzxcl
tag: [algorithm, set-power-series]
---

## Introduction
이 글에서는 power projection of set power series를 $O(N^2 2^N+M)$에 계산하는 알고리즘에 대해 알아볼 것이다. 이를 이용해 정점이 $N$개인 그래프의 chromatic polynomial을 $O(N^2 2^N)$에 계산하는 알고리즘도 함께 다룬다.

이 글에서는 [Operations on Set Power Series](https://infossm.github.io/blog/2021/11/22/Operations_on_Set_Power_Series/)의 내용을 모두 이해했다고 가정하고 설명한다.

## Power Projection of Set Power Series
Set $G = \{ g_0, g_1, \cdots, g_{N-1} \}$과 commutative ring $R$에 대해 정의된 set power series $\mathcal S_G(R)$에 대해 생각하자. 주어진 $a,w \in \mathcal S_G(R)$와 $M \in \mathbb Z_{\ge 0}$에 대해, $t=0,\cdots, M-1$에 대해 다음 식을 계산하는 문제를 power projection of set power series라고 한다.

$$\sum_{S \subseteq G} w_S(a^t)_S$$

이때, $a^t$는 subset convolution으로 정의된다는 것에 유의하자.

한편, polynomial composition of set power series는 주어진 $a \in  \mathcal S_G(R)$와 차수가 $M$미만인 다항식 $f \in R[X]$에 대해 $f(a)$를 계산하는 문제다. 즉, 다음을 계산하는 문제다.

$$\sum_{i=0}^{M-1} f_ia^i$$

이 두 문제는 서로 transpose 관계에 있다. Polynomial composition of set power series 문제에서, $a$를 상수 취급하고 $f$를 $M$차원 벡터로 생각하자. 다음과 같은 $2^N \times M$ 행렬 $A$를 생각해보면, $Af$를 계산하는 문제와 동일하다는 것을 알 수 있다. (편의를 위해 비트마스크 표기를 사용하였다.)

$$A = \begin{pmatrix} (a^0)_0 & (a^1)_0 & \cdots & (a^{M-1})_0 \\ (a^0)_1 & (a^1)_1 &\cdots &(a^{M-1})_1 \\ \vdots & \vdots & \ddots & \vdots \\ (a^0)_{2^N-1} & (a^1)_{2^N-1} &\cdots & (a^{M-1})_{2^N-1}  \end{pmatrix}$$

이제, $2^N$차원 벡터 $w$에 대해 $A^\top w$를 생각해보면 $A^\top w$의 $t$번째 원소는 $\sum_{i=0}^{2^N-1} w_i (a^t)_i$임을 알 수 있고, 이는 정의에 의해 power projection of set power series이다.

## Tellegen's Principle
우리는 이미 polynomial composition of set power series를 $O(N^2 2^N+M)$에 푸는 방법을 알고 있다. [Tellegen's principle](https://mathexp.eu/bostan/publications/BoLeSc03.pdf)에 의하면, 어떤 문제를 해결하는 linear algorithm이 있다면, 동일한 시간복잡도에 transpose된 문제를 해결할 수 있다. 이 글에서는 Tellegen's principle에 대한 자세한 논의는 하지 않고, 실제로 이를 어떻게 구현할 수 있는지에 대해서만 다룰 것이다.

Polynomial composition of set power series를 계산하는 알고리즘은 곱하기 쉬운 형태의 행렬 $A_1, A_2, \cdots, A_k$에 대해 $Ax = A_1A_2 \cdots A_kx$를 계산하는 것으로 취급할 수 있다. 따라서, $A_i$를 모두 $A_i^\top$로 바꾸고 $A^\top y = A_k^\top A_{k-1}^\top \cdots A_1^\top y$을 계산하는 식으로 접근할 것이다.

### 변수 선언, 제거
```cpp
vector<mint> f(vector<mint> input){
	int x = 0;
	// ...
}
```
위와 같은 상황을 생각해보자. `input`은 $3$차원 벡터라고 가정하면, $0$으로 초기화된 `x`라는 변수를 새로 추가하는 것은 다음 행렬을 곱하는 것으로 볼 수 있다.

$$\begin{pmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \\ 0 & 0 & 0 \end{pmatrix}$$

이 행렬의 transpose를 곱하는 것은 반대로 기존에 있던 변수 하나를 제거하는 것과 동일하다. C++에서는 명시적으로 변수를 제거할 필요는 없기 때문에 코드로 적을 필요는 없다. 

편의를 위해, 사용할 모든 변수는 초기에 전부 정의하기로 한다. 초기에 변수를 선언하고 마지막에 변수를 제거하는 것을 transpose하면, 여전히 초기에 변수를 선언하고 마지막에 변수를 제거하는 형태라는 것을 알 수 있다.

### 변수 계산
```cpp
y += x * c;
```
위와 같은 상황을 생각해보자. `x`와 `y`는 변수고, `c`는 상수이다. 변수가 `x`와 `y`만 있는 상황이라고 하면, 이는 다음 행렬을 곱하는 것으로 볼 수 있다.

$$\begin{pmatrix} 1 & 0 \\ c & 1 \end{pmatrix}$$

즉, `y`에 해당하는 행과 `x`에 해당하는 열의 교점에 `c`가 추가로 적힌 형태이다. 이를 transpose하면 `x`에 해당하는 행과 `y`에 해당하는 열의 교점에 `c`가 적히므로, 다음 코드에 대응된다:
```cpp
x += y * c
```
따라서, 상수는 가만히 두고 변수 2개만 위치를 서로 바꿔주면 된다.

### 함수 호출
```cpp
auto res = A(X);
for (int i=0;i<(int)Y.size();i++) Y[i] += res[i];
```
위와 같은 상황을 생각해보자. `A`는 선형변환이고, `X`와 `Y`는 모두 변수이다. 이는 행렬로 표현하면 다음 블록 행렬을 곱하는 것과 동일하다:

$$\begin{pmatrix} I & 0 \\ A & I \end{pmatrix}$$

따라서, transpose된 코드는 다음과 같이 구현할 수 있다:
```cpp
auto res = A_transpose(Y);
for (int i=0;i<(int)X.size();i++) X[i] += res[i];
```

### 상수 계산
입력과 관련없는 상수를 계산하는 코드는 영향을 받지 않는다. 따라서, transpose된 코드를 짤 때도 동일하게 계산해주면 된다.

## Transposition of Subset Convolution의 구현
Subset convolution은 두 개의 set power series를 입력으로 받지만, polynomial composition of set power series를 계산할 때는 둘 중 하나가 상수이므로, 하나만 변수로 취급할 것이다. 즉, subset convolution을 $2^N \times 2^N$ 행렬로 볼 것이다.

앞서 정의한 기본적인 연산만 가지고 subset convolution을 구현하면 다음과 같다:
```cpp
vector<mint> subset_conv(const vector<mint> &a, const vector<mint> &b){
    // a: input
    // b: constant
    int n = b.size(); // constant
    int k = 31 - countl_zero((unsigned)n); // constant

    vector<vector<mint>> B(k+1, vector<mint>(n)); // constant
    for (int i=0;i<n;i++) B[popcount((unsigned)i)][i] += b[i]; // constant
    for (int p=0;p<=k;p++){
        for (int b=0;b<k;b++){
            for (int i=0;i<n;i++) if (!(i&(1<<b))){
                B[p][i|(1<<b)] += B[p][i]; // constant
            }
        }
    }

    vector<vector<mint>> A(k+1, vector<mint>(n)); // variable
    vector<vector<mint>> C(k+1, vector<mint>(n)); // variable

    vector<mint> c(n); // output

    for (int i=0;i<n;i++) A[popcount((unsigned)i)][i] += a[i];
    
    for (int p=0;p<=k;p++){
        for (int b=0;b<k;b++){
            for (int i=0;i<n;i++) if (!(i&(1<<b))){
                A[p][i|(1<<b)] += A[p][i];
            }
        }
    }

    for (int p=0;p<=k;p++){
        for (int q=0;p+q<=k;q++){
            for (int i=0;i<n;i++){
                C[p+q][i] += A[p][i] * B[q][i];
            }
        }
    }

    for (int p=k;p>=0;p--){
        for (int b=k-1;b>=0;b--){
            for (int i=n-1;i>=0;i--) if (!(i&(1<<b))){
                C[p][i|(1<<b)] -= C[p][i];
            }
        }
    }

    for (int i=0;i<n;i++) c[i] += C[popcount((unsigned)i)][i];
    return c;
}
```
각각의 연산을 transpose하고, 계산 순서를 거꾸로 하면 transpose된 알고리즘을 구현할 수 있다. 예를 들어,
```cpp
    for (int p=0;p<=k;p++){
        for (int b=0;b<k;b++){
            for (int i=0;i<n;i++) if (!(i&(1<<b))){
                A[p][i|(1<<b)] += A[p][i];
            }
        }
    }
```
를 transpose하면,
```cpp
    for (int p=k;p>=0;p--){
        for (int b=k-1;b>=0;b--){
            for (int i=n-1;i>=0;i--) if (!(i&(1<<b))){
                A[p][i] += A[p][i|(1<<b)];
            }
        }
    }
```
가 된다. 계산 순서를 거꾸로 해야 하므로 반복문의 순회 방향도 뒤집어줘야 한다는 것에 유의하자.

따라서, subset convolution을 transpose한 코드는 다음과 같다:
```cpp
vector<mint> subset_conv_transpose(const vector<mint> &c, const vector<mint> &b){
    // c: input
    // b: constant
    int n = b.size(); // constant
    int k = 31 - countl_zero((unsigned)n); // constant

    vector<vector<mint>> B(k+1, vector<mint>(n)); // constant
    for (int i=0;i<n;i++) B[popcount((unsigned)i)][i] += b[i]; // constant
    for (int p=0;p<=k;p++){
        for (int b=0;b<k;b++){
            for (int i=0;i<n;i++) if (!(i&(1<<b))){
                B[p][i|(1<<b)] += B[p][i]; // constant
            }
        }
    }

    vector<vector<mint>> A(k+1, vector<mint>(n)); // variable
    vector<vector<mint>> C(k+1, vector<mint>(n)); // variable

    vector<mint> a(n); // output

    for (int i=n-1;i>=0;i--)  C[popcount((unsigned)i)][i] += c[i];

    for (int p=0;p<=k;p++){
        for (int b=0;b<k;b++){
            for (int i=0;i<n;i++) if (!(i&(1<<b))){
                C[p][i] -= C[p][i|(1<<b)];
            }
        }
    }

    for (int p=k;p>=0;p--){
        for (int q=k-p;q>=0;q--){
            for (int i=n-1;i>=0;i--){
                A[p][i] += C[p+q][i] * B[q][i];
            }
        }
    }

    for (int p=k;p>=0;p--){
        for (int b=k-1;b>=0;b--){
            for (int i=n-1;i>=0;i--) if (!(i&(1<<b))){
                A[p][i] += A[p][i|(1<<b)];
            }
        }
    }

    for (int i=n-1;i>=0;i--) a[i] += A[popcount((unsigned)i)][i];
    return a;
}
```

## Power Projection of Set Power Series의 구현
앞서 정의한 기본적인 연산만 가지고 polynomial composition of set power series를 구현하면 다음과 같다:
```cpp
vector<mint> poly_comp_of_set_power_series(vector<mint> a, const vector<mint> &b){
    // a: input
    // b: constant

    int n = b.size(); // constant
    int m = a.size(); // constant
    int k = 31 - countl_zero((unsigned)n); // constant

    vector<mint> pw(m); // constant
    if (m) pw[0] = 1; // constant
    for (int i=1;i<m;i++) pw[i] = pw[i-1] * b[0]; // constant

    vector<vector<mint>> dat(k+1, vector<mint>(n)); // variable
    vector<mint> c(n); // output

    for (int i=0;i<=k;i++){        
        for (int j=0;j<m;j++){
            dat[i][0] += a[j] * pw[j];
        } 
        
        for (int j=0;j+1<m;j++){
            a[j] -= a[j];
            a[j] += a[j+1] * (j+1);
        } 
        if (m) a.back() -= a.back();
    }
    
    for (int len=1,t=k-1;len<n;len*=2,t--){
        for (int i=0;i<=t;i++){
            auto res = subset_conv(
                vector<mint>(dat[i+1].begin(), dat[i+1].begin() + len), 
                vector<mint>(b.begin() + len, b.begin() + len * 2));
            for (int j=0;j<len;j++) dat[i][len+j] += res[j];
        }
    }

    for (int i=0;i<n;i++) c[i] += dat[0][i];
    return c;
}
```

이를 transpose하면 power projection of set power series 코드를 얻는다:
```cpp
vector<mint> power_proj_of_set_power_series(int m, vector<mint> c, const vector<mint> &b){
    // c: input
    // b: constant

    int n = b.size(); // constant
    // m: constant
    int k = 31 - countl_zero((unsigned)n); // constant

    vector<mint> pw(m); // constant
    if (m) pw[0] = 1; // constant
    for (int i=1;i<m;i++) pw[i] = pw[i-1] * b[0]; // constant

    vector<vector<mint>> dat(k+1, vector<mint>(n)); // variable
    vector<mint> a(m); // output

    for (int i=n-1;i>=0;i--) dat[0][i] += c[i];

    for (int len=n/2,t=0;len>=1;len/=2,t++){
        for (int i=t;i>=0;i--){
            auto res = subset_conv_transpose(
                vector<mint>(dat[i].begin() + len, dat[i].begin() + len * 2), 
                vector<mint>(b.begin() + len, b.begin() + len * 2));
            for (int j=0;j<len;j++) dat[i+1][j] += res[j];
        }
    }

    for (int i=k;i>=0;i--){
        if (m) a.back() -= a.back();

        for (int j=m-2;j>=0;j--){
            a[j+1] += a[j] * (j+1);
            a[j] -= a[j];
        }

        for (int j=m-1;j>=0;j--){
            a[j] += dat[i][0] * pw[j];
        }
    }
    
    return a;
}
```


[https://judge.yosupo.jp/problem/power_projection_of_set_power_series](https://judge.yosupo.jp/problem/power_projection_of_set_power_series) 에서 구현한 코드를 검증할 수 있다.

## Chromatic Polynomial
정점이 $N$개인 그래프 $G=(V, E)$에 대해 $G$의 [chromatic polynomial](https://en.wikipedia.org/wiki/Chromatic_polynomial) $P_G$의 차수는 $N$이하라는 것이 잘 알려져 있다. 따라서, $P_G(0), P_G(1), \cdots, P_G(N)$를 계산하면 interpolation을 통해 chromatic polynomial을 계산할 수 있다.

$P_G(k)$는 $G$의 모든 정점을 $k$개의 색을 이용해서 색칠하는 경우의 수이다. 같은 색의 정점끼리 모은 $k$개의 집합을 생각해보면 이는 모두 독립집합이다. 반대로, $V$를 독립집합 $k$개로 분할(순서 고려, 공집합 허용)했다면 그건 올바른 색칠이라는 것을 알 수 있다.

독립집합이면 $1$, 아니면 $0$인 set power series $I \in \mathcal S_V(\mathbb Z)$를 생각하자. $(I^k)_V$는 $V$를 독립집합 $k$개로 분할하는 경우의 수와 동일하다. 
즉, $P_G(k) = (I^k)_V$이다.

$V$면 $1$, 아니면 $0$인 set power series $w \in \mathcal S_V(\mathbb Z)$를 생각하면, $P_G(k) = \sum_{S \subseteq V} w_S (I^k)_S$로 적을 수 있다. 
따라서, $P_G(0), P_G(1), \cdots, P_G(N)$을 계산하는 문제는 power projection of set power series를 계산하는 알고리즘으로 $O(N^2 2^N)$에 풀 수 있다.

이를 구현하면 다음과 같다:
```cpp
vector<mint> chromatic_polynomial(int n, const vector<array<int, 2>> &E){
    // 0-based graph
    vector<vector<int>> adj(n, vector<int>(n));
    vector<mint> I(1 << n);

    for (auto &[u, v]:E) adj[u][v] = adj[v][u] = 1;
    for (int i=0;i<n;i++) if (adj[i][i]) return vector<mint>(n+1);

    I[0] = 1;
    for (int i=0;i<n;i++) I[1<<i] = 1;
    for (int i=0;i<n;i++) for (int j=i+1;j<n;j++) I[(1<<i) | (1<<j)] = !adj[i][j];

    for (int i=1;i<(1<<n);i++) if (popcount((unsigned)i) >= 3){
        I[i] = 1;
        for (int v=0;v<n;v++) if ((i & (1<<v)) && I[i ^ (1<<v)] == 0) I[i] = 0;
    }

    vector<mint> w(1 << n);
    w.back() = 1;

    return interpolate(power_proj_of_set_power_series(n+1, w, I));
}
```


## 연습문제
[https://www.acmicpc.net/problem/17643](https://www.acmicpc.net/problem/17643)

self-loop가 없고 서로 다른 두 점을 연결하는 간선이 정확히 $1$개인 유향그래프가 주어졌을 때, 간선의 방향을 적절히 뒤집어 DAG가 되도록 하는 모든 방법에 대해 비용의 합을 구하는 문제이다. 비용은 뒤집은 간선의 개수로 정의된다.

결과에 해당하는 DAG를 생각하자. 이 DAG의 간선 방향을 모두 뒤집어도 DAG이다. 이렇게 만든 두 DAG를 한 쌍으로 생각하자. 이렇게 묶인 두 DAG의 비용의 합은 정확히 $m$이다. 따라서, DAG를 만드는 경우의 수에 $m/2$를 곱하면 답이 된다.

한편, 간선의 방향을 적절히 정해서 DAG를 만드는 경우의 수는 [acyclic orientation](https://en.wikipedia.org/wiki/Acyclic_orientation)이라는 이름으로 알려져 있다. 또한, 그래프 $G = (V, E)$의 acyclic orientation의 개수는 $G$의 chromatic polynomial $P_G(x)$에 대해 $(-1)^{\mid V \mid}P_G(-1)$임이 알려져 있다. 따라서, 문제를 $O(N^2 2^N)$에 해결할 수 있다.

참고로 의도된 정해는 비트 DP로 acyclic orientation의 개수를 $O(3^N)$에 세는 것이다.
