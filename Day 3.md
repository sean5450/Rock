# Repo

YUM = yellowdog update manager

## YUM Command Line

 - yum --help
 - yum install
 - yum update
 - yum search
 - yum provides
 - yum makecache fast
 - yum clean all

## YUM Configuration Files

 - /etc/yum.repos.d/

    - [local-base] `required`
    - name=local-base `required`
    - baseurl=http://repo/base/ `required`
    - enabled=1
    - gpgcheck=0

---

  - /etc/yum.config

    - global settings

---

## Create a Yum Repo

### On local laptop

  - cp local.repo > local-yum.repo
  - sudo vi local-yum.repo
    - :%s/repo/x.x.x.x/g find and replace

  - yum clean all
  - yum makecache fast

---

### Install yum-utils

  - yum install yum-utils

---

### Hosting Packages

  - cd /srv
  - mkdir repos
  - cd repos/


### Sync Repo

  - yum repolist : to find all repoid names

  - reposync -l --repoid="repo_name" --download_path=/srv/repos --newest only --downloadcomps --download-metadata
                           ^local-base^

### Create Repo

  - yum install createrepo
  - creatrepo <repo_name> (i.e "local-base or base" from inside repos directory)
      - `createrepo local-base/ -g comps.xml`

---

  - yum install nginx

### NGINX

  - vi /etc/nginx/conf.d/repo.conf
  - sudo -s
---

server {
listen 8008;

location / {
  root /srv/repos;
  autoindex on;
  index index.html index.htm;
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
    root /usr/share/nginx/html;
  }
}
---
vi /etc/nginx/conf.d/proxy.conf

server {
  listen 80;
  server_name localrepo localrepo.local.lan

  proxy_max_temp_file_size 0;

  location / {
    proxy_set_header X-Real_IP $remote_addr;
    proxy_set_header Host $http_host;
    proxy_pass http://127.0.0.1:8008
  }
}
---

  - systemctl restart nginx


firewall-cmd --zone-public --add-port:8008/tcp --permanent
firewall-cmd --reload

---

---

### selinux

ls -Z (selinux)
chcon -R -u system_u -t httpd_sys_content_t (do this on repos directory)

---

### Troubleshooting Only

  - systemctl stop firewalld
  - setenforce 0 (stops selinux); 1 to turn back on
    - getenforce (verify status)

## Repo Tools

  - yum downloader
  - repotrack    
    - repotrack -a arch-type -p /path/to/save/rpms packages suricata

---

# Sensor   

![1](http://192.168.2.11:8081/seanpudlock/pudlock/raw/branch/master/whiteboard.jpeg)

## Ports
  - Kafka: 9092
  - Zookeeper: 2181, 2182, 2183
  - Elastic: 9200, 9300
  - Kibana: 5601
  - Logstash: 5044


# Spanning and Tapping
