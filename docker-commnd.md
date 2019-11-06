[TOC]

#docker-install
```
yum remove docker docker-client docker-client-latest docker-common  docker-latest docker-latest-logrotate docker-logrotate docker-engine
sudo yum install -y yum-utils device-mapper-persistent-data  lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

#docker-machine-install

版本可以替换
```
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
```
#docker-compose-install
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

#docker 阿里云加速
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://jhzi2d33.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
#docker 开机自启

`sudo systemctl enable docker`

#docker-install-portainer

`docker run -d -p 19000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --name cnooc_portainer portainer/portainer`

#weavescope
```
curl -L git.io/scope -o /usr/local/bin/scope
sudo chmod +x /usr/local/bin/scope
scope launch 192.168.1.62
```



