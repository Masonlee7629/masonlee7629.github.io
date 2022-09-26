---
title : "코드스테이츠 학습일지 - 2022.09.26"

categories:
  - CodeStates
excerpt: "자료구조/알고리즘 - Tree traversal, BFS/DFS"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-09-26
last_modified_at: 2022-09-27
---

## **Today I Learned**
📚오늘의 체크리스트
- [x] Tree traversal
- [x] BFS / DFS


---

## 자료구조
### Tree traversal
특정 목적을 위해 트리의 모든 노드를 한 번씩 방문하는 것을 트리 순회(Tree traversal)라고 한다.


계층적 구조라는 특징을 가진 트리 구조의 모든 노드를 순회하는 방법은 크게 세가지가 있다.
1. 전위 순회(preorder traverse)
2. 중위 순회(inorder traverse)
3. 후위 순회(postorder traverse)

트리 순회에서 주의할 점으로 노드를 순차적으로 조회할 때는 항상 된쪽에서 오른쪽의 순서로 조회한다.

#### 트리 순회의 순서
![image](https://user-images.githubusercontent.com/105192204/192313693-eac4ef8f-c883-4f7a-aba8-9428ca7a88c2.png)

1. 전위 순회 : F, B, A, D, C, E, G, I, H (root, left, right)
2. 중위 순회 : A, B, C, D, E, F, G, H, I (left, root, right)
3. 후위 순회 : A, C, E, D, B, H, I, G, F (left, right, root)

### BFS / DFS
그래프의 탐색 중 가장 대표적인 두 가지 방법이다.<br>
그래프의 탐색은 하나의 정점에서 시작하여 그래프의 모든 정점들을 한 번씩 방문(탐색)하는 것이 목적이다.<br>
BFS와 DFS는 경로를 탐색할 때 탐색의 순서에서 차이를 보인다.

#### BFS(Breathed First Search)
BFS는 너비 우선 탐색이라고 하며 주로 두 정점 사이의 최단 경로를 찾을 때 사용한다.<br>
루트 노드(또는 다른 임의의 노드)에서 시작하여 인접한 노드를 먼저 탐색하는 방법이다.<br>
시작 정점에서 가까운 정점을 먼저 방문하고 멀리 떨어져 있는 정점을 나중에 방문하는 순회 방법이다.<br>
큐를 이용해서 구현한다.

![image](https://user-images.githubusercontent.com/105192204/192317738-353a0346-08f8-4811-b288-bbbe76cd6f8b.png)

#### DFS(Depth First Search)
DFS는 깊이 우선 탐색이라고 하며 한 정점에서 시작해서 다음 경로로 넘어가기 전에 해당 경로를 완벽하게 탐색할 때 사용한다.<br>
루트 노드(또는 다른 임의의 노드)에서 시작하여 다음 분기로 넘어가기 전에 해당 분기를 완벽하게 탐색하는 방법이다.<br>
주로 모든 노드를 방문해야할 때 사용하며 BFS에 비해 간단하지만 검색 속도는 느리다.<br>
스택 또는 재귀함수를 이용해서 구현한다.

![image](https://user-images.githubusercontent.com/105192204/192320274-0b91a8c7-523f-4478-829a-675a3ddf7e94.png)


---

아쉬운 하루다.<br>
나름대로 열심히 했지만 노력에 비해서 정확히 알고 사용하지는 못하고 있다.<br>
오늘을 포함하여 부트캠프에 참여했던 날들을 되짚어보고 부족한 부분에 대해 반성하자.<br>
성장의 원동력으로 더 집중하고 몰입하자.
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---