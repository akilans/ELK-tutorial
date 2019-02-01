# ELK - Elasticsearch. Logstash, Kibana

# Elasticsearch Installation [ JAVA , CENTOS]

    * rpm package recommended - version can't be updated yum update
    * Install java 8
    * Download rpm file for Elasticsearch 6
    * sudo rpm -i elasticsearch.rpm
    * sudo systemctl daemon reload
    * sudo systemctl enable elasticsearch.service
    * /etc/elasticsearch/elasticsearch.yaml - edit cluster name, node name, set data path
    * /etc/sysconfig/elasticsearch - Set JAVA_HOME
    * /etc/elastcisearch/jvm.options - Change memory settings
    * create datapath and chnage the owner - chmod -R elastcisearch:elastcisearch /elastcisearchdata
    * edit /etc/elastcisearch/elastcisearch.yml - network.host=0.0.0.0 [ Allow Cross Origin Requests ]
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

# ElasticSearch

    * Create Data

        * PUT - http://localhost:9200/$INDEX/$TYPE/$ID [ Create/Update entry with Specified ID]
            - curl -XPUT 'http://192.168.33.11:9200/infosys/employees/1?pretty' -H 'Content-Type: application/json' -d'{"name": "Gayatri", "location": "Pune", "Role": "Manager"}'

        * POST - http://localhost:9200/$INDEX/$TYPE [ Create entry with Random ID]
            - curl -XPOST 'http://192.168.33.11:9200/infosys/employees?pretty' -H 'Content-Type: application/json' -d'{"name": "Akilan", "location": "Bangalore", "Role": "Technology Lead"}'

    * Read Data
        * GET - http://localhost:9200/$INDEX/$TYPE/$ID?pretty
            - All - curl -XGET 'http://192.168.33.11:9200/infosys/_search?pretty&q=*:*'
            - curl -XGET 'http://192.168.33.11:9200/infosys/employees/1?pretty'
    
    * Delete Data

        * curl -XDELETE "http://192.168.33.11:9200/infosys/employees/1?pretty"

        * curl -XDELETE 'http://192.168.33.11:9200/infosys?pretty' - Deletes All the data

    * BUlk Insert
        * Usually we won't insert single data everytime which is not recommended. Instead insert as bulk data per day/hour like that. Load is low and also efficient

        * For example apache logs everyday as index and insert every day

        - curl -XPOST 'http://192.168.33.11:9200/_bulk?pretty' -H 'Content-Type: application/json' -d'> {"index": {"_index": "infosys","_type": "employees"}}
> {"name": "Prasanna", "location": "Pune", "Role": "Group Manager"}
> {"index": {"_index": "infosys","_type": "employees"}}
> {"name": "Anupama","location": "Pune", "Role": "Delivery Head"}
> '
    
    * Mapping[ Assign data types for attributes in the document such as integer, float etc], 
    * Index [ Rename and delete], Index aliases [ Another name ]

    * Query Data 

        - curl -XGET "http://192.168.33.11:9200/infosys/_search?q=name:Anupama&pretty"
        

### Load sample data

    * wget https://download.elastic.co/demos/kibana/gettingstarted/shakespeare_6.0.json

    *  curl -XPOST "http://192.168.33.11:9200/shakespeare/doc/_bulk?pretty" -H "Content-Type: application/json" --data-binary @shakespeare_6.0.json

    * curl -XGET "http://192.168.33.11:9200/shakespeare/_search?pretty" -H "Content-Type: application/json" -d '
> {
> "query": {
> "match_phrase": {
> "text_entry": "to be or not to be"
> }
> }
> }'


    #### Mappings:

    - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies" -d '
{
"mappings" : {
"movie" : {
"properties" : {
"year" : { "type" : "date" }
}
}
}
}'
    * Get mapping data -
        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/_mappings?pretty"

    * Writing single document on movies index and movie type with year "date" mapping
        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies/movie/109487" -d '
> {
> "genre" : ["IMAX","Sci-Fi"],
> "title" : "Intersteller",
> "year" : 2014
> }'

    * curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/109487?pretty"

    * Insert bulk data
        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/_bulk?pretty" --data-binary @movies.json

    * Updating Document
        - curl -H "Content-Type: application/json" -XPOST "http://192.168.33.11:9200/movies/movie/109487/_update?pretty" -d '
> {
> "doc" : {
> "title" : "Intersteller woo"
> }
> }'

        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies/movie/109487?pretty" -d '
{
"genre" : ["IMAX","Sci-Fi"],
"title" : "Intersteller",
"year" : 2014
}'

        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?q=Dark&pretty"

        * Deleting Document
        - curl -H "Content-Type: application/json" -XDELETE "http://192.168.33.11:9200/movies/movie/58559?pretty"

    * CRUD example

        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies/movie/20000?pretty" -d '
> {
> "genre" : ["Documentary"],
> "title" : "Akilan Adventures",
> "year" : 2019
> }'

        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/20000?pretty"

        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies/movie/20000?pretty" -d '
{
"genre" : ["Documentary","Comdey"],
"title" : "Akilan Adventures",
"year" : 2019
}'
    * Partial Update

        - curl -H "Content-Type: application/json" -XPOST "http://192.168.33.11:9200/movies/movie/20000/_update?pretty" -d '
> {
> "doc" : {
> "title" : "Akilan Exploration"
> }
> }'

        *  To avoid conflicts pass retry_on_conflict

            - curl -H "Content-Type: application/json" -XPOST "http://192.168.33.11:9200/movies/movie/20000/_update?retry_on_conflict=3&pretty" -d '
{
"doc" : {
"title" : "Akilan Atrocity"
}
}'


        - curl -H "Content-Type: application/json" -XDELETE "http://192.168.33.11:9200/movies/movie/20000?pretty"

    * Analyser and mapping

        * mapping type "keyword" looks for exact match 
        * mapping type "text" get all the matches [ no capitalization, not exact match ]

        - curl -H "Content-Type: application/json" -XDELETE "http://192.168.33.11:9200/movies"

        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies" -d '
> {
> "mappings" : {
> "movie" : {
> "properties" : {
> "id" : { "type" : "integer" },
> "year" : { "type" : "date" },
> "genre" : { "type" : "keyword" },
> "title" : { "type" : "text", "analyzer" : "english" }
> }
> }
> }
> }'

    * curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
> {
> "query" : {
> "match" : {
> "title" : "Star Wars"
> }
> }
> }'

    *  curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
{
"query" : {
"match" : {
"genre" : "Sci-Fi"
}
}
}'


    * Mapping 

        -  curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/series?pretty" -d '
> {
> "mappings" : {
> "movie" : { 
> "properties" : {
> "film_to_franchise" : { "type" : "join", "relations" : { "franchise" : "film" } }
> }
> }
> }
> }'

    -  curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/series/_bulk?pretty" --data-binary @series.json 

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/series/movie/_search?pretty" -d '
{
"query" : {
"has_parent" : { "parent_type" : "franchise" , "query" : { "match" : { "title" : "Star Wars" } } }
}
}'

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/series/movie/_search?pretty" -d '
{
"query" : {
"has_child" : { "type" : "film" , "query" : { "match" : { "title" : "The Force Awakens" } } }
}
}'
