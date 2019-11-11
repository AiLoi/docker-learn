[TOC]
# git-lab-Centos
```
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld


sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix


curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash

sudo EXTERNAL_URL="192.168.10.203" yum install -y gitlab-ee

wget https://gitlab.com/xhang/gitlab/-/archive/12-3-stable/gitlab-12-3-stable.tar.gz
```


# docker-install-gitlab
```
docker run -d -p 8443:443 -p 8090:80 -p 2222:22 --name gitlab --restart always -v D:/Docker/data/gitlab/config:/etc/gitlab -v D:/Docker/data/gitlab/logs:/var/log/gitlab -v D:/Docker/data/gitlab/data:/var/opt/gitlab gitlab/gitlab-ee:12.0.8-ee.0

docker run -d   --env GITLAB_OMNIBUS_CONFIG="external_url 'http://ailuoli.cn/'; gitlab_rails['lfs_enabled'] = true;" -p 8443:443 -p 8090:8090 -p 2222:22 --name gitlab --restart always gitlab/gitlab-ee:12.0.8-ee.0
```


# git-lab-runner 设置docker权限
```
sudo usermod -aG docekr gitlab-runner
sudo service docker restart
sudo gitlab-ci-multi-runner restart
```
git@b1b54180a160:ailuoli/helloworld.git
git@127.0.0.1:2222:ailuoli/helloworld.git

# gitlab-runner-install
```
docker run -d --name gitlab-runner --restart always -v D:/Docker/data/gitlab-runner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest
<<<<<<< HEAD

docker exec -it gitlab-runner gitlab-runner register

if [ $(docker ps -aq -f name=web)]; then docker rm -f web;fi
```


=======
```
>>>>>>> eeeb7397933d068f895f9f542809814c6b382615
