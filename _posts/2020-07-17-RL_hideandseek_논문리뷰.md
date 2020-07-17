---
layout: post
title: 'OpenAI의 Emergent Tool Use From Multi-Agent Autocurricula 논문 리뷰'
date: 2020-07-17
author: hyona
color: rgb(255,210,32)
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: machine learning, reinforcement learning, RL, MultiAgent, OpenAI
---

> Emergent Tool Use From Multi-Agent Autocurricula 논문 리뷰

### [Emergent tool use from multi-agent autocurricula](https://arxiv.org/abs/1909.07528)
Multi Agent에 확 끌리게 해준 OpenAI 논문. Hider와 Seeker가 숨바꼭질을 학습하는 내용이다. 유튜브에 영상이 있다. [Multi-Agent Hide and Seek](https://www.youtube.com/watch?v=kopoLzvh5jY)   
 이 hide-and-seek에는 총 6가지의 emergent phase가 있다. Multi-agent는 environment 복잡성이올라감에 있어서 사람과 같은 도구 사용 등의 능력을 학습한다.

#### Introduction
'Human-relevant', 이 키워드들을 생각하고 논문을 보면 도움이 될듯 하다. 본 실험에서는 총 6가지의 emergence stage가 있다. 'Running and Chasing', 'Fort Building', 'Ramp Use', 'Ramp Defense', 'Box Surfing', 그리고 'Surf Defense'다. 이 새로운 변화가 생길 때 agent들은 적응하기 위해 새로운 압박을 받는다. Environment가 변하고 open-ended(열린 결말)을 맺으수록 관찰을 통한 평가는 어려워진다. 그러므로 본문에서는 targeted intelligence test를 제안하고, 이를 통해 agent들이 학습한다.   
본 논문의 Contribution들은 다음과 같다. 1) multi-agent self-play가 autocurricula를 도출할 수 있다. 2) 물리적 environment(환경)에서 multi-agent autocurricula가 도구를 사용하는 등의 인간과 연관된(human-relevent) 기술을 도출할 수 있다. 3)  open-ended 환경에서 agent들을 test할 수 있다. 4) 이 코드를 오픈소스로 공개했다![github](github.com/openai/multi-agent-emergence-environments.)

#### Hide And Seek
말 그대로 숨바꼭질이다. hider는 seeker가 시야각(135도)에 들어오도록, seeker는 hider의 시야각을 피하도록 task를 받는다. 환경에는 무작위로 움직일 수 없는 방이 생성되어 agent들이 탐험할 수 있다. seeker 찾기 전에 preparation phase가 주어져서, 이 시간을 hider들이 활용할 수 있다. Reward는 team-based로만 받는다. 시야각에 들어오면 1 또는 -1을 각각 받는다. 학습을 위해 너무 멀리가면 agent에게 -10의 reward를 부여한다. 환경 엔진으로는 MUJOCO를 사용했다. hider와 seeker는 각각 1~3명으로 진행을 하며, 이 agent들은 object(ex.ramp)를 이용할 수 있다. Action은 move, grab, lock로 총 3가지가 있다.   

#### Policy Optimization
PPO(proximal policy optimization)와 GAE(generalized advantage estimation), 그리고 rapid라는 RL 프레임워클르 사용했다. Policy는 policy network(이친구가 actor역할을 하는 듯)와 critic network로 이루어져 있다.   
Agent들이 같은 policy parameter를 공유하고 있으나, 관찰은 독립적으로 진행한다. 참고로 agent별로 파라미터를 공유하지 않을 경우 sample efficiency가 감소하는 경향을 보였다.
![algorithm](/img/hideandseek1.png)
위 그림을 보면 object와 agent들의 position 그리고 시야각이 input으로 들어감을 알 수 있다. 또한 agent들의 상호작용이 환경을 변화시키는데(박스 위치를 옮기거나 lock을 거는 등), self-attention based policy architecture를 사용함을 알 수 있다. 앞에서 언급한 6가지의 strategy를 통해 얼마나 많은 각각의 object들이 lock되었나를 관찰하면, 환경이 변화함에 있어서 다루는 tool이 달라짐을 볼 수 있다. Appendix B를 보면 더 자세히 알 수 있는데, policy들이 에이전트 간 공유되고 있고, PPO의 특징인 clip과  TD(시간차 기법)을 활용한 GAE 알고리즘을 확인할 수 있다.   


#### Auto-curricula and emergent behavior
학습 방향에 emergent strategy가 영향을 미친다. 6가지의 strategy들이 보이지 않는 압박을 agent에 주는 것이다. 다시 말하지만 reward는 오직 시야각에 들어오는지의 유무로, 상호작용에 대한 직접적인 보상은 주어지지 앟는다. 25 million 에피소드 이후에 hider가 object를 활용할 수 있게 되고, 75 million 에피소드 이후에 seeker또한 object를 활용하는 등의 초보적인 tool 사용을 배운다. 380 million 에피소드 이후에는 seeker가 box를 끌고 가 그 위를 올라서 hider를 찾는 등의 학습방향을 보여준다. 심지어 hider는 box(object)에 lock을 걸어 seeker가 사용하지 못하게 하기도 했다.   
Batch size가 클 수록 학습 시간이 빨라졌고, 학습 시 환경을 randomize할 수록 autocurriculum이 더욱 robust 해졌다. 여기서 추가적인 object를 환경에 넣어도 agent들은 이를 활용하는 방향으로 학습되었다.   


#### Comparison to intrinsic motivation
Intrinsic motivation은 unsurpervised exploration의 일종이다. 이 알고리즘과 본문에서 쓰인 multi-agent self-play 알고리즘은 에피소드의 과정을 통한 agent와 object의 이동을 측정하여 비교하였다.   
Count-based exploration baseline과 먼저 비교해보면, state가 2D 정보만 가졌을 때 agent와 box 이동이 제일 컸다. 하지만 큰 차원의 정보를 state로 부여했을 때는 급격하게 낮아졌다. 이 때 count based exploration은 state의 '흥미로운' 부분을 직접 지정을 해주어야 했다. 본문의 알고리즘은 이이런 지도학습은 필요하지 않다. Random network distillation(RND)은 Count-based보다 나았다.   
Appendix D에서 자세히 기술했는데, Sequential Lock task로만 비교 실험을 진행하였고, 세부적인 parameter에 대해 설명했다. Intrinsic motivation에서는 velocity 값을 빼고 position state만 있을 때 학습이 더 잘됐다.

<hr>
결국 multi-agent autocurricula가 물리적인 기반를 두고, 인간과 연관된 행동을 이끄는 것을 알 수 있다. ppo가 답인가..!
