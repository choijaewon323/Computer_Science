# 알고리즘 정리

## 정렬 (Sorting)

#### 삽입 정렬 (Insertion Sort) : 각 원소를 적절한 위치에 삽입
- 입력 : n개의 원소가 들어있는 배열
- 출력 : 오름차순으로 정렬된 배열
```
void insertionSort(Element[] E, int n)
	int xindex;
	for (xindex = 1; xindex < n; xindex++)
		Element current = E[xindex];
		key x = current.key;
		int xLoc = shiftVacRec(E, xindex, x);
		E[xLoc] = current;
	return;
```
```
int shiftVacRec(Element[] E, int vacant, Key x)
	int xLoc;
	if (vacant == 0)
		xLoc = vacant;
	else if (E[vacant-1].key ≤ x)
		xLoc = vacant;
	else
		E[vacant] = E[vacant-1];
		xLoc = shiftVacRec(E, vacant-1, x);
	return xLoc;
```
- 최악 시간 복잡도 : O(n^2)
- 평균 시간 복잡도 : n ^ 2 / 4

#### 퀵 소트 (Quick-Sort)
- 분할 정복 (Divide and Conquer)
  1. 문제를 작은 부분 문제로 나눔(divide)
  2. 작은 문제를 재귀적으로 해결(conquer)
  3. 문제들을 합침(combine)
  
```
solve(I)
     n = size(I)
     if (n ≤ smallsize)
     	solution = directlySolve(I);
     else
     	divide I into I1, …, Ik.
     	for each i in {1, …, k}
     		Si = solve(Ii);
     	solution = combine(S1, …, Sk);
     return solution;
```

- 퀵 소트
	- 분할 정복 기법을 사용한 랜덤 소팅 알고리즘
	  1. Divide : 랜덤한 원소(pivot) x 선택 후 L(less), E(equal), G(greater) 그룹으로 나눔(partition)
	  2. Conquer : L과 G를 정렬
	  3. Combine : L, E, G를 합침

```
Algorithm partition(S, p)
	L, E, G <- empty sequences
	x <- S.remove(p) 
	while !S.isEmpty()
		y <- S.remove(S.first())
		if y < x
			L.insertLast(y)
		else if y = x
		 	E.insertLast(y)
		else { y > x }
			G.insertLast(y)
	return L, E, G
```

    - 입력 : 시퀀스 S, pivot p
    - 출력 : 서브시퀀스 L, E, G
    - partition 단계에서는 O(n) 시간이 걸림
    - 퀵 소트의 최악 시간 복잡도 : O(n^2) <- pivot이 계속 최소거나 최대인 원소만 고를 때

- 제자리 퀵 소트

```
Algorithm inPlaceQuickSort(S, l, r)
	if l >= r
		return
	i <- a random integer between l and r 
	x <- S.elemAtRank(i) 
	(h, k) <- inPlacePartition(x)
	inPlaceQuickSort(S, l, h - 1)
	inPlaceQuickSort(S, k + 1, r)
```
    - 입력 : 시퀀스 S, 인덱스 l(left), r(right)
    - 출력 : l과 r 범위가 오름차순으로 정렬된 시퀀스 S

#### 병합 정렬 (Merge Sort)
#### 힙 정렬 (Heap Sort)
#### 기수 정렬 (Radix Sort)
