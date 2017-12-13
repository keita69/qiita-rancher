# qiita-rancher

# 初期化
```
vagrant init centos/7
```

# Vagrantfile 編集

# 起動（VMイメージをダウンロードするので時間がかかる）
```
vagrant up
```

## Rancher setup 

${PROXY:PORT}
${PROXY_USER}
${PROXY_PW}

```

sudo vi /etc/profile

#最終行以降に以下の行を追記
PROXY='${PROXY:PORT}'
export http_proxy=$PROXY
export HTTP_PROXY=$PROXY
export https_proxy=$PROXY
export HTTPS_PROXY=$PROXY

sudo vi /etc/yum.conf

proxy=${PROXY:PORT}
proxy_username=${PROXY_USER}
proxy_password=${PROXY_PW}

# 参考
# https://qiita.com/inakadegaebal/items/be9fecce813cebec5986

sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum makecache fast
yum list docker-ce.x86_64 --showduplicates | sort -r
sudo yum -y install docker-ce


sudo vi /etc/sysconfig/docker
export http_proxy=http://${PROXY_USER}:${PROXY_PW}@${PROXY:PORT}/
export https_proxy=https://${PROXY_USER}:${PROXY_PW}@${PROXY:PORT}/
export no_proxy=127.0.0.1,localhost

sudo vi /etc/resolv.conf
nameserver 10.0.2.3
  ↓↓↓
nameserver 8.8.8.8

sudo mkdir /etc/systemd/system/docker.service.d
sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf
sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf

[Service]
Environment="HTTP_PROXY=http://${PROXY_USER}:${PROXY_PW}@${PROXY:PORT}" "NO_PROXY=localhost,127.0.0.1"
[Service]
Environment="HTTPS_PROXY=https://${PROXY_USER}:${PROXY_PW}@${PROXY:PORT}" "NO_PROXY=localhost,127.0.0.1"

sudo systemctl daemon-reload
sudo systemctl restart docker

vagrant  halt
vagrant  up

sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:preview

```
