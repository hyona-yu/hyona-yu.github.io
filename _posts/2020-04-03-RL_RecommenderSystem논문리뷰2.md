---
layout: post
title: '[Paper]RL Recommender System 논문 리뷰 2'
date: 2020-04-04
author: hyona
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: recommendation system paper, paper review, machine learning
---

> 강화학습을 이용한 추천 시스템 논문을 탐구해보자 No2

# Papers
지난 리뷰에서는 총 4개의 논문을 탐구해 보았다. 이번에는 최근에 읽은 논문 중 흥미로운 논문 하나를 리뷰할 것이다.
## 1.[Are We Really Making Much Progress? A Worrying Analysis of Recent Neural Recommendation Approaches](https://arxiv.org/abs/1907.06902)

### Introduction & Research Method
2019년에 등재된 따끈따끈한 논문. 이름에서부터 풍겨오는 아우라와 같이, 최근(2015-2018)에 나온  KDD, SIGIR, TheWebConf (WWW), and RecSys 의 top-n recommendation task중 18개의 알고리즘을 선정하여 '과연 정말 성능이 올랐을까?'를 논하는 내용으로 이루어져 있다. 딥러닝 알고리즘과 top-n recommendation을 활용한 논문들에 대해 탐구한다.   
주의해서 봐야할 점은 이거다. 2009년에 나왔던 논문의 추천 알고리즘의 개선사항은 "don't add up", 즉 추가하지 마라 였다. 왜일까?   
##### 1. weak baseline   
##### 2. establishment of weak methods as new baselines
##### 3. difficulties in comparing results across Papers
3번에서 기술한 것은 저번 리뷰에서 다룬 'Survey'에서 꼽은 문제점이기도 하다.   
여기서 저자는 어떻게 18개 딥러닝 추천 알고리즘의 문제점을 파악할지에 대해 아래 두개의 의문을 갖고 출발하게 된다.
### Reproducibility(Reasonable) & Progress
18개의 논문 중 구현(Reproduce) 기준에 부합된 논문은 고작 7개다. 이 기준은 각 논문의 저자가 올려놓은 '소스코드와 데이터'로 그들이 주장한 결과가 나와야 한다는 거다. 7개중 6개는 발전한 성능을 내지 않았다. 간단하고 잘 튜닝된 데이스라인에서조차 말이다.    
진보함(progress)를 평가하기 위해 2018년에 나온 논문 "Evaluation of Session-based Recommendation Algorithms"에 의거하여, 각 논문들이 썼던 같은 dataset과 environment에서 각각 평가를 진행한다.
### Validation against baselines
 UserKNN, ItemKNN 등의 알고리즘과 비교해본 결과이다.
1. "Collaborative Memory Networks(CMN)". 소수의 dataset에서만 진보한 성능을 내는 모습을 볼 수 있었다.
2. "Metapath based Context for RECommendation(MCRec)". top-n recommendation과 priority-based sampling을 한 모델이다. ItemKNN이 해당 알고리즘을 제치고 가장 높은 성과를 냈다.
3. "Collaborative Variational Autoencoder(CVAE)". 4번의 CDL을 baseline으로 사용했다. 위에서 기술한 개선사항 2번 "establishment of weak methods as new baselines"에 해당되겠다.
4. "Collaborative Deep Learning(CDL)". stacked denoising autoencoder + collaborative filtering의 결합 형태이다.
5. "Neural Collaborative Filtering(NCF)". NN + collaborative filtering. sklearn의 elasticnet이 더 성과가 좋아버렸다.
6. "Spectral Collaborative Filtering(SpectralCF)". cold-start problem을 위한 Spectral Graph Theory 기반 알고리즘이다. train-test split이 Gini index에 기반해 편파적인 편이다. random 하게 잘 섞어서 학습과 검증을 하자!
7. "Variational Autoencoders for CF". 드디어 나타난 outperformed algorithm! feedback based variational autoencoder로, 학습의 파라미터를 중점적으로 tuning한 모습이다.   

### Discussion & Summary
결국 성과를 확인하기 위해서는 코드가 공유되어야 하고, 그 코드와 데이터를 기반으로 reproduce하였을 때 같은 결과가 나와야 한다. 또한 많은 논문이 neural collaborative filtering을 최첨단 기술로 사용하고 있으나, 간단한 baseline 알고리즘이 더 우수함을 나타냈다. 복합적인 neural 모델(예를 들면 RNN)을 baseline으로 잡고 실험을 하더라도, 결국 이는 앞에서 말한 weak baseline이 된다는 것이다. 공공 dataset을 사용할 것인지, 해당 dataset이 어떤 평가지표를 더 중요시 여기는 지도 중요한 점이니 새 알고리즘을 만들어 실험할 때 참고하도록 하자.


<hr>
 정리해보자면 대부분의 최신 논문 알고리즘은 (item)KNN, CF(Collaborative Filtering), DBF(content based Filtering)과 같은 baseline의 성능에 미치지 못한다. 기본적인 baseline 알고리즘을 뛰어넘는 새 알고리즘 만들기가 힘들다는 것을 의미하리라. 더 좋은 Recommender Sysstem을 설계하기 위해서는 어떻게 해야 할까? 어쩌면 dataset의 기깔나는 전처리가 답이 될 수도 있지 않을까?
