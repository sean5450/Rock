# Bro/zeek

### Installation

  - yum install zeek zeek-plugin-af_packet zeek-plugin-kafka -y

### Configuration

  - config files will be in /etc/zeek
  - `cd /etc/zeek`

#### Zeekctl.cfg

  - `vi zeekctl.cfg`
     - LogDir = /data/zeek
     - CfgDir = /etc/zeek
     - lb_custom.InterfacePrefix=af_packet::
       - (place at bottom of file)


#### Networks.cfg

  - `vi networks.cfg`
     - modify is necessary

#### Node.cfg

  - `vi node.cfg`
     - comment out lines 8-11
     - uncomment 16-36
     - delete second worker
     - add pin_cpus=3 to [manager]
     - [worker] lb_method=custom
     - [worker] lb_procs=2
     - [worker] pin_cpus=1,2
     - [worker] env_vars=fanout_id=93

```
[logger]
type=logger
host=localhost

[manager]
type=manager
host=localhost
pin_cpus=3

[proxy-1]
type=proxy
host=localhost

[interface-enp2s0]
type=worker
host=localhost
interface=enp2s0
lb_method=custom
lb_procs=2
pin_cpus=1,2
env_vars=fanout_id=93

```
#### Local.cfg

   - `cd /usr/share/zeek/site`
     - vi local.cfg
     - uncomment heartbleed, vlan-logging, mac-logging
     - @load scripts/json
     - @load scripts/afpacket
     - @load scripts/kafka
     - `mkdir scripts`
     - create json, afpacket, kafka files/scripts

       - `vi afpacket.zeek`

```
redef AF_Packet::fanout_id = strcmp(getenv("fanout_id'),"") == 0 ? 0 : to_count(getenv("fanout_id"));

```

       - `vi kafka.zeek`


```
redef Kafka::topic_name = "zeek-raw";
redef Kafka::json_timestamps = JSON::TS_ISO8061;
redef Kafka::tag_json = F;
redef Kafka::kafka_conf = table(
    ["metadata.broker.list"] = "172.16.100.100:9092"
);

# Enable bro logging to kafka for all logs
event bro_init() &priority=-5
{
    for (stream_id in Log::active_streams)
    {
        if (|Kafka::logs_to_send| == 0 || stream_id in Kafka::logs_to_send)
        {
            local filter: Log::Filter = [
                $name = fmt("kafka-%s", stream_id),
                $writer = Log::WRITER_KAFKAWRITER,
                $config = table(["stream_id"] = fmt("%s", stream_id))
            ];

            Log::add_filter(stream_id, filter);
        }
    }
}

```

     - `vi json.zeek`

```
redef LogAscii::use_json=T;
redef LogAscii::json_timestamps = JSON::TS_ISO8601;

```
    - `zeekctl check`
    - `zeekctl deploy`
    - `zeekctl stop`
    - `zeekctl status`
    - `zeekctl cleanup all`

---

# FSF

### Installation

    - `yum install fsf -y`

### Configuration   


    - `cd /opt/fsf/fsf-server/conf` vi config.py
    - modify log path: /data/fsf/logs
    - modify export path: /data/fsf/files
    - modify yara path: /var/lib/yara-rules/rules.yara
    - modify pid path: /run/fsf/scanner.pid
    - replace socket with sensor IP

```
#!/usr/bin/env python
#
# Basic configuration attributes for scanner. Used as default
# unless the user overrides them.
#

import socket

SCANNER_CONFIG = { 'LOG_PATH' : '//data/fsf/logs',
                   'YARA_PATH' : '/var/lib/yara-rules/rules.yara',
                   'PID_PATH' : '/run/fsf/scanner.pid',
                   'EXPORT_PATH' : '/data/fsf/files',
                   'TIMEOUT' : 60,
                   'MAX_DEPTH' : 10,
                   'ACTIVE_LOGGING_MODULES' : ['scan_log', 'rockout'],
                   }

SERVER_CONFIG = { 'IP_ADDRESS' : 172.16.100.100",
                  'PORT' : 5800 }

```
     - `cd /opt/fsf/fsf-client/conf` vi config.py
     - modify server listening to sensor IP

```
#!/usr/bin/env python
#
# Basic configuration attributes for scanner client.
#

# 'IP Address' is a list. It can contain one element, or more.
# If you put multiple FSF servers in, the one your client chooses will
# be done at random. A rudimentary way to distribute tasks.
SERVER_CONFIG = { 'IP_ADDRESS' : ['172.16.100.100',],
                  'PORT' : 5800 }

# Full path to debug file if run with --suppress-report
CLIENT_CONFIG = { 'LOG_FILE' : '/tmp/client_dbg.log' }

```

# Kafka/Zookeeper

### Installation

  `yum install Zookeeper kafka -y`

### Configuration

   - `/etc/zookeeper/`
   - `vi zoo.cg`

   - `vi /etc/kafka/server-properties`
     - line 31 insert IP
     - line 36 insert IP
     - line 60 logdirs = /data/kafka
     - line 103
     - line 123

#### Troubleshooting
     - /usr/share/kafka/bin
     - /opt/fsf/fsf-client/fsf_client.py --full ~/Bro-cheatsheet.pdf
     - /usr/share/kafka/bin/kafka-topics.sh --list --bootstrap-server 127.0.0.1:9092
     - /usr/share/kafka/bin/kafka-topics.sh --describe --bootstrap-server 127.0.0.1:9092
     - /usr/share/kafka/bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic bro-raw
     - /usr/share/kafka/bin/kafka-preferred-replica-election.sh --bootstrap-server 127.0.0.1:9092
     - rm -rf /var/lib/zookeeper/version-2/
     - rm -rf /data/kafka/*
     - rm -f /data/fsf/logs/rockout.logs
     - rm -f /data/suricata/eve.json
     - rm -f /data/steno/thread0/packets/*
     - rm -f /data/steno/thread0/index/*
