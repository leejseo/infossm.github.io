---
layout: post
title:  "알고리즘 문제 접근 과정 12"
date:   2023-04-23 08:00:00
author: VennTum
tags: [data-structure, algorithm]
---

# 알고리즘 문제 접근 과정 12

이번 포스트에서도 '알고리즘 문제 접근 방법' 시리즈에서 진행했듯이 특정 문제를 해결하기 위해 가장 낮은 단계의 접근에서부터 최종 해법까지 해결해나가는 과정을 작성합니다.

다만 기존과는 조금 달리, 이번에는 **JOI 기출문제들 중 높은 난이도의 문제**들을 위주로 이야기합니다.
JOI 기출문제들은 좋은 문제들이 많음에도, 한글로 번역이 되어있지 않고 한글 풀이가 존재하지 않는 문제들이 있어, 이들 위주로 풀이를 작성하려고 합니다.

이번 문제들의 난이도는 기존 시리즈들과는 달리, 기출문제들 중 높은 번호의 문제들을 다루다보니 조금 어렵게 느껴질 수 있습니다.
그럼에도 가장 낮은 단계부터 차근차근 접근하는 과정을 보여드려, 이해하시는 데 어려움이 없도록 설명하겠습니다.

# [Sugar Glider - JOI 2014 4번](https://www.acmicpc.net/problem/10048)

해당 문제에는 한글 번역이 존재하지 않습니다. 그러나 일본어의 경우 한글 번역이 꽤나 잘 동작하기 때문에, JOI 문제도 번역을 사용하여 문제 상황을 이해하기 편하실 겁니다.

실제 저의 경우에도, 번역이 되지 않은 JOI 문제들을 해결할 때에, 영문 번역이 존재하지 않는 경우 번역기를 사용하여 문제를 파악하였습니다.
전체적인 문제 상황을 파악하는 것에 큰 어려움이 없으실 것이므로 이와 같은 방법으로 JOI 문제들도 해결해보시면 좋습니다.
JOI 문제들의 퀄리티가 높기 때문에 한 번 즈음 다 해결해보시는 것도 좋은 방법입니다.

전체적인 문제 상황은 간단히 다음과 같습니다.

## 문제 상황

N개의 나무가 있으며, 각각 i번 나무는 높이 $h_{i}$ m를 가지고 있습니다. 이 때, M개의 나무 쌍에 대해서는 날아서 다른 나무로 이동하는 것이 가능합니다.
이 때, j번 나무 쌍 사이를 날아서 이동할 때에 걸리는 시간은 $t_{j}$초이며, 날아가는 동안에는 1초마다 높이가 1m씩 줄어들게 됩니다.
이렇게 날아가는 도중에 나무에 도착하기 전에 높이가 0이하가 되거나, t초를 날고 나서도 해당 나무의 높이보다 더 높게 존재하는 경우에는 이동할 수 없습니다.

JOI군은 처음에 1번 나무의 X미터 높이에 있으며, 날아서 N번 나무의 꼭대기로 이동하고 싶습니다.
JOI군이 각 나무에서 1초 동안 1m를 올라가거나 내려갈 수도 있을 때, N번 나무의 정상으로 이동하기 위해 얼마의 시간이 걸리는지 알려주세요.

## 관찰

주어진 문제를 해결할 때에 가장 어려운 부분이 무엇일까요? 즉, 어떠한 제약 조건이 없다면 이 문제를 쉽게 해결할 수 있을까요?

문제를 파악하는 데에 가장 쉬운 방법은 바로 문제 자체를 단순하시키는 작업입니다. 만약 어떠한 단순화된 문제에서 우리가 풀이를 찾아낼 수 있다면, 다음과 같은 방법으로 나누어 문제를 해결할 수 있기 때문입니다.

- 문제의 제약 조건을 바꾸어 어떻게 단순화할 수 있는가
- 단순화된 문제에서 어떻게 문제를 해결할 수 있는가
- 실제 문제에서 제약 조건을 바꾸지 않고도, 위의 단순화된 해법을 적용할 방법이 있는가

해당 부분을 판단하기는 많은 경우에 어려움이 있을 수 있습니다. 예를 들어, 아예 특정 조건을 무시하거나 처리하는 것이 불가능하여 이러한 제약 조건을 바꾼 문제의 풀이가, 원 문제의 풀이와는 상관이 없는 경우가 그러한 예시입니다.

