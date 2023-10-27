# Tree
* 계층적인 구조
* 비선형 자료구조
* 노드의 부모-자식 관계
* 한 개 이상의 노드 {루트 노드, 서브트리 노드} 로 이루어진 유한한 집합

<br/>

### Terminology
* root node : 부모가 없는 노드. 트리에는 단 하나의 루트 존재
* leaf node : 자식이 없는 노드
* internal : 말단 노드를 제외한 노드
* degree : 서브 트리의 개수
* depth : 루트 노드에서 현재 노드까지 간선의 수

<br/>

### Tree Representation
#### List Representation
* 노드의 크기(degree)가 고정된 것이 표현 용이
* 차수가 $K$인 트리의 각 노드를 길이가 $K+1$인 리스트로 표현
* 리스트의 요소는 데이터와 $K$개의 서브트리에 대한 포인터
* 노드의 크기(차수)가 고정된 편이 표현에 용이
* **메모리 낭비**가 심하다
    * $N$개의 노드, 차수 $K$의 자식 필드 개수는 $NK$
    * 이 중 자식을 가리키는 필드는 $N-1$개 (부모가 있는 노드가 $N-1$개이므로)
    * 그러므로 $N(K-1)+1$개의 필드는 비어 있음

#### Left Child - Right Sibling
* 트리의 각 노드를 리스트로 나타냄
* 리스트의 요소는 `[data, left child subtree, right sibling subtree]`
* 메모리의 낭비 감소
* 이진 트리로 변환 가능

<br/>

# Binary Tree
* 노드가 없거나, 한 개의 루트 노드와 두 개의 서브 이진 트리를 가지는 유한한 노드의 집합
* 어떤 트리도 LCRS 통해 이진 트리로 변환 가능

<br/>

### Variants
* `Full` (포화) : 모든 레벨의 노드가 꽉 찬 이진 트리
* `Complete` (완전) : 레벨 $K-1$까지는 포화 이진 트리이며 레벨 $K$에서 왼쪽부터 노드가 채워진 이진 트리
* `Skewed` (편향) : 노드가 왼쪽 또는 오른쪽으로 편향되어 있는 트리

<br/>

### Binary Tree Representation
#### List Representation
* 인덱스가 1부터 시작하는 배열의 각 요소를 노드로 표현
* 인덱스가 $i$인 노드의 부모는 $i/2$, 왼쪽 자식은 $2i$, 오른쪽 자식은 $2i+1$

#### Linked Representation
* 각 노드를 구조체/클래스 객체로 표현
* 데이터와 왼쪽, 오른쪽 자식에 대한 포인터
```java
public class Node<E> {
    private E data;
    private Node<E> leftChild;
    private Node<E> rightChild;
}
```

<br/>

### Huffman Code
* 아스키 코드는 모든 문자에 대해 7비트 길이의 코드를 가짐
* 자주 사용되는 문자에 대해 더 짧은 코드를 할당해 메모리를 아끼자!
* 데이터를 압축하는 알고리즘. 네트워크를 통한 전송 등에 활용

#### algorithm
1. 사용 빈도 등을 가중치로 정렬
2. 가중치가 가장 작은 두 노드를 자식으로 가지는 새 트리 노드 구성. 새 노드의 가중치는 두 노드의 가중치의 합
3. 2를 하나의 트리가 구성될 때까지 반복

#### encode & decode
* 트리의 왼쪽 간선을 0, 오른쪽 간선을 1로 encode
* 각 리프 노드(알파벳) 의 코드는 루트로부터 도달하는 경로의 코드
* 어떤 코드도 다른 코드의 prefix가 되지 않음 
    * 모두 루트로부터 다른 경로로 리프 노드에 도달하기 때문
* 암호화된 코드를 받아 루트로부터 리프 노드에 도달하면 하나의 문자를 decode한 것

<br/>

### Binary Tree Traversal
현재 노드를 $V$, 왼쪽 서브트리를 $L$, 오른쪽 서브트리를 $R$이라고 할 때,
1. `preorder` : $V \rightarrow L \rightarrow R$
2. `inorder` : $L \rightarrow V \rightarrow R$
3. `postorder` : $L \rightarrow R \rightarrow V$
4. `level` : 깊이별 탐색

#### problems
* 중위 순회를 재귀 대신 스택을 이용한 iterative 구현
* 전위, 중위 > 후위, 중위, 후위 > 전위
* 전위, 중위 > 트리, 중위,후위 > 트리
* BST의 전위 순회 > 후위 순회
* 전위 순회를 이용한 트리의 복사
* 후위 순회를 이용한 두 트리의 일치 검사