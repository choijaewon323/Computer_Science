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
- A,B 시퀀스의 첫번째 요소를 비교, 더 작은걸 C의 첫번째에
- 만약 A 시퀀스였다면, A 시퀀스 나머지와 B의 첫번째 요소를 비교
- 반복
```
merge(A, B, C)
if (A is empty)
	rest of C = rest of B
else if (B is empty)
	rest of C = rest of A
else if (first of A ≤ first of B)
	first of C = first of A
	merge (rest of A, B, rest of C)
else
	first of C = first of B
	merge (A, rest of B, rest of C)
return
```
```
void mergeSort(Element[] E, int first, int last)
if (first < last)
	int mid = (first+last)/2;
	mergeSort(E, first, mid);
	mergeSort(E, mid+1, last);
	merge(E, first, mid, last);
return;
```
- 최악 시간 복잡도 : O(n log n)


#### 힙 정렬 (Heap Sort)
- Heap
	- 완전 이진 트리
	- 최대 힙의 경우 모든 노드가 자식들보다 값이 크거나 같아야함
- 힙 소트
	- 힙에서 하나씩 빼면 정렬 완성
```
heapSort(E, n) // Outline
construct H from E, the set of n elements to be sorted
for (i = n; i ≥ 1; i--)
	curMax = getMax(H)
	deleteMax(H);
	E[i] = curMax;
deteleMax(H) // Outline
copy the rightmost element of the lowest level of H into K
delete the rightmost element on the lowest level of H
fixHeap(H, K);
```
```
fixHeap(H, K) // Outline
if (H is a leaf)
	insert K in root(H);
else
	set largerSubHeap to leftSubtree(H) or rightSubtree(H), whichever has larger key at its root. This involves one key comparison. 
	if (K.key ≥ root(largerSubHeap.key)
		insert K in root(H);
	else
		insert root(largerSubHeap) in root(H);
		fixHeap(largerSubHeap, K);
return;
```
- fixHeap의 경우 Worst로 2 log n 연산 수행



#### 기수 정렬 (Radix Sort)
- key를 자릿수로 나눠서 분류 – 버켓으로 분류
- 버켓들을 정렬
- 버켓들을 리스트로 구현
```
List radixSort (List L, int radix, int numFields)
List[] buckets = new List[radix];
int field; // field number within the key
List newL;
newL = L;
For (field = 0; field < numFields; field++)
	Initialize buckets array to empty lists.
	distribute (newL, buckets, radix, field);
	newL = combine (buckets, radix);
return newL;
```
```
void distribute (List L, List[] buckets, int radix, int field) 
//distribute keys into buckets
List remL;
remL = L;
while (remL != nil)
	Element K = first (remL);
	int b = maskShift (field, radix, K.key);
		// maskShift(f, r, key) selects field f (counting from the right) of key, 
		// based on radix r. the result, b, is the range 0 … radix –1,
		// and is the bucket number for K
	buckets[b] = cons(K, buckets[b]); // construct list
	remL = rest (remL);
return
```
```
List combine (List[] buckets, int radix)
// Combine linked lists in all buckets into one list L
int b; // bucket number
List L, remBucket;
L = nil;
for (b = radix-1; b≥0; b--)
	remBucket = buckets[b];
	while (remBucket != nil)
		key K = first (remBucket);
		L = cons (K, L);
		remBucket = rest (remBucket);
return L;
```
- 수행시간이 자릿수에 따라 결정됨

## 이진 탐색 트리 (Bineary Search Tree)
- Binary Search Tree
	- 모든 왼쪽 서브트리의 값들은 현재 노드보다 작은 값들
	- 모든 오른쪽 서브트리의 값들은 현재 노드보다 큰 값들
	- inorder로 순회하면 정렬된 리스트 얻음
	- 균형된 트리 : O(log n)
	- 불균형 트리 : O(n) – 한쪽으로 쭉 이어진 이진 탐색 트리
- Red-Black Tree
	- Property
		1. Root Property : 루트는 검정
		2. External Property : 모든 리프 노드는 검정
		3. Internal Property : 레드 노드의 자식들은 검정
		4. Depth Property : 모든 리프 노드는 같은 검정 depth – 루트까지의 검정 노드 수 같음
	- Insertion : 이진 탐색 수행 후 리턴된 위치에 레드 노드 삽입
	- Double Red 문제 발생
- Double Red
	- 새로 삽입된 노드 z, 그 노드의 부모 v, v의 형제 노드 w를 가정하면
	- w가 블랙인 경우 : Restructuring
	- w가 레드인 경우 : Recoloring
- Restructuring
	- z, v, v의 부모까지 총 세 노드의 구조를 바꿔 속성들을 모두 만족하도록
- Recoloring
	- v와 w를 black으로, v와 w의 부모 노드는 red로
	- 이를 루트까지 반복 (propagation)
- 레드 블랙 트리의 삽입 연산은 O(log n) 시간


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
- 매 순간 최선의 선택을 고르는 것 : short-term
- 틀릴 수도 있는 가능성 존재
- 정확한 것으로 검증된 문제
	- 최소 스패닝 트리
	- 두 정점 사이의 최단 거리

#### 최소 스패닝 트리 (Minimum Spanning Tree)
- 스패닝 트리 : 모든 정점이 연결되어있고, 모든 정점을 포함한 부분그래프
- 최소 스패닝 트리 : 스패닝 트리에서 최소의 가중치를 가지는 것

