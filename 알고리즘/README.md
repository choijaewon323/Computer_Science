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

## 그래프 및 그래프 순회

#### DFS
#### BFS
#### 강한 연결 요소 (Strongly Connected Components)
- 강하게 연결됨 : 유향 그래프에서 모든 정점쌍 v, w가 도달 가능한 것
- 강한 연결 요소 : 부분 그래프에서 모든 정점쌍 v, w가 도달 가능한 것
- Transpose Graph : 유향 그래프에서 모든 간선의 방향이 반대인 그래프
- 강한 연결 요소 찾는 알고리즘
```
1. DFS를 수행한다.
2. DFS를 수행하고 각각이 끝날 때마다 스택에 집어넣는다.
3. Transpose graph에 대해서 DFS를 수행한다. 이 때 시작점은 스택에서 pop하는 순서대로이다.
4. DFS가 끝날 때마다 저장한다.
```


## 그리디 알고리즘 (Greedy Algorithm)
- 순간의 선택을 최선이라고 생각함
- 최소 스패닝 트리, 두 정점의 최단 거리(일대다)에 사용

#### 최소 스패닝 트리 (Minimum Spanning Tree)
- 스패닝 트리 : 모든 정점이 연결되어있고, 모든 정점을 포함한 부분그래프
- 최소 스패닝 트리 : 스패닝 트리에서 최소의 가중치를 가지는 것

##### 1. 프림 알고리즘 (Prim's Algorithm)

##### 2. 크루스칼 알고리즘 (Kruskal's Algorithm)

## 최단 거리

#### 다익스트라 알고리즘 (Dijkstra's Algorithm)
- 그리디 알고리즘 이용
- 최악 시간 복잡도 : O(E + V log V)
- 일대다
#### 플로이드-워셜 알고리즘 (Floyd-Warshall Algorithm)
- 다이나믹 프로그래밍 이용
- 최악 시간 복잡도 : O(n^3)
- 다대다
