# Starting a multi-node cluster, Kibana and Fluentd with TLS enabled using Docker Compose

https://docs.fluentd.org/container-deployment/docker-compose
https://docs.fluentd.org/output/elasticsearch
https://github.com/uken/fluent-plugin-elasticsearch
https://www.elastic.co/guide/en/elastic-stack-get-started/7.8/get-started-docker.html#get-started-docker-tls

*Make sure Docker Engine is allotted at least 4GiB of memory*

### Generate certificates for Elasticsearch by bringing up the create-certs container:
```bash
docker-compose -f create-certs.yml run --rm create_certs
```

### Bring up the three-node Elasticsearch cluster, Kibana etc:
```bash
docker-compose -f efk-docker-tls.yml up -d
```

### Run the elasticsearch-setup-passwords tool to generate passwords for all built-in users, including the kibana_system user
```bash
docker exec es01 /bin/bash -c "bin/elasticsearch-setup-passwords \
  auto --batch --url https://es01:9200"
```

### Set ELASTICSEARCH_PASSWORD in the elastic-docker-tls.yml compose file to the password generated for the kibana_system user


### Use docker-compose to restart the cluster and Kibana:
```bash
docker-compose stop
docker-compose -f efk-docker-tls.yml up -d
```

### Access the Elasticsearch API over SSL/TLS using the bootstrapped password:
```bash
docker run --rm -v es_certs:/certs --network=es_elastic docker.elastic.co/elasticsearch/elasticsearch:7.8.1 curl --cacert /certs/ca/ca.crt -u elastic:PleaseChangeMe https://es01:9200
```

### Open Kibana to load sample data and interact with the cluster: https://localhost:5601.

### Tear down the containers, network, and volumes
```bash
docker-compose -f efk-docker-tls.yml down -v
```
