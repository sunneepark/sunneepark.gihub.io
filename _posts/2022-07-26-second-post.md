---
title: "ELK 도입기"
date: 2022-07-26
categories: jekyll update
---

# 0. structure


filebeat -> logstash -> elastic search -> grafana



# 1. filebeat
: 실시간 경량 로그 수집기 

- filebeat 파일 설치
  -   deb(filebeat-5.6.2-linux-x86_64.deb) 설치시 -e 옵션으로 적용되어있어서 로그를 남기지 않음.

```bash
mkdir -p /apps
cd /apps
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-5.6.2-linux-x86_64.tar.gz
tar xvfz filebeat-5.6.2-linux-x86_64.tar.gz
ln  -s filebeat-5.6.2-linux-x86_64 filebeat
cd filebeat
vi test_filebeat.yml
```



- test_filebeat.yml

```yaml
filebeat.prospectors:
#filebeat.inputs:
- type: filestream
  id: basic
  enabled: true
  encoding: utf8
  scan_frequency: 10s
  ignore_older: 5m
  max_bytes: 10485760
  tail_files: true
  paths:
    - /log/mongos.log

output.logstash:
  enabled: true
  hosts: ["localhost:10044"]
  compressio_level : 0
  timeout : 300
  ssl.enabled: false

logging.level: info
logging.to_stderr: false
logging.to_syslog: false
logging.to_files: true
logging.files:
  path: /apps/filebeat/log
  name: filebeat
  keepfiles: 7
  permissions: 0644
```

- filebeat 실행 
```
/filebeat/filebeat -c test_filebeat.yml &
```

# 2. log stash
: 데이터 파이프라인, 입력(inputs) -> 필터(filters) -> 출력(outputs) 으로 구성. in-memory(대부분) 이여서 빠르게 수행
 
- logstash 설치

```
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.6.2.tar.gz
tar xvfz logstash-7.6.2.tar.gz
```

- logstash/config/logstash-sample.conf
```
# Sample Logstash configuration for creating a simple
# Beats -> Logstash -> Elasticsearch pipeline.

#logstash 의 input 으로 Kafka를 사용하기도 함.
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
    #user => "elastic"
    #password => "changeme"
  }
}
```

- logstash 실행
```
./bin/logstash -f ./config/logstash-sample.conf
```

# 3. Elastic Search
: 데이터 처리 및 검색 엔진

- elastic search 구조
  <br>: 기본적인 sharding 구조

cluster 1 (node1[master] , node2, node3 ) <br>
cluster 2 (node4[master] , node5, node6) <br>
-> node1, node2 가 복제
-> cluster 마다 master node 선정, master node 는 인덱스의 메타 데이터, 샤드의 위치와 같은 cluster status 정보를 관리.<br>
  -> master node와 data node를 분리할 수도 없을 수도 있음. 분리하게 되면, 관리와 데이터처리가 각각 집중될 수 있음.

- keyword : document, index, shard
  - document : 단일 데이터 단위
  - index : document의 집합 (Indices), shard로 분리되고, 각 노드에 분산되어 저장
  - shard : 단일 검색 index, 각 shard 마다 n 개의 replica가 생김. 복제본과 원본은 다른 node에 저장.

1 index[5 documents] (shard 0, shard 1, shard 2, shard 3)

cluster 1 (node1 [master node], node2 [shard 0, shard 1] node3 [shard 0', shard 1']
cluster 2 (node3 [master node], node4 [shard 2, shard 3], node5 [shard 2', shard 3'])

- elastic search 설치
 
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.6.2-linux-x86_64.tar.gz.sha512

tar -xzf elasticsearch-7.6.2-linux-x86_64.tar.gz

ln -s elasticsearch-7.6.2 elasticsearch

cd elasticsearch

sudo /usr/sbin/sysctl -w vm.max_map_count=262144
```

- elasticseach/config/elasticsearch.yml
```
node.master: true

node.data : true

path.data: /home1/irteam/elasticsearch/data

path.logs: /home1/irteam/elasticsearch/logs

network.host: 0.0.0.0
http.port: 9200

cluster.initial_master_nodes: ["127.0.0.1"]
cluster.remote.connect: false
```



```bash
./bin/elasticsearch &

curl -XGET http://localhost:9200/_cat/indices

curl -XGET http://localhost:9200/_cat/health?v
```


http://[elasticsearch ip 주소]:9200 접속 시, 아래 처럼 나옴
```
{
  "name" : "...",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "sl3evSJdTeWfQGVvgiLR0g",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    ...
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

- 계정만들기
```bash
./bin/elasticsearch-setup-passwords interactive
elastic12 #$
curl --user elastic:elastic12#$ -X POST "http://127.0.0.1:9200/_security/user/test?pretty" -H 'Content-Type: application/json' -d'
{

  "password" : "navercorp",

  "roles" : [ "monitoring_user" ]
}
'
```

- 위 계정 생성이 xpack 등의 이유로 안될때, 
elasticsearch 계정 생성 (이름 :sunny, 비밀번호: sunny123)
```
curl --user elastic:changeme -X POST "http://localhost:9200/_security/user/sunny?pretty" -H 'Content-Type: application/json' -d'
{
  "password" : "sunny123",
  "roles" : [ "superuser" ],
  "full_name" : "sunny123"

}
'
```


- elastic 데이터 처리
api docs: https://esbook.kimjmin.net/04-data

  - 인덱스 생성
  ```
  curl -XPUT "http://localhost:9200/test_idx" -H 'Content-Type: application/json' -d'
  {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 5
    }
  }'
  {"acknowledged":true,"shards_acknowledged":true,"index":"test_idx"}
  ```

  - 도큐먼트 생성
  ```
  curl -XPUT http://localhost:9200/test_idx/_doc/1" -k -H 'Content-Type:application/json' -d'
  > {
  >   "name": "Jongmin Kim",
  >   "message": "안녕하세요 Elasticsearch"
  > }'
  {"_index":"test_idx","_type":"_doc","_id":"1","_version":1,"result":"created","_shards":{"total":6,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
  ```

  - 도큐먼트 조회
  ```
  curl -XGET "http://localhost:9200/test_idx/_doc/1"
  {"_index":"test_idx","_type":"_doc","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":
  {
    "name": "Jongmin Kim",
    "message": "안녕하세요 Elasticsearch"
  }
  }
  ```