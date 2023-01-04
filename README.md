# SpringBoot_ELK
This is a sample POC for centralizing and monitoring logs using ElasticSearch, LogStash , Kibana

Steps to be Followed:
1. Download the Elasticsearch zip file .
  Next, you can start the Elasticsearch cluster by running bin/elasticsearch on Linux and macOS or bin\elasticsearch.bat on Windows.

  To make sure the Elasticsearch cluster is up and working fine, open the browser at http://localhost:9200
  You will see Following Response:
  ```
  {
  "name" : "LAPTOP-UJ1121L9",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "F2yxEyrmQsGoI4HGeVtr_A",
  "version" : {
    "number" : "8.5.3",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "4ed5ee9afac63de92ec98f404ccbed7d3ba9584e",
    "build_date" : "2022-12-05T18:22:22.226119656Z",
    "build_snapshot" : false,
    "lucene_version" : "9.4.2",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
  }
  ```
  
2. Kibana
      Setting up Kibana is similar to Elasticsearch. Just download the latest version application from the official website.

      To start Kibana, run bin/kibana on Linux and macOS or bin/kibana.bat on Windows.

      By default, Kibana listens on port 5601. If you go to http://localhost:5601, Kibana UI will be displayed.

3. Logstash
      Download and extract the latest version of Logstash from official Logstash downloads
      Here, inside the bin folder, you need to create one configuration(.conf) file. For instance, in this case, you will be creating logstash.conf.
      
4. Run the springBoot Application.The log File might have got generated in logs/application.log path.
      
      
5.  Open Logstash Folder and open the logstash.conf file.

      Add the following content to it:
      ```
      input {
        file {
          type => "log"
          path => "/ELK/logs/application.log"    # Make sure you provide the absolute path of the file here
        }
      }

      filter {
        if [message] =~ "\tat" {
          grok {
            match => ["message", "^(\tat)"]
            add_tag => ["stacktrace"]
          }
        }

      }

      output {
        stdout {
          codec => rubydebug
        }

        elasticsearch {
          hosts => ["localhost:9200"]
        }
      }    
  ```    
In the input, you have specified the type and the complete path of your file. Note that the value of the path must be absolute and cannot be relative.
In filters, wherever in the logs you will find a Tab character(\t) followed by "at", you will tag that entry as a stacktrace of an error. Grok is simply a filter plugin useful to parse and apply queries to unstructured data in Logstash.
In the output, you have defined a response that prints to the STDOUT of the console running Logstash using output codecs.    

6. To run the logstash configuration file, use command: logstash -f <file>.conf

7. Hit this API for getting the additional logs to be generated:
    http://localhost:8080/api/get/Food-Details
    
8. To access data from Elasticsearch, Kibana requires index patterns.
    For this ---> navigate to http://localhost:9200/_cat/indices
      
9.  Next got to http://localhost:5601/app/management/kibana/dataViews and create data view with the index pattern found in the above step nmo 8.

10. After this, head on to the http://localhost:5601/app/discover.Select the index from the filters that you just created, 
    and you'll be able to see and analyze the logs.
