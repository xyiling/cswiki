```bash
# 运行elasticsearch
docker run -d \
  --name es-node \
  -p 9200:9200 -p 9300:9300 \
  -e "discovery.type=single-node" \
  -e "xpack.security.enabled=false" \
  -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
  -e "ELASTIC_PASSWORD=123456" \
  -v /opt/docker/elasticsearch:/usr/share/elasticsearch/data \
  elasticsearch:9.4.3

# 生成kibana-token，用于kibana连接到esasticsearch
docker exec -it es-node /usr/share/elasticsearch/bin/elasticsearch-service-tokens create elastic/kibana kibana-token
# 得到AAEAAWVsYXN0aWMva2liYW5hL2tpYmFuYS10b2tlbjpQLUxvQ1RlVlFTQ1R2TFM0TUxOWVhB

# 部署kibana:9.4.3
docker run -d --name kibana \
  -p 5601:5601 \
  -v /opt/docker/kibana:/usr/share/kibana/data \
  -e "ELASTICSEARCH_HOSTS=https://es-node:9200" \
  -e "ELASTICSEARCH_SERVICEACCOUNT_TOKEN=AAEAAWVsYXN0aWMva2liYW5hL2tpYmFuYS10b2tlbjpQLUxvQ1RlVlFTQ1R2TFM0TUxOWVhB" \
  -e "ELASTICSEARCH_SSL_VERIFICATIONMODE=none" \
  -e "XPACK_SECURITY_ENCRYPTIONKEY=kibanasupersecretkey943string000" \
  -e "XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=kibanasupersecretkey943string000" \
  -e "XPACK_REPORTING_ENCRYPTIONKEY=kibanasupersecretkey943string000" \
  --link es-node:es-node \
  kibana:9.4.3

# 
```