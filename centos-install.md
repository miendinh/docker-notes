# install Docker in CentOS

vi /etc/yum.repos.d/CentOS-Sources.repo

[extras-source]
name=CentOS-$releasever - Extras Sources
baseurl=http://vault.centos.org/centos/$releasever/extras/Source/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

sudo yum-config-manager \
      --add-repo \
      https://download.docker.com/linux/centos/docker-ce.repo


sudo yum-config-manager --enable docker-ce-edge

sudo yum-config-manager --enable docker-ce-test

sudo yum install docker-ce

## list of docker version

yum list docker-ce --showduplicates | sort -r

## start a docker
service docker start

sudo systemctl start docker

## hello world

sudo docker run hello-world

## test with ubuntu bash

docker run -it ubuntu bash
