---
title: "Welcome to Jekyll!"
date: 2022-02-19
categories: jekyll update
---

# filebeat 설치

deb(filebeat-5.6.2-linux-x86_64.deb) 설치시 -e 옵션으로 적용되어있어서 로그를 남기지 않음.

```bash
mkdir -p db/apps
cd db/apps
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-5.6.2-linux-x86_64.tar.gz
tar xvfz filebeat-5.6.2-linux-x86_64.tar.gz
ln  -s filebeat-5.6.2-linux-x86_64 filebeat
cd filebeat
vi test_filebeat.yml
```



- test_filebeat.yml

```yaml
filebeat.inputs:
- type: log
  enabled: true
  backoff: 1s
  max_backoff: 1s
  paths:
    - /home1/irteam/db/log/test.log
filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false
setup.template.settings:
  index.number_of_shards: 1
output.logstash:
  hosts: ["localhost:5044"]
processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
logging.level: info
logging.to_stderr: false
logging.to_syslog: false
logging.to_files: true
logging.files:
  path: /home1/irteam/db/apps/filebeat/log
  name: filebeat
  keepfiles: 7
  permissions: 0644
```

/home1/irteam/db/apps/filebeat/filebeat -c test_filebeat.yml &

<br>


# Elastic Search 설치

 
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.6.2-linux-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.6.2-linux-x86_64.tar.gz

 ln -s elasticsearch-7.6.2 elasticsearch

cd elasticsearch-7.6.2/



sudo /usr/sbin/sysctl -w vm.max_map_count=262144
```

```
cluster.name: mongodb_log

node.master: true

node.data : true

path.data: /home1/irteam/test/data
path.logs: /home1/irteam/test/logs

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


# logstash 띄우기

```
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.6.2.tar.gz
tar xvfz logstash-7.6.2.tar.gz
```


# 구조

filebeat -> logstash -> elastic search -> grafana