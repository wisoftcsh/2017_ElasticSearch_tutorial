ELASTIC STACK

과거에는 ELK

Elasticsearch

Logstach

Kibana

+Beats 서비스 추가하면서 이름 변경

Kinana
Elasticsearch (검색엔진)
Beats.  Logstash

100% open-source Software

Noenterprise edition

All new versions with 5.0

Elasticsearch (분산 검색 엔진, Rest API)
---
Data sotre

Search engine

Realtime analytic platform


Kibana (분석 도구)
---
Window into the Elastic Stack

Analyze and Visualize

Geospatial

Graph Exploration

Logstash 
---
Data processing pipeline

Ingest Data of All Shapes, Sizes, and Sources

Parse & Transform your data on the fly

choose your stash, transport your data

create and configure your pipeline, your way

Beats
---
Lightweight data shippers

plain and simple

ship from the source

ship to logstash for transformation andparsing

설치 (elasticsearch)
---
4개다 설치 <br>
1. 압축 해제 <br>
2. 환경 변수 <br>
3. 실행 elasticsearch <br>
4. curl http://localhost:9200 <br>

설치 (Kibana)
---
1. elasticsearch 실행
2. brew install kibana
3. config/kibana.yml
4. kibana 실행
5. http://localhost:5601

설치 (logstash)
---
1. brew install logstash
2. prepare a logstash.conf
3. logstash -f logstash.conf

hello world!
---
Elasticsearch vs. RDB

Elasticsearch - using rest API

curl -XGET http://localhost:9200 : 서버정보

curl -XGET http://localhost:9200/classes : classes 정보

curl -XPUT http://localhost:9200?classes : classes 인덱스 생성

curl -XDELETE http://localhost:9200?classes : classes 인덱스 삭제

문서 생성

curl -XPOST http://localhost:9200/classes/class/1/ -d '{"title":"database", "professor":"hyunju"}'

curl -XPOST http://localhost:9200/classes/class/2/ -d @file.json

문서 업데이트

curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"doc": {"unit" : 3}}'

curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"script": "ctx._source.unit += 1"}'


curl 도구
---
brew install httpie

http http://localhost:9200 or http :9200

put을 쓰는 이유는 elasticsearch에는 주소가 없기때문에

http post :9200/classes/class/1 title=database professor=hyunju

http post :9200/_bulk @file_bulk.json

업데이트 : http post :9200/classes/class/1/_update doc:='{"unit":3}'

echo '{"script":"ctx._source.unit +=5"}' | http post :9200/classes/class/2/_update

검색 :http :9200/classes/class/_search?q=rating:5


도메인을 지정하는 방법 = 맵핑
---
         
    {
       "class" : {
           "properties" : {
               "title" : {"type":"text"},
               "professor" : {"type":"keyword"},
               "org" : {"type":"keyword"},
               "unit" : {"type": "long"},
               "rating" : {"type":"long"},
               "submit_date" : {"type":"date", "format":"yyyy-MM-dd"},
               "school_location" :{"type":"geo_point"}
           }
       }
    }
  
(위에 코드는 json 파일)


http put :9200/classes/class/_mapping @mapping.json


데이터 가공
---
	{
       "size" : 0,
       "aggs" : {
           "avg_unit" : {
               "avg" : { "field" : "unit"}
           }
       }
    }
    
    -------------------------------------
    
    {
       "size" : 0,
       "aggs" : {
           "stats_unit" : { // min,max,sum,avg...
               "stats" : { "field" : "unit"}
           },
           "org" : { // 그룹
               "terms" : { "field" : "org" }
               "aggs" : {
                  "stats_unit" : { // min,max,sum,avg...
                    "stats" : { "field" : "unit"}
                 }
              }
           }
       }
    }
(위에 코드는 json 파일)

http :9200/classes/class/_search @unit__aggs.json


Kibana 연동
---
kibana.yml 파일 검색 ( max : /usr/local/etc/kibana.yml )

###server.host: "localhost" <br>
###elasticsearch.url: "http://localhost:9200" <br>
###수정해야 외부접속 가능<br>


