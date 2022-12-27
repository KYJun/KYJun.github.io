---
layout: post
title: "강화학습 튜토리얼 02: 강화학습 구조"
categories: rl
---
Interface : 강화학습의 기본 구성요소

> 강화학습 튜토리얼은 다음 자료를 참고하였습니다.
> 1. [David Silver RL Lectures](https://www.davidsilver.uk/teaching/)
> 2. [Dennybritz RL Github](https://github.com/dennybritz/reinforcement-learning)
>3. [Arthur Juliani's Medium Posts](https://medium.com/emergent-future/simple-reinforcement-learning-with-tensorflow-part-0-q-learning-with-tables-and-neural-networks-d195264329d0)
>
> 최소한의 수식을 사용하고, 누구나 이해할 수 있는 설명을 목표로 작성했습니다.
> 피드백은 항상 환영합니다.

### 강화학습 배경

이전 포스트에서 강화학습은 훈련에 앞서 시뮬레이션 단계를 거친다고 언급했습니다.

시뮬레이션 단계에서 가장 중요한 것은 각 요소 간의 상호작용을 관측하는 것입니다. 

본격적인 강화학습 내용에 들어가기에 앞서 강화학습의 각 구성 요소가 무엇이며, 어떤 상호작용을 거치는지 살펴보고자 합니다.

<br>

### 강화학습의 구성 요소

아래는 강화학습의 Interface를 간략하게 나타낸 그림입니다. 

![interface](/assets/02_rl_interface/interface.png)
> (출처 : https://www.davidsilver.uk/wp-content/uploads/2020/03/intro_RL.pdf)

먼저 크게 Agent (에이전트)와 Environment (환경)라는 두 대상이 있고, 각 대상 간의 상호작용을 나타내는 action (행동)과 reward (보상), observation (관측)이라는 요소가 있습니다. 

<br>

각각을 자세히 설명하면 다음과 같습니다.

- Agent : **행동 주체**가 되는 대상입니다. Agent는 Environment와 상호작용을 통해 현재 상태를 관측하고, 다음 행동을 선택합니다. 게임에서 플레이어를 가리킨다고 볼 수 있습니다. 우리의 현재 목표는 이 Agent가 게임에서 승리하도록 만드는 것입니다.

<br>

- Environment : 행동의 배경이 되는 **환경**입니다.  Agent는 이 환경 속에서 상호작용을 하게 됩니다. 환경은 게임의 규칙이 될 수도 있고, 게임 화면 내에 비치는 대상이 될 수도 있습니다. 적 캐릭터, 회복 포션, 절벽, 사다리, 점수, 체력 등등 Agent 주변의 모든 요소가 환경에 해당하게 됩니다. 환경은 Agent에게 선택을 요구하고, 선택 결과에 대한 대가를 제공합니다.

<br>

- Action : Agent가 취하는 **행동**을 가리킵니다. 가능한 행동의 범위는 Environment에 의해 한정되며, 최종적으로 행동을 결정하는 주체는 Agent입니다.

<br>

- Reward : Agent의 선택으로 특정 행동을 취하게 되면 환경은 이에 상응하는 **대가**를 제공합니다. 좋은 행동이라면 Agent에게 긍정적 영향을 줄 것이며, 반대라면 부정적 영향을 끼칠 것입니다. Reward의 종류는 점수의 증가 또는 감소, 체력 회복 또는 부상, 게임 오버, 게임 클리어 등 다양하지만 계산의 편의성을 위해 주로 숫자의 형태로 제시됩니다. Agent의 입장에서 Reward는 선택에 대한 평가 지표가 됩니다.

<br>

- Observation : 우리는 Agent의 시각으로 Environment를 바라보게 됩니다. 중요한 것은 Agent의 관측 가능한 영역입니다. 바둑의 경우, 두 플레이어의 모든 돌의 위치와 전체 판을 한 번에 관측할 수 있죠. 반면 스타크래프트의 경우, 각 플레이어는 맵의 제한된 부분만 관측할 수 있습니다. 이러한 관측 범위의 차이는 Agent의 선택에 영향을 주게 됩니다.

<br>

### 맺음말
간단하게 강화학습의 기본적인 Interface와 구성요소를 살펴보았습니다.

현재 우리의 목표는 "Agent가 관측할 수 있는 환경의 범위 내에서 가장 큰 Reward를 얻는 Action을 선택하는 것"입니다. 

그렇다면 해당 요소들을 어떻게 컴퓨터에게 인식하게 만들 수 있을까요?

다음 시간에 살펴볼 Markov Decision Process를 통해 기계의 시각으로 Interface의 요소들을 다시 검토하도록 하겠습니다.
