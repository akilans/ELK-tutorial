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

### Searching in Elasticsearch

    * Simple Search [Not simpler, need to do URL encode for complex queries] 
    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?q=title:star&pretty"

    * Body Request - easy to read
    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
> {
> "query":{
> "match" : {
> "title" : "star" 
> }
> }
> }'

    * Query with Filters better for searching

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/series/movie/_search?pretty" -d '
> {
> "query" : {
> "bool" : {
> "must" : { "term" : { "title" : "trek" } },
> "filter" : { "range" : { "year" : { "gte" : 2010 }}}
> }
> }
> }'

    * match_phrase - must find all terms in correct order
    * slop - order matters but if your'e ok with some words in between the term [ if slop 1 means you are ok one word in between the term]

    * The following command returns "star trek beyond" title
    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
> {
> "query" : {
> "match_phrase" : {
> "title" : { "query" : "star beyond" , "slop" : 1 }
> }
> }
> }'

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
{
"query" : {
"bool" : {
"must" : { "match_phrase" : { "title" : "star wars" }},
"filter" : { "range" : { "year" : { "gt" : 1980 } } } 
}
}
}'

    * pagination - size & from

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?size=2&pretty"

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?from=1&size=2&pretty"

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
{
"from" : 1,
"size" : 2,
"query" : {
"match" : {
"genre" : "Sci-Fi"
}
}
}'

    * sorting - number based sorting works fine but text based sorting is difficult. It can be decided during mapping. Else drop the index and create mappings with raw and import it again

        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?sort=year&pretty"

    * Delete movies index and create mappings

        - curl -H "Content-Type: application/json" -XDELETE "http://192.168.33.11:9200/movies"

        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies" -d '
> {
> "mappings" : {
> "movie" : {
> "properties" : {
> "title" : { "type" : "text" , "fields" : { "raw" : { "type" : "keyword"} } }
> }
> }
> }
> }'

        - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies/_bulk?pretty" --data-binary @movies.json 

        - url -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d ' 
{
"sort" : "year"
}'


        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?sort=title.raw&pretty"

        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d ' 
{
"sort" : "title.raw"
}'

        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/_search?pretty" -d '
> {
> "query" : {
> "bool" : {
> "must" : { "match" : { "genre" : "Sci-Fi" } },
> "must_not" : { "match" : { "title" : "trek" } },
> "filter" : { "range" : { "year" : { "gte" : 2010 , "lt" : 2015 } } }
> }
> }
> }'

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/_search?pretty" -d '
{
"sort" : "title.raw",
"query" : {
"bool" : {
"must" : { "match" : { "genre" : "Sci-Fi" } },
"filter" : { "range" : { "year" : { "lt" : 1960 } } }
}                                                    
}
}'

    * fuzziness - allows small typos error [ akilan - akilen - ahilan allow fuzziness 1]

    * Interstellar is the correct title

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
{
"query" : {
"fuzzy" : {
"title" : { "value" : "Intersteller" , "fuzziness" : 2 }
}
}
}'

    * pefpix and wildcard
    
    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
{
"query" : {
"prefix": {
> "title" : "sta"
> }
> }
> }'

    - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/movies/movie/_search?pretty" -d '
{
"query" : {
"wildcard": {
"title" : "sta*"
}
}
}'

    * Elasticsearch - Google search, Youtube search like feature can be achieved by "Defining Analyzer", Map the analyser to the Index, Pass the analyser in Search query. It is called Ngram - Explore more if you want


## Importing Data from Different Sources

    * Delete the previous index
    * Download the Dataset
    * Use Python to convert it to JSON format
    * Insert into Elasticsearch

    - python3 MoviesToJson.py > moremovies.json

    - curl -H "Content-Type: application/json" -XDELETE "http://192.168.33.11:9200/movies"

    - curl -H "Content-Type: application/json" -XPUT "http://192.168.33.11:9200/movies/_bulk?pretty" --data-binary @moremovies.json

    * Another way to use packages for elasticsearch

    - wget http://media.sundog-soft.com/es/IndexTags.py

    - sudo pip3 install elasticsearch

    - python3 IndexTags.py


## Logstash

    * Collect data from different systems and feed it ElasticSearch [ Many others ]
    * sudo rpm -i logstash.rpm
    * create a file under /etc/logstash/conf.d/apache-logstash.conf
    * List all the indices
        - curl -H "Content-Type: application/json" -XGET "http://192.168.33.11:9200/_cat/indices?pretty"
    * cd /usr/share/logstash and run the below command
        - bin/logstash -f /etc/logstash/conf.d/apache-logstash.conf
    * Edit the access_log file and save. Logstash starts to export data into Elasticsearch

## Importing from MySQL table

    - CREATE DATABASE movielens;

    - CREATE TABLE movies (
    -> movieID INT PRIMARY KEY NOT NULL,
    -> title TEXT,
    -> releaseDate DATE
    -> );


    - LOAD DATA LOCAL INFILE "/home/akilan/Desktop/ELK-tutorial/ml-100k/u.item" INTO TABLE movies FIELDS TERMINATED BY '|'
    -> (movieID, title, @var3)
    -> set releaseDate = STR_TO_DATE(@var3, "%d-%M-%Y");

    * sudo /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/mysql-logstash.conf 
