# ELK

[Install Elastic Search](https://www.elastic.co/guide/en/elastic-stack-get-started/7.5/get-started-elastic-stack.html)


## Quickstart

(Run all the things below are running)

1. Start `elasticsearch`

        elasticsearch
        
1. Start `kibana`

        kibana 

1. Start `logstash`

        logstash -f /usr/local/etc/logstash/first-pipeline.conf --config.reload.automatic    

1. Start `filebeat`

        rm -rf /usr/local/var/lib/filebeat/registry
        filebeat -e -c /usr/local/etc/filebeat/filebeat.yml -d "publish"        

1. Before starting the server, setup the `misc` index to disable index explosion, use the kibana dev tools, or API

        GET /_cat/indices

        GET /filebeat-7.5.1-pronghorn/_mapping
        GET /filebeat-7.5.1-pronghorn/_mapping/field/misc.appName

        # Delete the index
        DELETE /filebeat-7.5.1-pronghorn

        # Create the index
        PUT /filebeat-7.5.1-pronghorn

        # Update mappings
        PUT filebeat-7.5.1-pronghorn/_mapping
        {
            "properties": {
                "misc": { 
                    "type":"object",
                    "enabled": false
                }
            }
        
        }

    rm -rf /usr/local/var/lib/filebeat/registry

# Setup

## For MacOS

<!-- tabs:start -->

### ** elasticsearch **

```bash
brew tap elastic/tap    
brew install elastic/tap/elasticsearch-full
elasticsearch
```

Running on port [9200](http://localhost:9200)

Retrieving Indexes:
```bash
curl 'localhost:9200/_cat/indices?v'
```

Deleting an existing index:
```bash
curl -XDELETE localhost:9200/logstash-2020.01.16-000001
curl -XDELETE localhost:9200/logstash*
curl -XDELETE localhost:9200/filebeat*
```

Searching an index

```bash
curl -XGET 'localhost:9200/filebeat-pronghorn-2020.01.17/_search?pretty' | jq
```
Specifying a query
```bash
curl 'localhost:9200/filebeat-7.5.1-pronghorn/_search?pretty&q=appName:dicom' | jq '.'
```

Or, find work for a session:
```bash
http://localhost:9200/filebeat-7.5.1-pronghorn/_search?pretty&q=sessionId:283
```

Setting up an API Key??  https://www.elastic.co/guide/en/elasticsearch/reference/current/security-api-create-api-key.html

If the request comes from localhost, unnecessary, but for talking to elastic search overall, need some.

Getting templates

```bash
curl 'localhost:9200/_template/filebeat-7.5.1' | jq 
```

### ** kibana **

```bash
brew install elastic/tap/kibana-full
kibana
```

Running on port [5601](http://localhost:5601)

?>  If you want to allow remote users to connect, set the parameter `server.host` in `kibana.yml` (`/usr/local/etc/kibana/kibana.yml`) to a non-loopback address.

### ** logstash **

```bash
brew install elastic/tap/logstash-full
```

[Examples](https://www.elastic.co/guide/en/logstash/7.5/config-examples.html)

Creating a pipeline `/usr/local/etc/logstash`

```conf
input {
    beats {
        port => "5044"
    }
}
# The filter part of this file is commented out to indicate that it is
# optional.
filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG}"}
    }
}
output {
    stdout { codec => rubydebug }
}
```

[Other filters](https://www.elastic.co/guide/en/logstash/7.5/lookup-enrichment.html)

To run logstash

```bash
logstash -f /usr/local/etc/logstash/first-pipeline.conf --config.reload.automatic
```

As you make changes to `first-pipeline.conf` you will not have to reload logstash.

To have launchd start `elastic/tap/logstash-full` now and restart at login, run:

```bash
brew services start elastic/tap/logstash-full
```

To just run Logstash, in the foreground, run:

```bash
logstash
```

### Full (sample) pipeline 

For java/pronghorn:
[View Raw File](files/pronghorn.conf ':ignore')

```conf
input {
    beats {
        port => "5044"
    }
}
# The filter part of this file is commented out to indicate that it is
# optional.
filter {
    if "multiline" in [log][flags] {
        
        grok {
            match => { 
                "message" => "%{TIMESTAMP_ISO8601:tstamp}\t%{LOGLEVEL:lvl}\s{0,1}\t\[(%{JAVAFILE:worker}\s)?%{JAVAFILE:thread}(:\s%{JAVALOGMESSAGE:status})?\]\t%{JAVACLASS:class}\t-\t?%{DATA:msg}\n%{GREEDYDATA:data}"
                 
            }
        }
        mutate {
            add_tag => "verbose"
        }
    } else {
    
        grok {
            patterns_dir => ["/usr/local/etc/logstash/patterns"]
            match => { 
                "message" => "%{TIMESTAMP_ISO8601:tstamp}\t%{LOGLEVEL:lvl}\s{0,1}\t\[(%{JAVAFILE:worker}\s)?%{JAVAFILE:thread}(:\s%{JAVALOGMESSAGE:status})?\]\t%{JAVACLASS:class}\t-\t?%{GREEDYDATA:msg}" 
            }
        }
    }
    # use the log timestamp
    date {
        match => ["tstamp", "ISO8601"]
        remove_field => "tstamp"
    }
    # convert log levels to numbers
    translate {
        field => "lvl"
        destination => "[log][level]"
        dictionary => {
            "ERROR" => "50"
            "WARN" => "40"
            "INFO" => "30"
            "DEBUG" => "20"
            "TRACE" => "10"
        }
        # Keep the text field version, or not
        # remove_field => "lvl"
    }

    # parse JSON
    json {
        source => "data"
        add_tag => [ "json" ]
    }
    
    
    # some sort of hash
    if [log][file][path] =~ "pronghorn_main" {
        mutate {
            add_tag => "pronghorn_main"
            remove_field =>  [ "[log][file][path]" ]
        }
    }
     if [log][file][path] =~ "pronghorn_stacktrace" {
        mutate {
            add_tag => "pronghorn_stacktrace"
            remove_field =>  [ "[log][file][path]" ]
        }
    }
    
    # Show the full log message if it didn't fail
    # if "_grokparsefailure" not in [tags] {
    #     # do something
    #     # drop {}
    #     mutate {
    #         remove_field =>  [ "message" ]
    #     }
    # }

    mutate {
        # rename => { "msg" => "message" }
        # remove noise in tags
        remove_tag => "beats_input_codec_plain_applied"
    }
}
output {
    # When debugging
    stdout { codec => rubydebug }
    # For search
    elasticsearch { 
        hosts => [ "localhost:9200" ]
        # index => "filebeat-7.5.1-pronghorn-%{+yyyy.MM.dd}"
        index => "filebeat-7.5.1-pronghorn"
    }
}
```

### ** filebeat **

    brew install elastic/tap/filebeat-full


[Filebeat Instructions](https://www.elastic.co/guide/en/beats/filebeat/7.5/filebeat-getting-started.html)

Config file can be found at `/usr/local/etc/filebeat/filebeat.yml`

Test out the config: 

    filebeat test config -e

Start filebeat

    filebeat -e -c filebeat.yml -d "publish"

In order to get filebeat to re-harvest the sample logfile:

    rm -rf /usr/local/var/lib/filebeat/registry

Or

    rm /usr/local/var/lib/filebeat/registry/filebeat/data.json

Viewing filebeat logs:

    cd /usr/local/var/log/filebeat

The fields being used are in the `fields.yml` file at `/usr/local/Cellar/filebeat-full/7.5.1/libexec`.

https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-template.html#load-template-auto

### Example yaml file

[See Sample](/files/filebeat.yml ':ignore')


<!-- tabs:end -->

### Multiline

* [Multiline processing via logstash](https://www.elastic.co/guide/en/logstash/current/plugins-codecs-multiline.html)
* [Multiline processing via filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/_examples_of_multiline_configuration.html)

### Other (helpful) References
* [Filter Plugins](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)
* [Reference: Patterns](https://github.com/logstash-plugins/logstash-patterns-core/tree/master/patterns)


## Grok

Sample Javascript line
```
"%{TIMESTAMP_ISO8601:tstamp}\t%{LOGLEVEL:lvl}\s{0,1}\t\[%{JAVAFILE:thread}\]\t%{JAVACLASS:class}\t-\t%{GREEDYDATA:msg}
```
