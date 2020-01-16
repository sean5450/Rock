# Stenographer

### Installing
  - `yum install stenographer -y`

### Configuration
  - `vi config`
  - set up paths directory
  - set up index directory
  - set "interface"

```
{
  "Threads": [
    { "PacketsDirectory": "/data/steno/thread0/packets/"
    , "IndexDirectory": "/data/steno/thread0/index/"
    , "MaxDirectoryFiles": 30000
    , "DiskFreePercentage": 10
    }
  ]
  , "StenotypePath": "/usr/bin/stenotype"
  , "Interface": "enp2s0"
  , "Port": 1234
  , "Host": "127.0.0.1"
  , "Flags": []
  , "CertPath": "/etc/stenographer/certs"
}

```


   - run `stenokeys.sh stenographer stenographer`
    - stenokeys.sh (user) (group)

  - change permissions of steno directory `sudo chown -R stenographer: /data/steno/`
  - start steno service `systemctl start stenographer`

  - `ethtool -K enp2s0 tso off gro off lro off gso off rx off tx off sg off rxvlan off txvlan off`
  - `ethtool -N enp2s0 rx-flow-hash udp4 sdfn`
  - `ethtool -N enp2s0 rx-flow-hash udp6 sdfn`
  - `ethtool -C enp2s0 adaptive-rx off`
  - `ethtool -C enp2s0 rx-usecs 1000`
  - `ethtool -G enp2s0 rx 4096`

#### Troubleshooting

  - journalctl -xeu stenographer

---

# Suricata

### Installation

  - `yum install suricata -y`

### Configuration

  - `vi /etc/suricata/suricata.yaml`
  - :set nu (display line #)
  - set outputs line 76: no
  - stats log off line 404: no
  - set default directory: `default-log-dir: /data/suricata/`
  - suricata-update
  - `vi /etc/sysconfig/suricata`

     - OPTIONS="--af-packet=enp2s0 --user suricata "

  - `cat /proc/cpuinfo | egrep -e 'processors|physical id|core id' | xargs -l3`
  - `curl -L -O http://192.168.2.11:8009/suricata-5.0/emerging.rules.tar.gz`
  - mv file to rules directory
  - tar -xzf emerging.rules.tar.gz
  - suricata-update
  - chown suricata directory `chown -R suricata: /data/suricata`
  - cp /usr/share/suricata/classification.config /etc/suricata/
  - cp /usr/share/suricata/reference.config /etc/suricata/
