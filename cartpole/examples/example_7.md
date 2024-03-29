
## 개념
* * *

### 강화 학습
* * *

강화학습을 이해하기 위해 몇가지 기본적인 용어 또는 개념을 이해해야 한다.

‘에이전트’는 주어진 ‘환경’에서 ‘행동’을 선택하고,

그 ‘환경’에서 ‘상태’와 ‘보상’이 만들어진다.

<img src="/png/701.png">

에이전트의 목표는 주어진 환경에서 상태와 행동을 통해 얻어지는 보상이라는 정보를 잘 확인해서 누적 보상을 최대화하는 것이다.

에이전트는 주어진 환경과의 반복되는 상호작용 속에서, 누적 보상을 최대화하기 위해 어떤 선택이 가장 좋은 선택일지 학습하게 된다. 에이전트가 이러한 과정으로 행동을 선택하는 것을 행동 정책 (action policy)이라고 한다.

또한 에이전트가 주어진 환경의 특정 상태에서 행동을 선택하는 과정, 그리고 행동 정책을 구현하는 과정에서 가장 흔하게 Deep Q network 와 epsilon-greedy 정책 을 사용한다.


### Q-learning
* * *

#### Q-table
Q-learning은 ‘값’에 기반해서 어떤 행동을 선택하는 것이 좋을지 에이전트에게 알려주는 방식이다.

행동을 선택하는데 있어서 기초가 되는 값들을 선정하는데 있어서 처음으로 생각할 수 있는 직관적인 아이디어는 여러번의 게임을 통해서 어떤 행동 a를 선택했을 때의 보상을 정리한 표를 만드는 것이다. 어떤 상태에서 어떤 움직임을 하는 것이 가장 이득이 되는지 파악할 수 있다.

예를 들어, 특정 상태마다 네가지 선택지가 있는 게임을 간단한 게임을 생각해보자. 이 게임의 모든 상황과 선택에 대한 보상을 아래와 같이 정리할 수 있다.

<img src="/png/702.png">

이 표를 통해서 에이전트가 게임의 각 상황에서 어떤 선택을 하는 것이 가장 큰 보상으로 이어지는지 알 수 있다. 다시 말해서, 에이전트는 단순히 이 표를 외우고 있다면 게임을 잘 할 수 있다.

#### 지연된 보상

이전의 게임은 3개의 상태와 4개의 선택 옵션을 갖는 매우 간단한 게임이었지만, 실제 게임은 훨씬 복잡하다.

현실적인 게임을 제대로 플레이하려면, 에이전트는 즉시 보상으로 이어지지 않을 수도 있고 큰 보상으로 이어질 수도 있는 행동을 취할줄 알아야 한다.

아래에 약간 더 복잡한 게임이 있다.

<img src="/png/703.png">

위의 게임에서 state2 에서 아래 방향을 선택했을 경우, 15의 보상을 받지만 게임이 종료되어서 처음 상태인 state1로 돌아가게 된다. 만약 오른쪽 방향을 선택해서 state4로 이동한 후 아래 방향을 선택한다면 50의 보상을 받을 수 있다.

다시 말해서 에이전트는 즉시 받을 수 있는 15의 보상을 받기보다 한번 더 움직여서 더 큰 보상을 받을 수도 있다. 당장의 작은 보상보다 지연된 더 큰 보상 을 받을 수 있는 선택을 할 줄 알아야 한다.

#### Q-learning 룰

따라서 Q-learning의 룰을 정할 필요가 있다. Deep Q learning에서 뉴럴 네트워크는 현재 상태 s를 입력으로 받아서, 선택할 수 있는 각각의 행동 a에 대한 값 (Q-value)을 반환한다.

즉, 우리의 뉴럴 네트워크는 모든 상태 s에 대한 선택 a의 Q(s, a) 값을 반환해야 한다. 또한 이 Q(s, a) 값은 훈련 과정에서 아래의 룰을 통해 업데이트된다.

<img src="/png/704.png">

수식을 살펴보면, 현재의 Q(s, a) 값에서 더하기 표시 오른쪽 항만큼 업데이트됨을 알 수 있다. 대괄호 안의 r은 상태 s에서 행동 a를 취함으로써 즉각적으로 얻는 보상을 나타낸다.

그 다음 항은 지연된 보상에 대한 계산을 나타낸다. 우선 𝛾 는 지연된 보상의 효과를 줄이는 역할을 하며, 0에서 1 사이의 값을 갖는다. max𝑎′𝑄(𝑠′,𝑎′) 는 다음 상태에서의 모든 Q-value의 최대값을 나타낸다. 좀 더 정확히, 만약 상태 s에서 행동 a를 취해서 상태 s’가 된다고 하면, max𝑎′𝑄(𝑠′,𝑎′) 는 상태 s’에서의 모든 선택 a’에 대한 Q-value의 최대값을 의미한다.

