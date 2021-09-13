# RDBGuide
관계형 데이터베이스에 대한 공부

TODO : B+ tree 이어서 작성하기
## 정규화
  <details>
  <summary>
  제 1 정규화
  </summary>
  <br>

  * Atomic columns 
  * 한개의 컬럼에 1개의 값만 들어가야 한다.
  * 컬럼을 더 만들어 넣는다.
  </details>
  <br>

  <details>
  <summary>
  제 2 정규화
  </summary>
  <br>

  * No partial dependencies
  * 부분 종속성이 없어야 한다.
  * 테이블의 특정 부분들이 집합이 되어 PK에 의존하면 안된다.
  * 집합을 따로 테이블을 만들어 넣는다.
  </details>
  <br>

  <details>
  <summary>
  제 3 정규화
  </summary>
  <br>
  
  * No transitive dependencies
  * 이행 종속성이 없어야 한다.
  * 테이블의 특정 부분들이 내부 컬럼값에 의존하면 안된다.
  * 의존 부분들을 따로 테이블을 만들어 넣는다.
  </details>
  <br>

## Join
  <details>
  <summary>
  Inner Join
  </summary>
  <br>

  * 내부 조인은 조인되는 컬럼의 짝이 없을 경우 제외된다.
  * 소개팅과도 같다. 짝이 없으면? 나가리... 나는 인싸 조인이라고 부른다.
  * ![](img/inner_join.PNG)
  * SELECT * FROM table_a JOIN table_b ON a.id = b.a_id;
  </details>
  <br>

  <details>
  <summary>
  Outer Join
  </summary>
  <br>
  
  * 외부 조인은 조인되는 컬럼의 짝이 없어도 모두 포함된다.
  * Join 되는 컬럼이 겹치지 않는다면 null 로 들어간다.   
    ![](img/outer_join.PNG)   
  * SELECT * FROM table_a LEFT OUTER JOIN table_b ON a.id = b.a_id   
    UNION   
    SELECT * FROM table_a RIGHT OUTER JOIN table_b ON a.id = b.a_id;
  </details>
  <br>

  <details>
  <summary>
  Left / Right join
  </summary>
  <br>
  
  ![](img/left_right_join.PNG)
  left / right join 은 해당 조인이 포함시킬 부분집합을 예기합니다.

  Left inner join 이라는 개념은 없습니다.   
  심지어 left outer join 이라는 개념은 이미 left join 안에 내포되어 있습니다.   

  위 그림의 Table A 안의 모든 부분들을 포함해야 한다는 조건 자체가 outer 의 개념에 해당되기 때문입니다. 
      
  </details>
  <br>
    
## [Indexing](https://www.youtube.com/watch?v=HubezKbFL7E)
  <details>
  <summary>
  Clustered index
  </summary>
  <br>

  테이블이 생성될 때 pk 를 기준으로 나열된 Balanced+ Tree 가 생깁니다.   
  이 B+ Tree 를 사용하는 index 가 Clustered index 이라고 합니다.   

  ![](img/clustered_index.PNG)   

  여기에서 leaf node 들은 모두 data Rows 라고 나오는데 이는 Data 의 정보를 I/O 할 위치정보를 담고 있습니다.   

  ![](img/clustered_index2.PNG)   

  다음은 WHERE id = 1120 가 Clustered index B+ tree 를 타는 과정입니다.   

  </details>
  <br>  

  <details>
  <summary>
  Non-clustered index
  </summary>
  <br>
  
  PK 가 아닌 다른 컬럼에 인덱싱을 하신다면 이는 모두 Non-clustered index 에 해당됩니다.      
  이 인덱스의 동작 원리는 B+ tree 이며 해당 인덱스의 값을 바탕으로 정렬됩니다.   

  ![](img/non_clustered_index.png)   

  Row Locators 이라고 나온 값들은 실제 데이터의 PK 값을 저장하고 있습니다.   
  그래서 Non-clustered index 를 통과하고 그 이후에 Clustered index 를 통과하게 됩니다.   
  </details>
  <br>

  <details>
  <summary>
  B+ Tree
  </summary>
  <br>

  B+ Tree 는 leaf node 들이 doubly linked list 의 형태가 되어 있는 balanced tree 입니다.   
  RDB 는 이 B+ tree 를 통해 Clustered / Non-clustered index 를 만듭니다.   
  B Tree 의 설질을 갖고 있어 특정 노드가 해당 크기를 넘어설 경우 위로 보내고, 분산시키며 balance 를 유지시킵니다.
  </details>
  <br>

  <details>
  <summary>
  explain
  </summary>
  <br>
  
  [이분만큼 index 튜닝에 대하여 실질적으로 실험하며 보여준 동영상은 없었습니다.](https://www.youtube.com/watch?v=HubezKbFL7E&t=557s)    

  <br>
  
  EXPLAIN 구문을 사용하신다면 특정한 쿼리의 인덱싱을 벤치마킹할 수 있습니다.   

  ```sql
  EXPLAIN SELECT * FROM user WHERE id = 1
  ```

  결과   

  |구분|내용|
  |---|---|
  |id|SELECT 절마다 부여된 번호|
  |table|참조하는 테이블|
  |select_type|사용된 SELECT 절의 종류|
  |type|조회하는 전략, 방법|
  |possible_keys|조회에 사용이 가능한 인덱스 리스트
  |key|실제로 사용되는 인덱스|
  |key_len|실제로 사용되는 인덱스의 길이|
  |ref|실제로 사용되는 인덱스 이전의 선행 테이블의 컬럼|
  |rows|읽기 위해 통과되는 row 의 개수|
  |extra|추가정보| 

  type

  |구분|내용|
  |---|---|
  |const|단일한 테이블에서 최대 1개의 단일한 결과가 나올 경우|
  |eq_ref|join 된 테이블에서 최대 1개의 단일한 결과가 나올 경우|
  |ref/range|range 의 시작 점을 찾고 linked-list 를 진행하며 range 가 끝나는 부분을 찾는 경우|
  |index|인덱스를 사용하여 제일 첫 leaf node 에서 linked-list 진행하며 찾을 때까지 나가는 경우|
  |all|full table scan 으로 모든 컬럼을 받아 찾는 경우|

  이 결과에서 유심하게 보셔야 할 부분은 `type`, `possible_keys`, `key`, `rows` 입니다.

  `type` 에서   
  type `const`, `eq_ref` 의 경우 더 빨라질 여지는 없습니다.   
  단일한 값을 찾는 인덱스를 사용하였기 때문입니다.   
  
  `ref`/`range` 가 나왔을 경우 쿼리에서 BETWEEN 구문같은 범위를 지정했을 경우 의도한
  대로 인덱싱이 진행되었습니다.

  `index`/`all` 이 나왔을 경우 index tree 나 table 을 전수조사하였다는 의미임으로
  개선의 여지가 있습니다.   

  `possible_keys`의 경우   
  인덱스를 사용할 수 있지만 어떠한 이유로 인덱스를 사용하지 않는 것이 더 빠르다고
  mysql 이 인식할 수 있습니다.   
  예로 DISK I/O 가 개별적으로 이뤄지기보다 batch 단위로 이뤄지는 것이 더 좋다고 판단되는 경우 
  인덱스가 사용되지 않고 possible_keys 에 위치할 수 있습니다.   

  `key` 를 통해서 자신의 인덱스가 실제로 사용되는지 확인하시면 됩니다.   

  `row` 를 통해 어느정도의 row 를 통과해야 하는지 인덱스의 성능을 확인할 수 있습니다.
  </details>
  <br>
