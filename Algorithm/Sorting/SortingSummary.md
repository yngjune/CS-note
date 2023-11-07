### further readings
* [timsort](https://www.drmaciver.com/2010/01/understanding-timsort-1adaptive-mergesort/)
* [quicksort video](https://www.youtube.com/watch?v=aMnn0Jq0J-E)
* [which sorting algo?](https://cs.stackexchange.com/questions/3/why-is-quicksort-better-than-other-sorting-algorithms-in-practice)

### 어떤 상황을 고려해야 할까?
* input distribution (uniform, almost sorted, almost in right place, many duplicates, ...)
* QS는 작은 배열에 대해서는 오버헤드에 의해 효율적이지 않음 => 작은 배열 (eg. k<=10)에 대해는 그냥 삽입정렬 등..
* disk 접근을 최소화해야 한다!

디스크에 접근해야 할 만큼 큰 데이터 정렬 -> random접근하는 QS보다 sequential인 merge

디스크 접근을 최소화하도록. 예시 - DBMS 많은 데이터 load필요시 잠시 인덱스를 drop하고 완료 후 다시 index build.

> hw 병렬 구현 & 시간 효율적 & 적은 게이트 사용
* bitonic / batcher odd-even mergesort
> not large num, unique vals
* counting, radix sort
> linked elem
* mergesort
> need stable
* mergesort
> small size data
* bitonic / batcher odd-even mergesort
> $\Theta(n)$ memory available
* almost in right place-> bubble, insertion
* contains large pieces of already sorted -> ada.merge/timsort
> $\Theta(logn)$ memory available
* only sequential access (eg. disk)=> merge (logn 메모리 버전도 가능하다고 함)
* multiple 동시 접근 => quicksort
> $\Theta(1)$ memory
* 정렬하려는 자료구조가 랜덤접근 허용 -> 힙정렬

### 정렬 알고리즘들
* bitonic sort
* batcher odd-even mergesort
* how to implement bottom-up in-place mergesort
* adaptive mergesort
* timsort
* introsort
* quicksort - approximated median or median of median

### Implementation hints for quicksort
Naive binary quicksort requires Θ(n)
 additional memory, however, it is relatively easy to reduce that down to Θ(log(n))
 by rewriting the last recursion call into a loop. Doing the same for k-ary quicksorts for k > 2 requires Θ(nlogk(k−1))
 space (according to the master theorem), so binary quicksort requires the least amount of memory, but I would be delighted to hear if anyone knows whether k-ary quicksort for k > 2 might be faster than binary quicksort on some real world setup.


### Implementation hints for mergesort
bottom-up mergesort is always faster than top-down mergesort, as it requires no recursion calls.

the very naive mergesort may be sped up by using a double buffer and switch the buffer instead of copying the data back from the temporal array after each step.

For many real-world data, adaptive mergesort is much faster than a fixed-size mergesort.

the merge algorithm can easily be parallelized by splitting the input data into k approximately same-sized parts. This will require k references into data, and it is a good thing to choose k such that all of k (or c*k for a small constant c >= 1) fit into the nearest memory hierarchy(usually L1 data cache). Choosing the smallest out of k elements the naive way(linear search) takes Θ(k)
 time, whereas building up a min-heap within those k elements and choosing the smallest one requires only amortized Θ(log(k))
 time (picking the minimum is Θ(1)
 of course, but we need to do a little maintenance as one element is removed and replaced by another one in each step).

The parallelized merge always requires Θ(n)
 memory regardless of k.

From what I have written, it is clear that quicksort often isn't the fastest algorithm, except when the following conditions all apply:

there are more than a "few" possible values

the underlying data structure is not linked

we do not need a stable order

data is big enough that the slight sub-optimal asymptotic run-time of a bitonic sorter or Batcher odd-even mergesort kicks in

the data isn't almost sorted and doesn't consist of bigger already sorted parts

we can access the data sequence simultaneously from multiple places

memory writes are particularly expensive (because that's mergesort's main disadvantage), so far as it slows down the algorithm beyond a quicksort's probable sub-optimal split. or we can only have Θ(log(n))
 additional memory, Θ(n)
 is too much (e.g. external storage)