그러나 꽤나 많은 경우, 단순화된 문제에서의 풀이가 실제 원래의 문제에서도 사용되는 경우들이 종종 있습니다.

그러한 예시 중 하나가 바로 시작점, 혹은 끝점 등에서 좌표나 위치가 중첩되는 경우, 구간이 겹치는 경우 등에 해당하는 문제들이 그러합니다.
이러한 문제들 중 일부는 일단 주어진 쿼리들이 유니크하다는 전제 하에 문제를 해결해두고, 중첩 쿼리들을 묶어서 생각하는 등의 문제로 해결할 수 있는 경우가 많습니다.
(물론, 모든 것들은 케이스마다 다르기 때문에, 실제로 어떠한 접근이 유효할 것인지 파악하는 능력을 많은 문제들을 접하는 것으로 키우는 것이 중요합니다)

혹은 실제로 이러한 접근이 유효하지 않다고 하더라도, 해당 제약 조건이 '왜' 중요한지에 대해서 파악할 수 있다는 장점을 가지고 있습니다.

그렇다면 다시 돌아와서, sugar glider 문제에서는 어떠한 제약 조건이 가장 까다로울까요?

아마 다들 바로 파악하실 수 있으셨을 것입니다.
바로, **날아가고 난 후, 0~나무 높이 사이에 위치해야한다**라는 조건이 위 문제를 어렵게 만들고 있습니다.

위의 제약조건이 있을 때에 우리는 여러가지 경우로 나누어서 날아가는 것을 생각해줘야합니다.

- 현재 나무의 높이가 날아가는 거리보다 낮을 경우, 날아가는 것이 불가능합니다
- 현재 위치의 높이에서 날아간 후, 목표 나무의 높이보다도 높을 경우에는, 그 차이만큼 현재 나무에서 내려간 후 날아가야 합니다
- 현재 위치의 높이에서 날아간 후, 0 미만의 높이가 된다면, 그 차이만큼 현재 나무에서 올라간 후 날아가야 합니다

이러한 경우들을 따져주게 되어야, 목표 나무에서 0부터 h 사이의 높이 중 어느 높이에 존재하게 된다는 것입니다.

즉, 이 과정에서 어떠한 나무에서 어떠한 높이에 있는지를 고려하게 되는 경우, 어떤 경로로 이동할 때에는 그것보다 높은 상황으로 올라가야할 수도, 어떠한 경우엔 그보다 낮은 상황으로 내려가야할 수도 있기 때문에, 어떠한 높이에 있어야 최적이 되는지가 매 번 달라지게 됩니다.

이를 모두 고려하게 된다면, 각 나무마다 '해당 나무의 높이 x로 올 때까지 걸리는 최소 시간'에 대한 정보들을 기록하고 있어야되며, 이는 전체 나무의 수 N에 전체 나무의 가능한 높이 h를 곱한 값만큼을 모두 고려하는 경우로 볼 수 있게 됩니다.

해당 풀이를 직접 적용하는 경우에는, 전체 N x H에 해당하는 배열을 만들어 값을 채우는 것과 동시에, M개의 나무쌍에 대해서 각 현재 나무에서 H에 해당하는 높이들에 대해 도착할 때의 최적의 경우를 고려해줘야하는 M x H에 해당하는 경우를 따져줘야하기 때문에, 전체 시간복잡도 O((N+M) x H)가 걸리게 됩니다.

해당 방법으로 구현하는 경우, 주어지는 값들이 너무 크기 때문에 올바른 답을 구하기에 충분하지 않음을 알 수 있습니다.

## 풀이

그렇다면, 아까의 단순화를 고려하여 만약에 날아가고 나서도 어떠한 나무 높이에 있어도 된다고 생각해봅시다.
그 예시로, 모든 나무들이 높이도 무한하고, 땅 아래로 들어가는 것까지 유효하다고 생각한다면, 우리는 위 문제를 어떻게 해결할 수 있을지 생각해볼 수 있습니다.

