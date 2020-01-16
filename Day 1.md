## SSH Key Generation
- `ssh-keygen -t ed25519 -a 100 -C ""`

## Centos Load

### Hardware

- 4 CPU
- 32g ram
- 2 Disk
  - SSD
  - HDD

---

### Tools

- zeek CPU intensive
- steno write/disk
- kafka write/disk
- suricata write/disk CPU
- elastic
  - logstash ram intensive
  - elastic search write/disk
  - kibana
  - filebeat read/disk
- fsf
  - client (shipper)
  - server (yara rules/processing)

### Partitions

- select both partitions and delete/reclaim space.

- select LVM

- create volume groups
  - data
  - os  

- assign partitions
  - /home = 8 Gib
  - / = leftovers Gib
  - /var = 8 GiB
  - /boot = 2 GiB
  - /var/log = 2 GiB
  - /tmp = 2 GiB
  - /data/suricata = 100 GiB
  - /data/elastic = 100 GiB
  - /data/stenographer = 100 GiB
  - /data/kafka = 100 GiB
  - /data/fsf = 75 GiB
