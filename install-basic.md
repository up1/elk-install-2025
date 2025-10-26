# Install ELK stack

## 1. [Elasticsearch](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/installing-elasticsearch)
* 9200 (http.port) = REST API
* 9300 (transport.port) = Transport API. Used for intra-cluster communications and client access via the transport API (Java client)
```
$xattr -d -r com.apple.quarantine .
$./bin/elasticsearch 
```

Reset password for user=elastic
```
$./bin/elasticsearch-reset-password -u elastic
```

password=*fJZSnxD0VuIm2HXhtSi

Access to https://localhost:9200/
```
{
  "name" : "Somkiats-MacBook-Pro.local",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "s1A9bBhSQjum91PmkgvTCA",
  "version" : {
    "number" : "9.2.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "25d88452371273dd27356c98598287b669a03eae",
    "build_date" : "2025-10-21T10:06:21.288851013Z",
    "build_snapshot" : false,
    "lucene_version" : "10.3.1",
    "minimum_wire_compatibility_version" : "8.19.0",
    "minimum_index_compatibility_version" : "8.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

Configurations
* https://www.elastic.co/docs/deploy-manage/deploy/self-managed/important-system-configuration


## 2. Kibana
* 5601 (server.port) = Kibana server port
```
$./bin/kibana
```