이 경우, 우리는 앞서 이야기했던 불가능한 경우들을 모두 배제하고 문제를 생각해줄 수 있습니다.
우리는 이 경우, JOI군이 언제 올라가거나 내려가야하는지를 하나도 고려하지 않고, 그저 최종 상태에서 나무의 목표 높이에 오르기 위해 얼마를 이동해야하는지 고려하는 단순한 다익스트라 문제처럼 볼 수 있게 됩니다.

만약, 목표 상태로 이동한 이후 해당 높이보다 높다면 그 높이 차만큼 내려오고, 그렇지 않다면 그 차만큼 올라가는 방식으로 항상 N번 나무에서만 해당 상태를 고려해주어도 상관없다는 것을 알 수 있습니다.

만약에 중간에 이동하는 경우가 최적이 되는 경우가 존재하더라도, 같은 상황을 그저 마지막 나무에서 고려하는 것으로 바꾸는 것만으로 동일하게 해결이 가능하다는 것입니다.

이러한 제약 조건을 지우는 것은 정말로 문제를 쾌적하게 해결하게 해줍니다.

그렇다면, 이제는 해당 제약 조건을 넣었을 때에도, 우리가 앞서 단순화한 풀이를 적용할 수 있는 방법이 있는지 고려해주도록 합시다.


앞서서 문제를 단순화할 수 있었던 가장 큰 요인은 다음과 같습니다.

- 올라가거나 내려가는 것을, 마지막 나무에서만 고려해도 된다

그렇다면 위와 같은 상황만 고려해도 될 때, 왜 편하게 문제를 풀 수 있을까요?

이는 바로 **중간의 나무 상태들 중 어떠한 것이 최적인지 고려할 필요가 없다**라는 것에 있습니다. 우리는 앞서 관찰에서 생각한 풀이에서, 어떠한 높이에 있는 경우가 가장 좋은지 모두 고려하면서 문제를 해결하게 되었고, 이를 따지다보니 결국 H라는 높이들 중 어떠한 것이 최적인지 기록하게 되었습니다.

그러나 실제로 위 경우를 생각해보면, 중간에서는 내려가거나 올라가지 않아도 된다는 것 따지게 되었을 때, 어떠한 경우만 고려해도 되나요?

바로, **중간 상태에서 높이가 가장 높은 경우가 최적**이라는 것을 어렵지 않게 알 수 있을 것입니다.
우리가 시작 높이 X에서 시작해서 도착까지는, 날아가는 과정에서 높이가 내려가기만 하게 됩니다. 그리고 날아가면서 소요되는 시간과, 우리가 직접 JOI군을 움직여서 높이를 낮추는 경우는 둘이 동일한 시간이 들어가게 됩니다.

그러므로, 실제 우리가 고려해줘야 하는 경우는 바로 **올라가는 경우를 최소화하는 것**에 있다는 것을 어렵지 않게 이해할 수 있습니다.
이는 실제로 결과를 좌우하게 되는 요소가 되며, 올라가는 경우는 도착 나무에서 높이가 낮을 수록 더 커지게 됩니다.

즉, 우리의 목표는 중간 상태의 높이를 최대화하는 경우가 항상 최적이라는 것을 쉽게 이해할 수 있게 됩니다.

그렇다면, 실제 제약조건 중 이를 고려할 수 있는 것은 1, 2, 3번 중 어떠한 것이 될까요? 하나씩 살펴보도록 하겠습니다.

먼저, 첫번째 제약조건을 보겠습니다. 이는 어떠한 나무 쌍이 날아갈 수 있는지를 확인하는 조건이었습니다. 이 경우는 입력을 받는 도중에도 시작 나무의 키와 이동하는 데 걸리는 시간을 고려하는 것을 전처리해줄 수 있는 단순한 조건인 것을 확인할 수 있습니다.

그렇다면 두 번째 제약조건은 어떨까요?

우리는 1번 조건에서 걸러지지 않은 경우에는 어떠한 방법으로든 시작 나무에서 도착 나무로 이동하는 것이 가능합니다. 날아가기 전에 특정 높이만큼 올라가거나 내려가는 것을 취하는 것이 가능하기 때문입니다. 즉, 2번 조건에서는 일단 날아가는 것은 가능한 상황입니다.
또한, 우리가 특정 높이만큼 내려가는 것은 필수인 경우, 앞선 파악에 의해서 그저 **해당 목표 나무의 꼭대기로 움직인다**와 동일한 결과를 내는 것을 알 수 있어, 큰 고려대상이 아님을 확인할 수 있습니다.

