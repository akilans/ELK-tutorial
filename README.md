# ELK - Elasticsearch. Logstash, Kibana

# Elasticsearch Installation [ JAVA , CENTOS]

    * rpm package recommended - version can't be updated yum update
    * Install java 8
    * Download rpm file for Elasticsearch 6
    * sudo rpm -i elasticsearc.rpm
    * sudo systemctl daemon reload
    * sudo systemctl enable elasticsearch.service
    * /etc/elasticsearch/elasticsearch.yaml - edit cluster name, node name, set data path
    * /etc/sysconfig/elasticsearch - Set JAVA_HOME
    * /etc/elastcisearch/jvm.options - Change memory settings
    * create datapath and chnage the owner - chmod -R elastcisearch:elastcisearch /elastcisearchdata
    * sudo systemctl start elastcisearch
    * check /var/log/elastcisearch/ - log files
    * Defualt port - 9200 
    * curl -XGET 'http://localhost:9200/_cluster/health?pretty'
    * curl -XGET 'http://localhost:9200/_cluster/stats?human&pretty'
    * /usr/share/elastcisearch/ - has all the x-pack plugin ./bin/elastcisearch-plugin install x-pack
    
# Kibana Installation [ NODEJA , CENTOS]

     * rpm package recommended - version can't be updated yum update
     * Download rpm file for Kibana
     * sudo rpm -i kibana.rpm
     * /etc/kibana/kibana.yaml - Allow access kibana from anywehere,  Elasticsearch URL
     * Default port - 5601
     * sudo systemctl start kibana 
     * /usr/share/kibana - has all the x-pack plugins ./bin/kibana-plugin install x-pack
