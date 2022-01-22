
# logstash
- 데이터 처리 파이프라인 
- 데이터를 수집`(input)`하여 변환`(filter)`한 후 사용하는 저장소로 전달`(output)`

# logstash 설정 정보 
## logstash/config/logstash.yml
- `config.reload.automatic: true`로 설정해두면 logstash.conf 파일 수정 시 자동 reload

## logstash/pipeline/logstash.conf
- `input`, `filter`, `output` 정보 관리하는 곳
- ```
  input {
    tcp {
      port => 5000
        codec => json_lines
      }
  }

  ## Add your filters / logstash plugins configuration here
  filter {
  }

  output {
    stdout { }
    elasticsearch {
      hosts => "elasticsearch:9200"
      user => "elastic"
      password => "changeme"
      ecs_compatibility => disabled
    }
  }
  ```

## docker로 설치 및 실행 
### 설치 
- `docker pull docker.elastic.co/logstash/logstash:7.9.1`

### 실행 
- `docker run -d --link elasticsearch7:elasticsearch --name logstash7 docker.elastic.co/logstash/logstash:7.9.1`