이제는 실제 결과에 영향을 미치는 마지막 조건을 다루도록 하겠습니다.

마지막 조건의 경우, 시작 나무에서 '올라가야 한다'라는 조건을 주기 때문에 결과에 큰 영향을 미칠 수 있습니다. 우리는 중간에 올라가는 것을 함께 고려하게 된다면, 이에 소요되는 시간을 따로 고려해줘야하기 때문입니다.

그렇다면 이는 어떻게 해결할 수 있을까요?

정답은 바로, **중간에 올라가는 경우는 고려하지 않아도 된다**입니다.

그 이유는 다음과 같습니다.

먼저, 3번의 조건에서도 1번의 경우와 마찬가지로, 우리가 시작 나무에서 올라가기만 한다면 이동하는 것은 가능함을 알 수 있습니다.
그리고 이와 같이 시작 나무에서 올라가서 날아가게 된다면, 결과적으로 도착하는 나무에서는 높이가 항상 0이 된다는 것 또한 알 수 있습니다.
즉, 우리는 도착한 이후에도, 다른 나무로 날아가기 위해서는 해당 높이만킄 또다시 올라가는 것을 반복해야한다는 것입니다.

이것이 의미하는 바는, 우리가 한 번 3번 조건에 들어가게 된다면, 그 이후에는 항상 다음 나무의 높이 0에 도착하기 위해, 해당 거리만큼을 올라가야한다는 것과 동일하게 됩니다.

그렇다면, 이를 다음과 같이 보면 어떻게 될까요?

- 중간에 올라가야하는 경우를 고려하지 않고, 마이너스 높이로 생각한 이후, 마지막 나무에서 한 번에 올라가기

우리는 어차피 누적되어 계속해서 올라가야하는 경우, 이를 마지막 나무에서 한 번에 올라가는 것으로 처리해도 결과가 바뀌지 않는다는 것을 확인할 수 있습니다.
이를 이해하기 어렵다면, 직접 한 두 개의 나무 예제를 만들어서 생각해주셔도 괜찮습니다.

이렇게 고려해도 되는 이유는, 우리가 날아가는 거리는 항상 음수가 아니며, 우리가 목표로하는 나무의 꼭대기 위치도 항상 양수이기 때문에, 어떠한 경우든 **중간에 0 이하로 내려가는 경우, 마지막 나무까지 가기 위해서는 항상 올라가기만 해야 한다**라는 결론을 얻을 수 있기 때문입니다.

즉, 어차피 항상 올라가기만 해야하는 상황에 놓였다면, 우리는 이를 한 번에 계산해도 상관없다는 것을 알 수 있게 됩니다.

이를 통해 우리는 다음과 같은 풀이를 생각해줄 수 있게 됩니다.

- 나무 쌍이 이동 가능한지 조건만 파악해준 이후
- 이동하는 경우, 날아간 높이와 해당 나무의 높이 중 작은 값에 위치하게 되고
- 마지막 나무에서 한 번에 올라가기

위 조건들을 생각하게 되면, 우리는 특정 나무에 대해서 모든 높이를 고려할 필요 없이, 특정 나무로 이동할 수 있는 최대 높이에 대한 정보만 가지고 있어도 된다는 것을 알 수 있습니다.

이는 결국 최댓값을 유지하기 위한 다익스트라의 형태로 고려하는 것이 가능하게 되므로, 총 O(NlgM)의 시간복잡도로 해결할 수 있게 됩니다.

# [산타클로스와 루돌프 - JOI 2010 예선 6번](https://www.acmicpc.net/problem/5570)

## 관찰

문제 상황이 꽤나 복잡합니다. 저 또한 해당 문제를 처음 풀 때에 문제 상황을 잘못 이해하고 헤멘 경험이 있기에, 중요한 내용들을 잘 숙지한 후 해결하는 것이 중요합니다.

제가 문제를 해결하면서 헷갈렸던 부분을 첨부하겠습니다.

- 공터와 선물을 주지 않은 집은 자유롭게 통과할 수 있으나, 선물을 준 집은 통과할 수 없습니다.
- 중간에 선물을 주지 않은 집을 건너 뛰고도 이동할 수 있습니다.

