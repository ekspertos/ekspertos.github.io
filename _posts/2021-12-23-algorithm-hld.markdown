---
layout: post
title:  "Heavy-Light Decomposition Algorithm"
subtitle: "HLD Algorithm"
categories: algorithm
tags: blog github pages jekyll Git Push Algorithm
comments: true
---

### 서론
>
> 그래프 문제를 트리 문제로 모델링하는 알고리즘 Heavy-Light Decomposition을 소개한다.
>

알고리즘 문제들을 풀다보면 많은 경우 문제들을 그래프로 모델링 할 수 있다. 여러 요소들 간의 관계가 주어지는 경우 각 요소를 정점으로 관계를 간선으로 하는 모델화가 자주 사용되고 DP 문제는 DAG(Directed Acyclic Graph)로 각 상태가 이어지는 것을 보게된다.

일반적인 형태의 그래프라면 효율적인 계산이 매우 어렵거나 다항시간 안에 해결하는 것이 불가능한 문제들도 보게된다. 그래프 문제를 트리 형태로 모델링 한다면 보다 적은 시간 복잡도로 문제를 해결할 수 있게된다. 예로 최단 경로 탐색 문제에 HLD를 사용하면 $ O(log^2N) $의 시간복잡도로 문제를 해결한다.

이번 글에서는 트리이기 때문에 사용할 수 있는 테크닉 중 하나인 Heavy-Light Decomposition에 대해 기본적인 설명과 이를 사용해 어떤 문제들을 풀 수 있는지 알아보겠다.

## Heavy-Light Decomposition
Heavy-Light Decomposition(HLD) 구조의 핵심은 트리를 일 차원 배열 여러개로 나눈다는 것이다.   



![HLD](https://ekspertos.github.io/assets/img/algorithm/HLD.gif)
