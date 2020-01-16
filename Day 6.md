# Filebeat

### Installation

    - `yum install filebeat -y`

### Configuration

    - `vi filebeat.yml`

```
filebeat.inputs:
- input_type: log
  paths:
    - /data/suricata/eve.json
  json.keys_under_root: true
  fields:
    kafka_topic: suricata-raw
  fields_under_root: true
- input_type: log
  paths:
    - /data/fsf/logs/rockout.log
  json.keys_under_root: true
  fields:
    kafka_topic: fsf-raw
  fields_under_root: true
processors:
  - decode_json_fields:
      fields:
        - message
        - Scan Time
        - Filename
        - objects
        - Source
        - meta
        - Alert
        - Summary
    process_array: true
    max_depth: 10

```
#-------------------------- Kafka ouput ---------------------------------------
output.kafka:
  hosts: ["172.16.100.100:9092"]
  topic: '%{[kafka_topic]}'


# Elasticsearch

### Installation

    - `yum install elasticsearch -y`

### Configuration

    - `vi elasticsearch.yml`
      - change lines 17,23,37
      - uncomment line 43
      - uncomment line 55, change to _local:ipv4_
      - uncomment line 59
    - `vi jvm.options`
      - "-Xms8g"
      - "-Xmx8g"
    - `mkdir /etc/systemd/system/elasticsearch.service.d`
    - `vi /etc/systemd/system/elasticsearch.service.d/override.config`

```
       [Service]
       LimitMEMLOCK=infinity

```

### Troubleshooting

    - curl localhost:9200/_cat_

# kibana

### Installation

    `yum install kibana -y`

### Configuration

    `vi /etc/kibana/kibana.yml`
      - uncomment and change line 7
      - uncomment line 28