이제 본격적으로 문제를 접근해보도록 하겠습니다.

주어진 문제는 간단하게 생각한다면, 현재 위치에서 동서남북으로 이동하면서, 아직 선물을 주지 않은 집을 향해서 이동할 수 있으며, 해당 집으로 이동해 선물을 주고 나면 다시는 해당 집을 넘어서 갈 수 없습니다.
이러한 제약조건이 있는 상황에서, 교회에서 시작해 모든 집들에 선물을 주고나서 교회로 돌아오는 경로가 몇 가지 있는지 파악하는 것이 목표입니다.

즉, 특별한 제약이 있는 TSP 문제로 생각해볼 수 있습니다.

전체 맵의 크기는 10 x 10이며, 주어진 집들의 수도 교회 포함 24개 이하라는 조건이 있습니다.

우리는 가장 단순한 형태에서, TSP를 구하는 방법을 어렵지 않게 알 수 있습니다. 어떠한 집들이 방문되었는지에 대한 여부를 $2^24$에 해당하는 경우의 수로 고려해줄 수 있으며, 각각의 상태에서 내가 현재 위치하고 있는 집 i를 알고 있는 상태라면, 그 상태에서 다음 어느 집으로 이동할 수 있는지 24개의 집을 방문하는 경우를 고려할 수 있습니다.

이와 같은 방식으로 접근하게 되면, 총 $24^2 * 2^24$ 정도에 해당하는 시간복잡도가 나오게 됩니다. 그러나 우리가 이를 실제로 고려하게 되면, 배열만 만든다고 하는 과정에서도 총 $24 * 2^24 = 402653184 > 4$억개의 공간이 필요하기 때문에 메모리 초과가 난다는 것을 알 수 있습니다. 그러므로, 우리가 일반적으로 사용하던 Bit DP 형태로 문제를 해결하기는 쉽지 않습니다.

그렇다면 실제 위 문제가 우리가 알고있는 TSP와의 차별점은 어떠한 것들이 존재할까요?

이를 이용하여 실제로 해당 문제를 해결할 수 있게 됩니다.

## 풀이

우리는 위 문제의 제약조건들에서 찾을 수 있는 장점들을 확인할 수 있습니다.

- 이동이 4방향으로만 가능하므로, 실제 특정 상황에서 이동할 수 있는 정점들은 훨씬 적다
- 중간에 방문한 정점이 있으면, 그 정점 너머로는 이동이 불가능하여 이동 제약이 더욱 커진다
- 전체 맵의 크기가 10 by 10 밖에 되지 않는다

즉, 이러한 제약 조건들이 있기 때문에 기본 DP와 같은 형태로 접근할 경우에는 낭비되는 정보가 꽤나 많이 있을 수도 있습니다.

이러한 점을 고려하게 되면, 위 조건들을 고려하였을 때, 특정 분기에서 이동이 가능한지 여부가 조금 더 빠르게 판단될 수 있다는 것을 파악할 수 있으며, 그 과정에서 이동 가능한 분기의 경우의 수도 크게 많지 않다는 것을 알 수 있습니다.

그러므로, 실제 가능한 결과들을 셀 경우에 어쩌면 꽤나 빠르게 답을 구해낼 수 있을지도 몰라서, back tracking이 유효한 전략으로 고려될 수 있습니다.

산타는 항상 교회 또는 집으로만 이동해야 하기 때문에, 교화외 집들에 번호를 매겨 총 K개의 정점으로 만든 후, 각 정점끼리 이동할 수 있는지에 대한 여부를 전처리하여 K^2의 인접행렬을 만들어둡니다.
이후, 교회에서부터 이동 가능한 정점들을 순회하고, 그 과정에서 다른 곳으로 갈 수 없다는 것을 파악하게 되는 경우 퇴각하는 방식으로 back tracking을 고안할 수 있습니다.

그러나 단순 back tracking의 경우는 확정적으로 시간 초과가 날 수 있습니다. 그러므로 우리는 앞서 발견한 제약조건들을 통해 가능한지 여부를 잘 파악하는 조건을 넣어줘야합니다.

제가 고려한 조건들은 다음과 같습니다.

