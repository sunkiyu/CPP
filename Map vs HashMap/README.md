# Map vs HashMap
* Map 특정 key로 데이터를 접근/관리   
* 키로 값에 접근하여 삽입/삭제 빠름    
* 이진탐색트리, Red Black Tree를 사용   
* 이진 탐색 트리는 O(log n) 속도, 더 빠른 탐색 원할 때 Hash Map 사용 O(1) => 배열로 접근하기 때문   
* Map,Set은 자료 정렬 보관, Hash Map은 자료 정렬하지 않음   
* STL 컨테이너에는 Hash Map 없고 따로 구현된 STL 라이브러리 사용해야함   
* 공식 컨테이너 못들어간 이유는 탐색 성능이 안정적이지 않음   
* map,set 삽입 삭제 탐색 모두 O(log n) 보장   
* list : 삽입, 삭제 O(1) 임의 원소 접근 O(n) 보장   
* vector: 맨끝 삽입, 삭제 O(1) 나머지 O(n), 임의 원소 접근 O(1) 보장   
* Hash Map의 경우 Hashing key를 사용해 데이터 접근하는데 Hashing key 생성방법은 여러가지다.   
* 생성된 키를 이용해 배열 인덱스를 사용해 접근하기 때문에 접근 속도가 O(1)이다.
* Hashing key가 겹쳐 충돌이 일어나면 안되기 때문에 Hashing key 생성 방법에 의해 성능 좌우된다.    
* Hashing key 값이 랜덤 분포되어야 가장 좋은 성능 발휘한다    


## Hash Map vs Map
* 자료  탐색에 Hashing 사용 vs 자료탐색에 이진 탐색 트리 사용(최근 Red-Black-Tree)       
* 탐색 속도 O(1) 이상 키값 분포에 따라 다름(랜덤한게 좋다.) vs 탐색 속도 O(log n) 보장   
* 내부 자료 정렬 안함 vs 내부 자료 정렬




