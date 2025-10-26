# Install cluster
* https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-from-archive-on-linux-macos

## 1. First node
* port= 9200, 9300

Config file `elasticsearch.yml`
```
cluster.name: demo-cluster
node.name: master-node-1
network.host: 0.0.0.0
http.port: 9200
transport.port: 9300
discovery.seed_hosts: ["master-node-1"]
cluster.initial_master_nodes: ["master-node-1"]
node.roles: [ "master", "data", "ingest" ]
```

Start server
```
export ES_HOME=/Users/somkiatpuisungnoen/data/slide/elk/elk-2025/software/elasticsearch-9.2.0-master
cd $ES_HOME
./bin/elasticsearch
```

Check
```
export ELASTIC_PASSWORD="vT*2WBHG+TGonr9YdzST"
curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200
```

Get node in cluster
```
curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200/_cat/nodes
```

Generate enrollment token for other nodes
```
./bin/elasticsearch-create-enrollment-token -s node
```

## 2. Second node
* port= 9202, 9302

Config file `elasticsearch.yml`
```
cluster.name: demo-cluster
node.name: data-node-1
node.roles: [ "data", "ingest" ]
```

Start server
```
export ES_HOME=/Users/somkiatpuisungnoen/data/slide/elk/elk-2025/software/elasticsearch-9.2.0-data
cd $ES_HOME

./bin/elasticsearch --enrollment-token <enrollment-token>
```

Check nodes in cluster
```
curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200/_cat/nodes

curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9201/_cat/nodes

curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9201/_cluster/health?pretty
```

## 3. Kibana

Generate enrollment token for Kibana
```
bin/elasticsearch-create-enrollment-token -s kibana --url "https://127.0.0.1:9200"
```

Setup kibana with enrollment token
```
./bin/kibana-setup --enrollment-token <your-enrollment-token>
```

Start kibana
```
bin/kibana
```

Go to kibana
* http://localhost:5601/