- 탐색 과정에서 이동하려를 정점읠 방문 여부를, 현재 존재하는 정점의 수(앞으로 V라고 하겠습니다)만큼 탐색해보지 않고, bit를 이용하여 상태를 표시하는 것으로 V만큼의 시간을 절약할 수 있습니다.
- 탐색 도중, 현재 위치와 방문하려는 정점 사이에 이미 방문한 정점이 있는지 빠르게 판단하기. 이를 위해, 인접행렬을 만드는 과정에서, 정점과 정점 사이에 존재하는 정점들을 bit 형태로 저장한 배열 또한 전처리하여 같이 만들어 놓습니다. 이렇게 되면 탐색하는 과정에서, 이미 방문한 정점들과 현 정점에서 방문하려는 정점 사이에 존재하는 정점들이 있는지 여부를, 이들 값에 대한 bitwise-AND 연산을 통해 확인하는 것이 가능하게 됩니다. 이를 통해 일일이 확인하지 않고도 O(1)에 판단해줄 수 있습니다.

위 과정들을 통해 탐색에 들어가는 식낭르 압축하게 되면, 1번을 사용하여 상태를 빠르게 표현해줄 수 있으며, 2번을 통해서 다음 상태로 이동 가능한지 여부 또한 O(1)로 해결해줄 수 있게 됩니다.

그러나 이 과정까지만 고려하여 제출하게 될 경우 시간초과를 피하기는 어렵습니다.
그렇기에 우리는 조금 더 줄일 수 있는 방법을 생각해봐야합니다.

탐색 과정을 줄이는 데에 가장 중요한 것은 바로 불필요한 탐색 분기를 없애는 것입니다. 
그 중 가장 중요한 것은, 현 상태 이후에서 마지막 교회로 이동이 가능한지를 빠르게 판단해주는 것이 중요합니다.

이를 위해, 현재 내가 있는 정점의 위치와 이미 방문한 정점들 이후로 도착하는 것이 가능한지 저장하는 배열을 하나 만들어 주겠습니다.
이를 저장하는 것은 $24 * 2^24$에 해당하는 dp를 만드는 것으로 빠르게 판단하는 것이 가능합니다.


이 과정까지 온다면, 해당 문제의 풀이가 어떻게 구성되었는지 파악하실 수 있을 것입니다.
즉, 우리는 우리가 처음에 생각했었던 bit DP를 그대로 가져오는 것과 동시에 한 번 방문했던 경로는 다시 확인하지 않고, 그 때의 경우의 수를 그대로 가져다 사용하는 memoization 방식으로 문제를 해결하게 되는 풀이가 됩니다.

그러나 이 방법은 앞선 '배열의 크기가 너무 크다'라는 단점을 그대로 가지고 있습니다.

이를 해결하기 위해서, 우리는 실제 '가능한 경우의 수'를 int형으로 저장하는 것이 아닌, '가능한지 여부'만을 1 bit로 표현하는 방식으로 경우의 수를 계산해주는 방식을 선택하였습니다.
기존의 방법이라면, 가능한 경우에 바로 멈추는 것이 가능한 방법이었지만, 이번에는 '불가능한 경우에 멈추는' 방법으로 back tracking을 고안한 것입니다.

이는 어떻게 보면 불합리한 방법으로 생각될 수 있습니다만, 실제 가능한 경우의 수가 많지 않기 때문에, unsigned int형을 32개의 state로 쪼개서 사용하는 방식으로 구현하면 50000KB의 빡빡한 메모리 사용량을 보이면서 문제를 해결해줄 수 있습니다.

## 더 효율적인 풀이

앞선 구조는 그대로 갖고, bit dp에서 '가능한 경우의 수'를 고려하는 방식으로 구현하면 10배 정도 더 빠르게 문제를 해결해줄 수 있습니다.

이는 실제 back tracking 과정에서 방문하게 되는, 즉, 실존하는 경로들의 수가 많지 않다는 것을 이용하는 방식으로, 방문 과정에서 발견한 상태들에 대해서만 unordered_map과 같은 hash table을 사용하여 값을 기록해주는 것입니다. 이처럼 실제 존재하지 않는 경우는 배열로 잡지 않는 방식을 통해서 메모리도 아낄 수 있으며, 이와 동시에 bit dp가 가지는 장점을 그대로 가지고 와서 사용할 수 있게 됩니다.

# 코드

## Sugar Glider