##### 1. 프림 알고리즘 (Prim's Algorithm)
- 매 반복마다 가장 짧은 인접 정점 선택
- 선택된 노드들 : tree
- 아직 선택 안됐지만 인접 : fringe
- 나머지 : unseen
```
PrimMST(G,n) // OUTLINE
	Initialize all vertices as unseen.
	Select an arbitrary vertex s to start the tree; reclassify it as tree.
	Reclassify all vertices adjacent to s as fringe.
	While there are fringe vertices;
		Select an edge of minimum weight between
			 a tree vertex t  and a fringe vertex v;
		Reclassify v as tree; add edge tv to the tree;
		Reclassify all unseen vertices adjacent to v as fringe.
```

##### 2. 크루스칼 알고리즘 (Kruskal's Algorithm)
- 항상 최소 가중치인 간선 선택
- 다른 셋일 경우 합침
- disjoint set 사용 (유니온 파인드)
```
KruskalMST(G,n)	// outline
	R = E // R is remaining edges
	F = null // F is forest edges
	while (R is not empty)
		Remove the lightest (shortest) edge, vw, from R;
		if (vw does not make a cycle in F)
			Add vw to F;
	return F ;
```

## 최단 거리

#### 다익스트라 알고리즘 (Dijkstra's Algorithm)
- 그리디 알고리즘, bfs 이용
- 최악 시간 복잡도 : O(E + V log V)
- 한 정점부터 나머지 정점까지의 최단 경로 (1 : n)
- 가중치가 음수인 경우는 사용 불가 – 그 경우 벨만 포드
```
dijkstraSSSP(G,n) // OUTLINE
    Initialize all vertices as unseen.
    Start the tree with the specified source vertex s; reclassify it as tree;
    define d(s,s) = 0.
    Reclassify all vertices adjacent to s as fringe.
    while there are fringe vertices:
        Select an edge between a tree vertex t and a fringe vertex v such that (d(s,t)+W(tv)) is minimum;
        Reclassify v as tree; add edge tv to the tree;
        define d(s,v) = (d(s,t)+W(tv)).
        Reclassify all unseen vertices adjacent to v as fringe.
```

#### 플로이드-워셜 알고리즘 (Floyd-Warshall Algorithm)
##### Transitive Closure
- 방향 그래프에서, A -> B 이고 B -> C이면 A -> C 인 간선을 포함시킴
##### Floyd-Warshall Algorithm
- 최악 시간 복잡도 : O(n^3)
- transitive closure와 dynamic programming 사용
- 매 반복마다 정점 하나씩 공개 – 공개된 정점을 경유하는게 빠르면 수정
- 모든 쌍의 최단 거리 구하기 (N : N)
```
Algorithm FloydWarshall(G)
	for all  vertex pairs (i,j) 
	if  i = j
		D0[i,i] <- 0
	else if (i,j) is an edge in G
		D0[i,j] <- weight of edge (i,j)
	else
		D0[i,j] <- + infinity
 for k <- 1 to n do    
	for i <- 1 to n do    
	   for j <- 1 to n do    
		  Dk[i,j] <- min{Dk-1[i,j], Dk-1[i,k]+Dk-1[k,j]}
 return Dn
```

## 다이나믹 프로그래밍 (Dynamic Programming)

#### 피보나치

#### 행렬 곱셈 


## 문자열 매칭 (String Matching)

#### 브루트 포스 알고리즘 (Brute-force Algorithm)
- T, P : 문자열, n, m : 각 문자열의 길이
```
Algorithm BruteForce(T, P)
for i <- 0 to n – m
	j <- 0
	while j < m && T[i + j] = P[j]
		j <- j + 1
	if j == m
		return i
	else
		break
return -1
```
- 최악 시간 복잡도 : O(nm)


#### KMP 알고리즘 (Knuth-Morris-Pratt Algorithm)
- 브루트 포스와 비슷하지만, 미스매치가 발생했을 때 조금이라도 일치하는 부분부터 시작
- prefix와 suffix가 일치하는 부분만큼
- failure function : 배열 형태로 존재, 미스매치가 발생하는 인덱스에 대해 다음 match 시 시작 가능한 인덱스 저장
```
Algorithm KMPMatch(T, P)
	F <- failureFunction(P)
	i <- 0
	j <- 0
	while i < n
	if T[i] = P[j]
		if  j = m - 1
			return  i - j { match }
		else
			i <- i + 1
			j <- j + 1
	else
		if  j > 0
			j <- F[j - 1]
		else
			i <- i + 1
return  -1 { no match }
```
```
Algorithm failureFunction(P)
	F[0] <- 0
	i <- 1
	j <- 0
	while i < m
	if P[i] = P[j]
		{we have matched j + 1 chars}
		F[i] <-  j + 1
		i <- i + 1
		j <- j + 1
	else if  j > 0 then
		{use failure function to shift P}
		j <- F[j - 1]
	else
		F[i] <- 0 { no match }
		i <- i + 1
```
- 최악 : O(m + n)	


#### 보이어-무어 알고리즘 (Boyer-Moore Algorithm)
- Last-Occurrence Function : 검색할 패턴 p 안에서 해당 문자가 마지막에 나오는 인덱스 저장
```
Algorithm BoyerMooreMatch(T, P, S)
	L <- lastOccurenceFunction(P, S )
	i <- m - 1
	j <- m - 1
	repeat 
	if T[i] = P[j]
		if  j = 0
			return  i  { match at i }
		else
			i <- i - 1
			j <- j - 1
	else
		{ character-jump }
		l <- L[T[i]]	
		i <- i + m – min(j, 1 + l)
		j <- m - 1
until  i > n - 1
return  -1 { no match }
```
- 최악의 경우 O(nm + s) 만큼 걸리지만, 평문 영어 텍스트의 경우 브루트 포스보다 빠름
- 이미지와 같이 패턴이 일정하지 않은 경우 느림


## NP-Complete 문제

#### P 클래스와 NP 클래스

#### NP-Complete 클래스

#### NP-Complete 문제
