---
layout: post
title: "강화학습 튜토리얼 05 : Value & Policy"
categories: rl
---
Value and Policy : 가치와 정책

> 강화학습 튜토리얼은 다음 자료를 참고하였습니다.
> 1. [David Silver RL Lectures](https://www.davidsilver.uk/teaching/)
> 2. [Dennybritz RL Github](https://github.com/dennybritz/reinforcement-learning)
>3. [Arthur Juliani's Medium Posts](https://medium.com/emergent-future/simple-reinforcement-learning-with-tensorflow-part-0-q-learning-with-tables-and-neural-networks-d195264329d0)
>
> 최소한의 수식을 사용하고, 누구나 이해할 수 있는 설명을 목표로 작성했습니다.
> 피드백은 항상 환영합니다.

<br>

### 최적화

지난번 포스트에서는 강화학습에서 최적화의 대상 중 하나인 Model에 대해 살펴보았습니다. 

Model은 MDP를 기반으로 구축되며, 현재 주어진 환경에서 Agent가 선택할 수 있는 최적 경로를 도출해주는 역할을 하였습니다. 

Model은 환경의 축소판으로, 보다 Agent의 목표에 충실한 환경의 재구성판이라고 볼 수 있습니다.

하지만 Model은 강력함에 따른 큰 부담이 존재했습니다. 여기서 등장한 것이 Model-Free 방식입니다. 

Model-Free 방식은 환경을 다시 정의하는 복잡한 방법을 택하지 않습니다. 대신 시뮬레이션을 통해 얻은 경험을 토대로 Agent의 선택을 돕는 이정표를 학습합니다.

이 이정표는 다시 Value와 Policy로 나뉩니다. 각각을 예를 들어 설명해 보도록 하죠. 

<br>

### Agent와 Environment

플레이어 A와 B는 각각 미로에 들어왔습니다. 둘은 한 번도 미로에 들어와 본 적이 없었고, 지도도 갖고 있지 않습니다.

A는 미로 바닥에 숫자가 써있는 걸 발견했습니다. 갈림길에서 숫자가 큰 쪽을 따라 진행하자 금방 출구에 도달하게 되었습니다.

![value](/assets/05_vp/value_maze.png)
> A에게 주어진 점수는 Value로 볼 수 있다.

B는 갈림길에 화살표가 있는 걸 발견했습니다. 화살표가 가리키는 방향을 따라 진행하자 마찬가지로 출구에 도착할 수 있었습니다.

![value](/assets/05_vp/policy_maze.png)
> B에게 주어진 화살표는 Policy로 볼 수 있다.

A와 B는 매 State마다 다음 Action 선택에 대한 단서를 제공받았습니다. A는 다음 State에서 얻을 수 있는 예상 점수를, B는 다음에 선택해야 하는 Action을 알 수 있었습니다. 

A는 최적의 Value를 따라, B는 최적의 Policy를 따라 문제를 해결한 것이다.

<br>

### Value-based vs. Policy-based

Value와 Policy의 가장 큰 차이는 Agent의 결정에 미치는 영향에 있습니다.

Value는 A에게 주어진 점수처럼 환경에 제시되어 있습니다.  Agent는 이 점수를 관측하여, 자신의 행동 근거로 삼게 됩니다.

당연히 Agent는 점수가 최대가 되는 State 혹은 Action으로 이어지는 선택을 할 것입니다.

Policy는 화살표로, Agent의 선택 그 자체입니다. Agent는 Policy를 따르기 때문에 반드시 어떤 형태로든 Policy가 제공이 되어야 선택이 가능합니다.

B의 경우 매번 한 가지 행동만이 제시되었지만, 대개 Policy는 확률로 제시됩니다. 두 갈래로 나뉜 갈림길에서의 Policy가 오른쪽 70%, 왼쪽 30%라면, Agent는 해당 확률에 입각하여 다음 행동이 정해집니다.

결론적으로, Value가 환경의 입장에서 제시되는 관측치로, 행동에 간접적으로 영향을 주는 변수라면 Policy는 행동에 직접적으로 영향을 주는 확률이라고 할 수 있습니다.

<br>

### Value-Based

Value-Based 에 대해 조금 더 자세히 살펴보기 위해선 어떻게 Value를 구하는지에 대해 살펴보아야 합니다.

Value의 종류에는 여러 가지가 있지만 주로 활용되는 것은 State와 Action을 입력값으로 받아 점수를 출력해주는 Q-Value이다.

Q-Value는 다음과 같이 정의할 수 있습니다.

![bellman](/assets/05_vp/bellman.png)
> 해당 식은 Bellman Equation이라고도 불립니다.

위 식을 정리하면 현재 State(s)와 Action(a)의 Q-value는 해당 action을 통해 진행한 다음 State(s')에서 얻게 되는 Reward(R)와 다음 State에서 가능한 Action(a') 간 얻을 수 있는 Q-value의 기대값에 discount factor(gamma)를 곱해서 합한 값이라는 것이다.

> Return과 Discount Factor는 MDP 포스트를 참조

즉, 현재의 Q-value를 구하기 위해선 필연적으로 다음 Q-value가 필요하다.

연쇄적으로 다음 단계로 넘어가다 보면, 마지막 State, 즉, 다음 State가 존재하지 않는 상황에 이르게 될 것이며, 여기서의 Q-value는 Reward로 한정된다.

해당 State에서 역으로 계산해 오면, 매 단계마다의 Q-value 계산이 가능하다.

이렇듯 각 상태 별로 Q-value를 모두 알게 된다면 해당 값을 지표로 삼아 최적의 선택을 할 수 있게 됩니다.

Value-based 강화학습에서는 Agent의 목표는 다음과 같이 간단하게 변화한다.

**"현재 State에서 Value가 최대인 Action을 선택하는 것"**

<br>

### Policy-based

Policy는 얼핏 Value와 유사한 것처럼 보입니다.

이는 Agent가 움직이기 위해선 Policy가 필요하기 때문입니다.  Value-based 방식이라도 결국 '최대의 Value를 따른다'는 Policy를 제시되었고, Agent는 이를 따른 것입니다. 

Policy-based의 특징은 환경과의 상호작용을 거치지 않고, Agent의 행동에 직접 개입할 수 있다는 것입니다.

Policy는 자체적으로 확률적 특징을 갖고 있습니다.

예를 들어, 갈림길에서 왼쪽과 오른쪽의 Q-value가 동률이 나왔다고 가정해 봅니다.

Value-based라면 Agent는 무조건 조금이라도 높은 Value를 따라 매번 같은 선택을 할 것이며, 다른 길을 선택할 가능성은 매우 낮습니다.

반면, Policy-based라면 Agent는 두 길을 거의 비슷한 확률로 책정할 것이며 두 길 모두를 비슷한 확률로 방문하게 될 것입니다.

Policy 기반 강화학습에서는 Agent의 목표는 다음과 같습니다.

**"현재 State에서 제시되는 Policy에 따라 행동하는 것"**

Agent는 Policy에 따라 행동한다고 했다는 걸 기억하신다면, 여태 계속 목표라고 적은 모든 것이 곧 Agent의 Policy의 종류라 볼 수 있을 것입니다.

<br>

### 맺음말

이렇게 Value-based와 Policy-based 방식에 대해 살펴 보았습니다. 지난 시간에 다룬 Model-based 방식과 함께 강화학습에서 최적화의 대상이 되는 3가지 입니다만, 해당 3가지는 상호 배타적이기 보다는 상호 유기적인 모습을 취합니다. 

Model을 통해 최적의 Value를 얻고, Value에서 최적의 Policy를 얻는 등 실제로 두 가지 이상을 복합적으로 학습하는 방식이 활용되기도 합니다.

다음 시간에는 실제 강화학습의 훈련 과정에 들어가기에 앞서 본 포스트에서도 잠깐 언급되었던 시뮬레이션 단계에서 선택 방식에 대해 조금 자세히 살펴보도록 하겠습니다.
