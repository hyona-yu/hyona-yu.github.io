---
layout: post
title: '[Paper Review]Recurrent Attention Model 논문 리뷰'
date: 2020-09-30
author: Jekyll
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Human Activity recognition, rl, deep learning
---

> [SAI 동아리 발표] RAM(Recurrent Attention Model) 관련 논문들.

최근들어 HAR(Human Activity Recognition),특히 vision sensor가 아닌 IMU sensor data기반에 대해 알아보았다. 평소 관심사인 RL에 대한 애정이 식어간다고 느꼈다. 그러다보니, state를 각 sensor data 에 대해 t(time)로 쪼개서 labeling을 하면 강화학습을 적용할 수 있겠단 막연한 생각을 했었다. 역시나 누군가는 만들어놨다!

![생각만했었다!](http://hyona-yu.github.io/img/0930post1.jpg)
 HAR 분야에서 2019년에 publish된 논문 하나를 찾았다.[1] weakly labeled data에 대해 처리해서 HAR를 수행했는데, 읽다 보니 일전에 google open ai에서 publish한 논문이 주된 알고리즘이었다.[2] 오늘은 이렇게 두 개의 논문을 리뷰해 보겠다.   






 ### 1. [Weakly Supervised Human Activity Recognition From Wearable Sensors by Recurrent Attention Learning](https://ieeexplore.ieee.org/document/8570822)
참고> labeled: 라벨링 된거, weakly-labeled: 라벨링은 안됐는데 데이터 특징으로 라벨 유추할 수 있는 거, unlabeled: 라벨링 안된거.

센서로 HAR 연구를 하는 논문들이 많이 쓰는 UCI-HAR Dataset을 사용했다. 제목에 나와있는 것 처럼 recurrent model을 사용했다. (센서값이 time serial로 sequential data인 경우가 많다!) 뒤에서 나올 RAM과 DRAM 에 영감을 받은 이 논문은, coarse-grained label만 주어진 학습 데이터를 사용하는  부정확한 지도(inaccurate supervision)에 대해 얘기하고 있다. 즉, labeling이 정확하게 주어지지 않은 학습 데이터로 train을 한다. 일련의 센서 데이터를 시간에 따라 정확하게 labeling하기 힘든 점에 주목했다.

   논문에서 이루고자 하는 바는 크게 두가지다.
* 첫째, weakly labeled data로 행동 인지를 수행할 수 있는 방안 제시
* 둘째, 학습 증진(action exploration)을 위한 강화학습 기반 새로운 reward fcn 제안.

제시한 Model은 아래와 같다.
![논문](http://hyona-yu.github.io/img/0930post2.jpg)
크게 4가지 network로 나눌 수 있다.
1. Context Network. fc(θc): Recurrent Network(LSTM)에 들어갈 첫 initial state를 제공한다. 전체 sensor data의 특징을 뽑아낸다. 3개 Convolution + pooling layer로 이루어져 있다.
2. Glimpse Network. fg(θg): input값은 current location tuple(l), original input sensor data(x). 이를 통해 위치 l 근처에 있는 특징들을 추출한다. 추출할 때 glimpse window는 ws라고 정의, 3개 CNN층으로 x의 특징을 추출 후에 linear layer를 거친 l과 합친다.
![glimpse network](http://hyona-yu.github.io/img/0930post3.jpg)
3. Controller Network. fh (θh)and fl(θl): fh는 LSTM 사용. fl은 mapping하는 용도. Glimpse Network에서 추출한 output (glimpse feature)를 input으로 받아 recurrent state를 갱신하고 next location을 예측한다.
4. Classification Network: Context Network의 output인 global feature Glimpse Network의 output인 local feature를 받아, label을 예측한다.  




위 모델에서 강화학습이 어디서 나올까? 3 Controller network의 학습을 위해 쓰인다. Mother paper에서 reward는 final probablity의 예측 성공 유무로 retrun받는다. 다른 glimpse location과 구별되지 않기에 slow convergence한 문제점을 갖고 있었다. 논문에서 제시된 문제를 풀기 위해서는 reward를 다시 제안해야 한다. 바로 good glimpse local part를 모으는 것이다.즉 예측을 위해 focus를 잘 맞추는 location을 탐색할 수 있도록 reward 함수를 수정하는 것이다. 아래는 그렇기 위해 제시된 두 가지 방법이다. 주의깊게 볼 점을 discount factor가 1인 것.
![glimpse network](http://hyona-yu.github.io/img/0930post4.jpg)

![glimpse network](http://hyona-yu.github.io/img/0930post5.jpg)

 이를 통해 controller network의 loss function은 아래와 같이 표현할 수 있다. (baseline은 Policy Gradient에 대한 2000년 논문(Sutton et al)에서 제시한 값) Action이 next location을 추측한 것이라고 생각하면 될 듯.

 ![glimpse network](http://hyona-yu.github.io/img/0930post6.jpg)


제시한 모델에 대한 weakly-labeled data를 통한 성능 평가다.(논문 필자가 직접 만든 데이터)

+labeled data를 넣어도 성능 향상!

![glimpse network](http://hyona-yu.github.io/img/0930post7.jpg)
