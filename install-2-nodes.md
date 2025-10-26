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


## 4. Logstash
* https://www.elastic.co/docs/reference/logstash/installing-logstash
* https://www.elastic.co/docs/reference/logstash/first-event
* https://www.elastic.co/docs/reference/logstash/advanced-pipeline

Create first pipeline = hello-pipeline.conf
```
input {
    stdin {}
}
# The filter part of this file is commented out to indicate that it is
# optional.
# filter {
#
# }

output {
    stdout { codec => rubydebug }
}
```

Start logstash
```
bin/logstash -f hello-pipeline.conf 
```

Health check
* http://localhost:9600/


Output to Elasticsearch
* https://www.elastic.co/docs/reference/logstash/plugins/output-plugins
* https://www.elastic.co/docs/reference/fleet/secure-logstash-connections
```
input {
    stdin {}
}

output {
    elasticsearch {
        hosts => ["https://127.0.0.1:9200"]
        index => "hello-logstash"
        ssl_enabled => true
        user => "elastic"
        password => "vT*2WBHG+TGonr9YdzST"
        ssl_certificate_authorities => 'http_ca.crt' # Cert from ES
    }
    stdout { codec => rubydebug }
}
```