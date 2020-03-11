#### 安装centos7 64位操作系统

#### 联网(已联网的忽略)

修改vi /etc/sysconfig/network-scripts/ifcfg-ens33(文件以实际为准)

修改ONBOOT=yes，然后重启网络服务systemctl restart network。

Ping [www.baidu.com](http://www.baidu.com) 验证



 

#### 内核升级（高于3.10忽略）

```
rpm -import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
列出可用的内核相关包
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available

```

安装命令（推荐使用4.X版本，个人5.X版本升级后启动失败）install后面根据列出来的自行修改

```
yum -y --enablerepo=elrepo-kernel install kernel-lt.x86_64 kernel-lt-devel.x86_64 
```

**修改启动项**

grub2-set-default 0

**重启**

Reboot

**验证**

```
uname -r
```

 

#### VIM安装（可以忽略）

```
yum -y install vim*
```

#### 修改yum源

cd /etc/yum.repos.d/

mv CentOS-Base.repo CentOS-Base.repo.backup

curl -O http://mirrors.163.com/.help/CentOS7-Base-163.repo

mv CentOS7-Base-163.repo CentOS-Base.repo

yum clean all

yum makecache

#### 时间同步

​      yum install ntpdate -y

​      cp --force /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

​      ntpdate us.pool.ntp.org

​       (crontab -l;echo ' */10 * * * * /usr/sbin/ntpdate us.pool.ntp.org | logger -t NTP')|crontab - 

#### 安装firewall(不能省)

​       systemctl start firewalld

yum install firewalld  -y

firewall-cmd --zone=public --add-port=22/tcp --permanent && \

firewall-cmd --zone=public --add-port=500/udp --permanent && \

firewall-cmd --zone=public --add-port=4500/udp --permanent && \

firewall-cmd --zone=public --add-port=9345/tcp --permanent && \

firewall-cmd --zone=public --add-port=80/tcp --permanent && \

firewall-cmd --zone=public --add-port=8080/tcp --permanent && \

firewall-cmd --reload && \

firewall-cmd --zone=public --list-ports

#### 关闭selinux

 vi /etc/sysconfig/selinux

 保证如下配置-关闭SELINUX：

 SELINUX=disabled

 

#### 实施docker脚本：（1.3.1）

yum install epel-release -y

echo "[dockerrepo]" >/etc/yum.repos.d/docker.repo

echo "name=Docker Repository" >>/etc/yum.repos.d/docker.repo

echo "baseurl=https://yum.dockerproject.org/repo/main/centos/7/">> /etc/yum.repos.d/docker.repo

echo "enabled=1">> /etc/yum.repos.d/docker.repo

echo "gpgcheck=1">> /etc/yum.repos.d/docker.repo

echo "gpgkey=https://yum.dockerproject.org/gpg">>/etc/yum.repos.d/docker.repo

yum install docker-engine-1.13.1-1.el7.centos –y

 

 

systemctl enable docker.service

systemctl start docker

echo '{"registry-mirrors": ["http:\/\/b1ca9cdc.m.daocloud.io"], "storage-driver":"overlay","insecure-registries":["registry.storm.io"]}' > /etc/docker/daemon.json 

 

#### 私服hosts配置(重要)

地址根据你仓库地址

echo "10.50.23.34 registry.storm.io" >> /etc/hosts 

 

 

#### 修改docker.service文件

cd /etc/systemd/system/multi-user.target.wants

vi docker.service

ExecStart=/usr/bin/dockerd --graph=/data/docker 

备注: 

--graph=/data/docker：docker新的存储位置, 强烈建议使用外挂载磁盘位置

--storage-driver=overlay ： 当前docker所使用的存储驱动 一般忽略

-- registry-mirror  : 镜像加速, 一般忽略

注：存储驱动貌似不改也会变成overlay

#### 重启docker　

systemctl daemon-reload

systemctl restart docker

注意: 可能在restart的时候会出现错误, 这时候可以重启服务器之后再试

docker info

可以进行查看对应的信息

 

 

#### 镜像拉取

先登录docker  根据jenkins项目的Configure里的shell脚本的账号密码

docker login -u admin -p Harbor12345 registry.storm.io

docker pull 

docker save

 

 

 

 

 

 