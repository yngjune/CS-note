# Greedy
연속적으로 각 시점에서의 최선의 선택을 통해 문제를 해결하는 알고리즘

### 그리디를 적용할 수 있는 문제
* 연속적으로 부분적인 선택을 수행
* 매 순간의 선택이 전체 문제의 최적의 해에 반하지 않음
* 즉, 현 시점에서의 선택을 연장해 최적의 해를 만들 수 있는 경우

<br/>

### Common Proof Strategy
#### Induction
* Inductive Hypothesis : $t$번째 선택이 최적의 해에 반하지 않음
* Base case : 아무 선택을 하지 않은 경우이므로 최적의 해로 연장 가능
* Inductive step : $t$번째 선택까지 최적의 해에 반하지 않았을 때 $t+1$번째 선택도 최적의 해에 반하지 않음
* Conclusion : 마지막에 도달한 경우, 그리디를 통해 문제를 해결할 수 있음

#### Contradiction
* Inductive step을 증명하기 위함
* 각 단계의 선택을 통해 최적의 해 $T^*$를 만들어 간다 가정
* $T^*$이 다음 단계의 그리디 선택에 위반된다 가정
* $T^*$을 변형해 다음 그리디 선택에 부합하며 여전히 최적의 해인 $T$를 생성

<br/>

### vs Dynamic Programming
* 두 알고리즘 모두 optimal substructure 활용
* DP와 달리 그리디 알고리즘은 하나의 부분 문제의 해만 활용