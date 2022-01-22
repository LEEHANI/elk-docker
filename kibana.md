
# Kibana
- elasticsearch 데이터 시각화 도구 툴. 데이터 조회 및 집계가 가능하다 
- `localhost:5601/` 웹브라우저 접속


## docker로 설치 및 실행
- kibana는 elasticsearch와 같은 버전으로 설치해야함. 
### 설치 
`docker pull docker.elastic.co/kibana/kibana:7.9.1`
### 실행 
`docker run -d --link elasticsearch7:elasticsearch -p 5601:5601 --name kibana7 docker.elastic.co/kibana/kibana:7.9.1`
