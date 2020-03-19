---
layout: post
title: 'RL Recommender System 논문 리뷰 1'
date: 2020-03-19
author: Jekyll
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: jekyll
---

> 강화학습을 이용한 추천 시스템 논문을 탐구해보자

# Papers
강화학습과 Recommender System을 접목한 것에 최근 흥미가 생겼다. 이 글은 필자가 읽은 논문들을 간단히 요약한 것이다.
### 1.[Deep Learning based Recommender System: A Survey and New Perspectives](https://arxiv.org/abs/1707.07435)
2019년에 나온 비교적 최근 저널로, Recommender System의 기술 동향을 전반적으로 서술해준 글이다.
 앞에선 전통적인 Recommender System(RS) 의 'Collaborate filtering'(historical interaction으로부터 학습), 'Content based', 'hybrid' 방법론을 간단히 설명한다. 여기서 Attention Algorithm, Restricted Boltzman Machine(RBM), Graph CNN (GCN) 등 생소한 개념들을 사용하여 RS를 구축하기도 한다. Neural Network(NN)을 사용한 기술들 또한 등장하는데, CNN, RNN, GAN, RL, 이들을 CF와 결합하는 등 굉장히 많은 예시들을 압축하여 이해할 수 있다.     

이제 대략적으로 RS에 대한 감을 잡았으니, Reinforcement Learning(RL)을 이용한 논문을 탐구해 보자!    

### 2.[Deep Reinforcement Learning for Page-wise Recommendations ](https://arxiv.org/abs/1805.02343)
 2018년 RecSys에 실린 논문이다. Page-wise라는 개념을 처음으로 접했는데, 요약하자면 아래와 같다.
##### page-wise : item set에서 비슷한 item들을 늘여놓는 것 보다 complementary 한 것을 섞는다.
요점은 보완적인 item들을 한 데에 놓는 것이 더 좋을 수 있단 것이다. 위 논문에서는 page-wise 개념과 함께 actor-critic, GRU, 그리고 item이 될 action을 2D로 나타내기 위해 CNN과 deCNN을 사용한다.    
 GRU(RNN의 일종으로 LSTM보다는 간단한데 성능은 비슷한, 혹은 더 좋음) 가 real time preference의 핵심으로 쓰인다. 연속적인 state를 위하여 사용했다.
actor-critic과 논문에 언급된 DDPG는 어떤 알고리즘인가? 궁금증에 읽은 것이 아래의 논문이다.

### 3.[CONTINUOUS CONTROL WITH DEEP REINFORCEMENT LEARNING](https://arxiv.org/abs/1509.02971)
 Deep Deterministic Policy Gradient(DDPG)를 소개한 논문이다. actor-critic 을 쓰는 Deterministic Policy Gradient(DPG)에 Deep이 붙었다. 간단하게, DPG에 Neural Network 를 끼얹은 거라고 볼 수 있다.  그러면 DPG, actor-critic은 무엇인가.   
 RL을 action 과 state로만 생각하고 쉽게 정리해 보자. action은 선택지에서 특정한 행동이라고 생각하면 된다. state는 말 그대로 상태, 현재 행동을 선택하려 하는 상태이다.
 즉, 내 상태 state를 이용하여 return한 action으로 행동하여 reward를 쌓고 이 reward가 큰 방향으로 계속 나아가는 것인데, actor-critic은 여기서 한 번 꼬았다. 간단히 설명하자면, actor는 행동을 결정하기 위한 policy를 갱신하고 critic은 value function을 측정하고 갱신한다.

이 actor-critic에 더불어, 여기서 학습한 samples(MDP에서 말하는 tuple이라고 이해하면 될 듯.)이 독자적으로 있다는 문제를 해결하기 위해 replay buffer를 쓴다. sample들을 길이가 한정된 replay buffer에 넣고 여기서 minibatch를 돌리는 것이다.

아마 저자는 DQN(Deep Q Network)에 감명받아 Neural Network를 써야겠다고 생각하셨나 보다. actor와 critic 부분을 각각 NN으로 돌려버린다. 이렇게 탄생한 것이 DDPG이다.   

### 4.[Recommendations with Negative Feedback via Pairwise Deep Reinforcement Learning ](https://arxiv.org/abs/1802.06501)

2번에서 page-wise 에 관한 내용을 읽었으니 이부분을 조금 더 학습해보고자 4번째 논문을 골랐다. 우연히도 2번과 4번 논문 모두 JD.com이라는 중국 쇼핑몰 소속에서 나온 글이었다. dataset도 위 온라인 사이트에서 제공받았다.(부럽다!)   
논문에서 말하고자 하는 알고리즘은 아래와 같다.   
##### DQN + using negative state + GRU for sequential positive state data + rerank clicked/ordered items + Pairwise
여기서도 GRU가 쓰였다. 크게 주목할 점은 DQN(Deep Q Network)를 사용했단 것과, negative state라는 개념을 도입했단 거다. 여기서 state는 user의 browsing history이다. 풀어 쓰자면 user가 item을 클릭하거나 샀는지, 아니면 그냥 넘겼는지에 관한 기록인 것이다. 여기서 state+는 item이 선택되었을 때, state-는 item이 스킵되었을 때다. 보통 학습할 때 스킵한 item에 대해서는 학습을 안하는데, 위 논문에서는 이또한 학습에 필요한 데이터로 넣음으로써 reward 향상과 q-value 향상을 보여주었다.

<hr>
update at 2020-3-19
   사담: 강화학습 논문을 계속 읽으니깐 MDP 이론은 이제 달달외워질 지경이다(LOL)