그렇다면 왜 이 항 (max𝑎′𝑄(𝑠′,𝑎′))을 고려할 필요가 있을까? 이 항은 에이전트가 상태 s에서 행동 a를 취했을 때 예상할 수 있는 미래 최대 보상이기 때문입니다. 하지만 이 값은 𝛾 값으로 그 효과를 줄일 필요가 있다. 왜냐하면 항상 미래의 보상을 위해 참는게 최선이 아닐 수도 있기 때문이다. 가장 좋은 것은 짧은 시간 동안 최대의 보상을 얻는 것이다.

여기에서 Q(s’, a’) 값은 그 다음 상태의 𝛾max𝑎″𝑄(𝑠″,𝑎″) 값을 암시적으로 포함하고 있음을 알아야 한다. 또 그 다음, 그 다음에 대해서도 마찬가지이다. 이런 과정을 통해 에이전트는 즉각적인 보상 r 뿐만 아니라 미래의 효과가 줄어든 (discounted) 보상에 대해서도 고려하면서 행동 a를 선택할 수 있다. 

𝛼 항은 업데이트 과정에서 사용되는 학습률 (learning rate)을 나타낸다. 

### Deep Q learning
* * *

Deep Q-learning은 학습 과정에서 Q-learning의 업데이트 룰을 적용한다.

다시 말해서, 현태 상태 s를 입력으로 받는 뉴럴 네트워크를 구성하고, 뉴럴 네트워크가 상태 s의 모든 행동 a에 대해 적절한 Q(s, a) 값을 출력하도록 훈련시킨다. 훈련이 이루어지고 나면, 에이전트가 가장 큰 Q(s, a) 값 을 나타내는 행동 a를 선택할 수 있다.

<img src="/png/705.png">

에이전트가 행동을 선택하고 나면 (Action selection), 그 상태에서 그 행동을 취함으로써 얻는 보상이 결정된다. 이제 우리가 해야할 일은 두번째 그림 (Training)과 같이 Q-learning 룰에 따라 네트워크를 훈련하는 것이다. 행동 a에 대한 Q(s, a) 값에 대해서만 𝑟+𝛾𝑄(𝑠′,𝑎′) 를 목표값으로 해서 훈련이 이루어진다.

이러한 방식으로 뉴럴 네트워크를 훈련하고 나면, 이제 출력되는 Q(s, a) 값들은 에이전트에게 그 상태에서 어떤 선택이 가장 좋은 선택인지 알려줄 수 있게 된다.

### Epsilon-greedy 정책
* * *

이전의 내용에서 행동을 선택하는 정책은 단순히 뉴럴 네트워크가 가장 높은 Q(s, a)를 출력하는 행동 a를 선택하는 것이었다.

하지만 이 정책을 가장 효과적인 방식이라고 할 수는 없다. 왜냐하면 우리의 뉴럴 네트워크는 처음에 무작위로 초기화되고, 그래서 차선의 행동들을 임의로 선택할 가능성이 있기 때문이다.

다시 말해서, 에이전트가 전체 게임의 모든 상태-행동-보상 공간을 탐색하지 못하고 최선이 아닌 차선의 선택들을 하게 될 수 있다. 게임의 가장 좋은 전략을 찾지 못하는 것을 의미한다.

#### 탐색과 활용

이제 탐색 (exploration) 과 활용 (exploitation) 이라는 두가지 개념을 이해할 필요가 있다.

최적화 문제의 시작점에 있어서 좋은 지역 최소값 (local minima) 또는 가능하다면 전역 최소값 (global minima)을 찾기 위해서 주어진 문제 공간을 광범위하게 탐색하는 것이 좋다. 

하지만 공간을 적절하게 한 번 탐색하고 나면, 이제 발견한 것들을 잘 활용하면서 가장 작은 최소값을 찾는 것이 최적화 알고리즘이 좋은 해답을 찾는데 있어서 가장 좋은 방법이다.

그래서 강화학습에 있어서, (특히 훈련의 초반일수록) 행동을 선택하는데 있어서 약간의 임의성 (randomness)을 부여한다. 그리고 이 임의성을 𝜖 값으로 정의한다.

만약 0에서 1 사이의 임의의 숫자를 얻었을 때, 𝜖 값보다 작다면, 임의의 행동을 선택한다. 그리고 𝜖 값보다 크다면, 뉴럴 네트워크의 출력값에 근거해서 행동을 선택한다. 이 𝜖 값은 학습 초반에 크고 학습이 이루어 질수록 0에 가까운 값으로 작아진다. 이렇게 해서, 학습의 초반에 더 많은 가능성들을 탐색하고, 학습의 후반에 뉴럴 네트워크가 좋은 정답을 출력할 수 있도록 한다.

사람에 비유하면, 이런저런 경험을 많이 해본 사람이 시간이 지나고 나면 여러가지를 고려한 좋은 선택을 할 수 있는 것과 비슷하다고 할 수 있다.