```cpp
#include <cstdio>
#include <algorithm>
#include <vector>
#include <queue>
#define ll long long
using namespace std;

struct data{ int to; ll dist; };
struct cmp{
    bool operator()(data d1, data d2){ return d1.dist > d2.dist; }
};
int n, m;
bool visit[100010];
ll x;
ll arr[100010], darr[100010];
vector <data> vt[100010];
priority_queue <data, vector<data>, cmp> pq;

void f_dijkstra(int a){
    int i, s;
    ll dist, nd, now;
    data temp;
    pq.push((data){a, 0});
    while(!pq.empty()){
        temp = pq.top(); pq.pop();
        a = temp.to; dist = temp.dist;
        if(visit[a]) continue;
        visit[a] = 1;
        for(i = 0; i < vt[a].size(); i++){
            s = vt[a][i].to; nd = vt[a][i].dist;
            if(!visit[s]){
                now = darr[a] + nd;
                if(x - darr[a] - nd > arr[s]) now = x - arr[s];
                if(darr[s] == 0 || darr[s] > now) darr[s] = now, pq.push((data){s, now});
            }
        }
    }
}
int main(){
    int i, a, s;
    ll d;
    scanf("%d %d %lld", &n, &m, &x);
    for(i = 1; i <= n; i++) scanf("%lld", &arr[i]);
    for(i = 1; i <= m; i++){
        scanf("%d %d %lld", &a, &s, &d);
        if(arr[a] >= d) vt[a].push_back((data){s, d});
        if(arr[s] >= d) vt[s].push_back((data){a, d});
    }
    f_dijkstra(1);
    if(darr[n] == 0) printf("-1");
    else printf("%lld", darr[n] + arr[n] - (x - darr[n]));
    return 0;
}
```

## 산타클로스와 루돌프

```cpp
#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;

int n, m, res, cnt = 2;
int arr[20][20], earr[30][30];
unsigned int table[24][524290];
vector <int> vt[30];

bool f(int a, int s){
    int i, d, flag = 1, iscan;
    if(s == (1 << (cnt - 1)) - 1){
        if(a == 0){ res++;
            return 0;
        }
        table[a][s / 32] |= (1 << s % 32);
        return 1;
    }
    if(a == 0 && s != 0){
        table[a][s / 32] |= (1 << s % 32);
        return 1;
    }for(i = 0; i < vt[a].size(); i++){
        d = vt[a][i];
        iscan = table[d][(s | (1 << d)) / 32] & (1 << ((s | (1 << d)) % 32));
        if((s >> d) % 2 == 0 && (s & earr[a][d]) == 0 && iscan == 0){
            if(f(d, s | (1 << d)) == 0) flag = 0;
        }
    }
    if(flag) table[a][s / 32] |= (1 << s % 32);
    return flag;
}

int main(){
    int i, j, k, a, s, now;
    scanf("%d %d", &m, &n);
    for(i = 0; i < n; i++){
        for(j = 0; j < m; j++){
            scanf("%d", &arr[i][j]);
            if(arr[i][j] == 1) arr[i][j] = cnt++;
            else if(arr[i][j] == 2) arr[i][j] = 1;
        }
    }
    for(i = 0; i < n; i++){
        for(j = 0; j < m; j++){
            if(arr[i][j]){
                a = arr[i][j];
                for(k = 0; k < m; k++){
                    s = arr[i][k];
                    if(s && s != a) vt[a - 1].push_back(s - 1);
                }
                for(k = 0; k < n; k++){
                    s = arr[k][j];
                    if(s && s != a) vt[a - 1].push_back(s - 1);
                }

            }
        }
    }
    for(i = 0; i < n; i++){
        for(j = 0; j < m; j++){
            if(arr[i][j]){
                a = arr[i][j]; now = 0;
                for(k = j + 1; k < m; k++){
                    s = arr[i][k];
                    if(s) earr[a - 1][s - 1] = now, earr[s - 1][a - 1] = now, now = now | (1 << (s - 1));
                }
                now = 0;
                for(k = i + 1; k < n; k++){
                    s = arr[k][j];
                    if(s) earr[a - 1][s - 1] = now, earr[s - 1][a - 1] = now, now = now | (1 << (s - 1));
                }
            }
        }
    }
    a = f(0, 0);
    printf("%d", res);
    return 0;
}
```