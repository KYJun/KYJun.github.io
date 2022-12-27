---
layout: post
title: "강화학습 튜토리얼 07 : On-Policy와 Off-Policy"
categories: rl
--- 
On & Off Policy

> 강화학습 튜토리얼은 다음 자료를 참고하였습니다.
> 1. [David Silver RL Lectures](https://www.davidsilver.uk/teaching/)
> 2. [Dennybritz RL Github](https://github.com/dennybritz/reinforcement-learning)
>3. [Arthur Juliani's Medium Posts](https://medium.com/emergent-future/simple-reinforcement-learning-with-tensorflow-part-0-q-learning-with-tables-and-neural-networks-d195264329d0)
>
> 최소한의 수식을 사용하고, 누구나 이해할 수 있는 설명을 목표로 작성했습니다.
> 피드백은 항상 환영합니다.

### Epsilon-Greedy Approach

이번 포스트는 지난 포스트에서 이어지므로, 지난 포스트를 먼저 읽고 오는 것을 추천드립니다.

간단하게 지난 포스트 마지막 부분에 지적한 문제를 살펴보겠습니다.

강화학습의 시뮬레이션 단계에서는 Exploration과 Exploitation의 균형을 맞추기 위해 Epsilon-Greedy Approach가 주로 사용된다고 하였습니다.

Epsilon-Greedy Approach는 처음에는 무작위로 시뮬레이션을 진행하다 점차 학습 결과에 입각한 Greedy Approach를 빈번하게 사용하도록 하는 방식입니다.

Epsilon은 방식을 전환하는 확률을 나타내는 변수로, 0이 되지 않습니다.

해당 방식으로 학습을 진행하면 어떤 결과로 이어지게 될까요?

<br>

### Bellman Equation

우선 Value-based 방식으로 강화학습이 진행된다고 가정하겠습니다. 

특정 State에서 각 Action에는 Value가 할당됩니다. 이를 State-Action Value, 또는 Q-Value라고 부르며, Q-Value가 최대가 되는 Action을 선택하는 것이 Agent가 채택할 수 있는 최적의 Policy가 됩니다.

초기 Q-value는 무작위 값이 주어지게 됩니다. 이후 시뮬레이션 단계에서 얻은 결과값, 즉, Return 값에 따라 선택받은 State-Action 쌍에 대한 Q-value가 갱신됩니다.

Q-value 갱신은 다음 식에 의해 진행됩니다.

![bellman](/assets/05_vp/bellman.png)

해당 식은 Bellman Equation이라고 불립니다.

여기서 주목할 점은 현재 State의 Q-value의 계산에 다음 State의 Q-value가 필요하다는 것입니다. 

계산의 편의성과 효율성을 도모했지만 Q-value의 갱신에 시뮬레이션 단계가 주는 비중이 무척 증가했다는 특징이 있습니다.

<br>

### 절벽 타기 게임

먼저, 예시를 위해 아래 문제를 푼다고 생각해보겠습니다.

![cliff](/assets/07_onoff/cliff.png)

목표는 시작점에서 최대한 빨리 끝점을 향해 이동하는 것입니다. 단, 절벽(Cliff)에 도달하면 실패하게 됩니다.

각 지점에 대한 Reward 설정은 절벽이 아닌 부분은 -1, 절벽은 -10, 도착점은 +10 정도가 될 것입니다.

<br>

상식적으로 판단했을 때, 최단 경로는 다음과 같습니다.

![optimal](/assets/07_onoff/optimal_path.png)

<br>

하지만 강화학습 결과로 배운 경로는 다음과 같았습니다.

![safe](/assets/07_onoff/safe_path.png)

왜 이런 결과가 나왔을까요?

<br>

### 안전한 선택

절벽 바로 옆에 위치한 칸에서 Agent는 다음 Action을 선택하게 됩니다. 

절벽으로 향하는 Action은 Q-value가 무척 낮아 Greedy Approach에서는 선택되지 않을 것입니다. 

문제는 Random Approach가 적용된 선택에서 발생합니다.

비록 무작위 선택을 하게 되어 절벽으로 향하는 Action을 선택할 확률이 적다고 하더라도 결코 0이 아닙니다.

실제로 절벽 바로 옆칸의 Q-value는 위쪽 칸에 비해 낮은 값을 갖게 되어, Q-value 값에 입각하여 움직이는 Agent는 해당 경로를 가지 않게 되는 것입니다.

<br>

### On-Policy

이전에 Agent가 움직이는 원칙을 곧 Policy라고 부른다고 했습니다.

Epsilon-Greedy Approach 역시 Agent의 행동 방식을 규정한다는 점에서 하나의 Policy라고 볼 수 있습니다.

해당 방법의 문제는 Agent가 최적화하는 Policy가 Greedy Policy가 아니라 Epsilon-Greedy Policy가 된다는 점에 있습니다. 

다시 강화학습 흐름을 살펴봅시다.

Policy는 강화학습의 진행 과정에서 두 번 개입하게 됩니다.

1. 시뮬레이션에서 State 이동시
2. 학습에서 다음 State의 Q-value 계산시

Agent는 두 단계 모두에서 같은 Epsilon-Greedy Policy에 입각해 샘플링 및 계산을 진행합니다.

즉, 우리가 최적화하는 대상이 일정 확률로 무작위 행동을 취할 수 있는 Policy라는 문제가 발생하는 것입니다.

해당 방식을 **"On-Policy"**라고 부릅니다.

이런 On-Policy의 문제 해결을 위해선, 간단히 두 단계에 사용되는 Policy를 분리하면 됩니다.

서로 다른 Policy를 사용한다고 하여, 해당 방법을 **"Off-Policy"**라고 부릅니다.

<br>

### Off-Policy

Off-Policy 방식의 핵심은 Policy의 구분입니다.

시뮬레이션 단계에서 Agent는 Exploration과 Exploitation 사이에서 고민해야 합니다. 따라서, **"Epsilon Greedy Policy"**를 고수해야 합니다.

하지만 Agent의 목표는 **"Greedy Policy"**가 되어야 합니다. 주어진 경험을 최대한 활용한 방식이 최적의 선택을 이끌어낼 수 있을 것이기 때문입니다.

Q-value 갱신 과정에서 사용될 다음 State의 Q-value 계산에서는 Greedy Approach를 활용해서 얻은 Return을 반영시키는 것입니다.

앞선 절벽 문제에서 Off-policy 방식을 채택하면, 절벽 바로 옆 경로의 Q-value는 무작위성이 사라졌기 때문에 높은 값을 얻게 됩니다. 결과적으로 의도한 최단 경로를 찾을 수 있게 되죠.

<br>

### 맺음말

On-Policy와 Off-Policy의 차이를 비교하며 Epsilon-Greedy 방식의 한계를 극복하는 방안을 살펴보았습니다.

본 포스트에서는 Off-Policy의 장점에 대해 설명했지만 On-policy가 무조건 단점만 있는 방식은 아닙니다. SARSA Learning과 같이 On-policy 방식을 활용한 강화학습 방법도 실제로 연구되어 왔습니다.

Off-Policy 방식의 단점은 구현 방식입니다. Importance Sampling라는 수학적 연산이 더해져, 계산 부담이 가중됩니다.

그러나 Off-Policy의 장점을 무시할 수 없었기에, 이후 연구는 간편한 방법으로 Off-Policy를 구현하는 것에 집중되게 됩니다.

해당 연구의 대표적인 예시로 Deep Q-Network가 있습니다. 관련 설명은 해당 내용을 다루는 포스트에서 보다 자세히 진행하겠습니다.

다음 포스트에서는 Monte Carlo와 Temporal Difference의 차이를 통해 실제 시뮬레이션을 학습에 반영하는 과정을 살펴보도록 하겠습니다.
