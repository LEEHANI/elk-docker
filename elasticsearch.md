

- 한글검색 빈약 
- 동의어나 유의어를 활용한 검색 가능 
-  

# 기본 개념 

## 클러스터 
- 하나 이상의 노드가 모인 것. 전체 데이터를 저장하고 모든 노드를 통합 색인화 및 검색 기능을 제공한다. 
- 클러스터는 고유한 이름으로 식별된다. 
- 여러 노드를 하나의 클러스터로 묶으려면 `cluster.name`을 동일하게 설정해야한다. 

## 노드 
- 클러스터에 포함된 단일 서버로, 데이터를 저장하고 클러스터의 색인화 및 검색 기능에 참여한다. 
- 노드는 클러스터처럼 이름으로 식별된다. 
- `클라이언트와 통신`을 위해 `9200번 포트` 사용. `노드간의 통신`을 위해서는 `9300번 포트` 사용. 
- 클러스터는 마스터 노드를 갖고있는데, 마스터 노드는 인덱스 생성, 삭제, 노드 추적, 노드에 할당할 샤드 결정 과 같은 클러스터 전체 작업을 담당한다.  

## Discovery
- 새 노드를 실행하거나 마스터 노드가 실패했을 때, 마스터 디스커버리 프로세스가 실행된다. 
- discovery.seed_hosts 목록에서 시드 주소들을 보고 다음과 같이 실행된다. 
- 1. 각 노드는 각 주소에 연결하고, 연결된 노드를 식별하고 마스터 자격이 있는지 확인하여 시드 주소를 조사한다. 
- 2. 성공하면 모든 마스터 적격 피어 목옥을 원격 노드와 공유하고 원격 노드는 피어와 차례로 응답한다. 

## 인덱스. == 인디시즈(indices) 
- ```
  관계형 DB                  elastic search
  데이터베이스(database)       인덱스(index)
  테이블(table)              타입(type)         //7.x 버전 이상에서는 타입이 삭제 
  행(row)                   도큐먼트(document)
  열(column)                필드(field)
  스키마(schema)             매핑(mapping)
  ```

## 도큐먼트(document)
- 데이터가 저장되는 최소 단위. 
- 기본적으로 JSON 포맷으로 데이터가 저장.

## 매핑
- 매핑은 `필드의 구조`와 `제약조건에 대한 명세`를 말한다. RDB에서는 `스키마`와 같다. 

## 샤드 & 리플리카 
- 인덱스 내부에는 색인된 데이터들이 있다. 이 데이터가 단일 노드에 저장할 경우, 단일 노드의 디스크에서 수용하지 못하거나 단일 노드에서 검색 요청 처리 시 속도가 너무 느려질 수 있다. 
- 엘라스틱서치는 이러한 문제를 해결하고자 색인을 샤드라는 조각으로 분할하여 저장한다. 
- 샤드는 `프라이머리 샤드(원본 샤드)`와 `레플리카 샤드(원본 샤드 복사본)`가 있다. 
- 샤드가 있으므로 수평 분할/ 확장이 가능하고, 여러 샤드에 분산 배치하고 병렬화함으로써 성능/처리량을 늘릴 수 있다. 
- 오류가 발생하더라도 리플리카 샤드로 장애 극복이 가능하다. 
- 클러스터에 노드를 추가하면 샤드들이 노드에 분산되어 저장된다. 

## elasticsearch.yml 
- elasticsearch 실행 환경에 대한 설정들은 `config/elasticsearch.yml`에서 설정한다. 
- `cluester.name: <클러스터명>`: 노드들은 클러스터명이 같으면 같은 클러스터로 묶임.  
- `node.name: <노드명>`
- `path.data: <경로>`
- `path.logs: <경로>`
- `bootstrap.memory_lock: true`: 엘라스틱서치가 사용중인 힙메모리 영역을 다른 자바 프로그램이 간섭 못하도록 미리 점유하는 설정 

# elasticsearch index APIs
- https://www.elastic.co/guide/en/elasticsearch/reference/7.16/indices.html

## create index 
- `PUT my-index`
  + 필드 매핑 가능 
  + 인덱스 별칭(_alias) 가능 
-  ```
   PUT my-index/_doc/1
   {
      "name": "my-index", 
      "age": 791
   }
   ```

## delete index 
- `DELETE my-index`

## exists
- `HEAD my-index`. 
- 데이터, 인덱스, 별칭이 존재하는 지 확인 

## get index 
- `GET my-index`. 인덱스 정보를 반환 
- response
  ```
  {
  "my_index4" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "age" : {
          "type" : "long"
        },
        "name" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
              }
            }
          }
        } 
      },
      "settings" : {
        "index" : {
          "creation_date" : "1641385307714",
          "number_of_shards" : "1",
          "number_of_replicas" : "1",
          "uuid" : "Qn90g4DNSFuClQX0PfPfHg",
          "version" : {
            "created" : "7090199"
          },
          "provided_name" : "my_index4"
        }
      }
    }
  } 
  ```
- 일부분 세팅만 보고싶으면 `GET my-index/_settings`, `GET my-index/_mappings` 등으로 조회 가능. 

# docker로 설치 
## elasticsearch 
### 설치 
`docker pull docker.elastic.co/elasticsearch/elasticsearch:7.9.1`
### 실행 
`docker run -d -p 9200:9200 -p 9300:9300 -e --name elasticsearch7 docker.elastic.co/elasticsearch/elasticsearch:7.9.1`


