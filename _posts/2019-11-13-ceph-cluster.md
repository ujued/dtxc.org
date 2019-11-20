---
layout: post
title:  "Ceph Cluster在CentOS7上安装"
date:   2019-11-13 16:29:41 +0800
categories: nativecloud ceph
---
> 本文旨在说明在`CentOS7`主机群上安装`Ceph`。以下脚本， 不要直接在你的机器上运行，看下面的几点说明后作出适当的修改。执行过程中，会有一般性错误，你能解决的。

1. 主机群
node1: 作为部署机，和Ceph服务主机   
node2: 作为Ceph服务主机   
node3: 作为Ceph服务主机    

2. Shell 脚本1
```shell
cat <<EOF > /etc/yum.repos.d/ceph.repo
[Ceph]
name=Ceph packages for $basearch
baseurl=https://mirrors.aliyun.com/ceph/rpm-nautilus/el7/$basearch
enabled=1
gpgcheck=1
type=rpm-md
gpgkey=https://mirrors.aliyun.com/ceph/keys/release.asc
priority=1

[Ceph-noarch]
name=Ceph noarch packages
baseurl=https://mirrors.aliyun.com/ceph/rpm-nautilus/el7/noarch
enabled=1
gpgcheck=1
type=rpm-md
gpgkey=https://mirrors.aliyun.com/ceph/keys/release.asc
priority=1

[Ceph-source]
name=Ceph source packages
baseurl=https://mirrors.aliyun.com/ceph/rpm-nautilus/el7/SRPMS
enabled=1
gpgcheck=1
type=rpm-md
gpgkey=https://mirrors.aliyun.com/ceph/keys/release.asc
priority=1
EOF

yum install -y epel-release                                      # 1
yum install ntp ntpdate ntp-doc                                  # 2
systemct enable ntpd; systemct start ntpd                        # 3
yum install -y openssh-server                                    # 4

firewall-cmd --zone=public --add-service=ceph-mon --permanent    # 5
firewall-cmd --zone=public --add-service=ceph --permanent        
firewall-cmd --reload

```

2. Shell脚本2
```shell
yum install -y ceph-deploy                                       # 6
ssh-key-gen; \
  ssh-copy-id -i ~/.ssh/id_esa.pub root@node2                    # 7
  ssh-copy-id -i ~/.ssh/id_esa.pub root@node3

mkdir my-cluster; cd my-cluster                                  # 8
ceph-deploy new node1                                            # 9

echo public network = 192.168.0.0/24 >> ceph.conf                # 10

ceph-deploy install node1 node2 node3                            # 11

ceph-deploy mon create-initial                                   # 12

ceph-deploy admin node1 node2 node3                              # 13

ceph-deploy mgr create node1                                     # 14

ceph-deploy osd create --data /dev/sdf node1                     # 15
ceph-deploy osd create --data /dev/sdf node2
ceph-deploy osd create --data /dev/sdf node3

ceph -s                                                          # 16
```

3. 2个脚本用途
第一个在 三个主机上都运行一遍，没有什么需要改的，直接运行即可。第二个脚本在Ceph部署机即node1上执行，需要看下面的说明作出改动。

4. 脚本说明
 #1 安装centos企业包  
 #2 主机时间同步服务
 #3 开启并允许开机启动时间同步服务
 #4 ssh服务（没试过，sshd不知道会不会在安装后运行）
 #5 防火墙几个主机上的Ceph通信端口放过
 #6 安装Ceph部署工具
 #7 生成ssh public key并上传到其他主机
 #8 创建部署工作目录并进入
 #9 创建集群配置文件，并把node1作为初始集群监控节点
 #10 修改ceph的公共配置文件
 #11 安装ceph相关服务工具等到各个节点
 #12 创建集群需要的一些认证文件
 #13 复制相关文件到各个主机默认位置（主机上的Ceph服务和工具会用这些文件）
 #14 创建管理节点到node1
 #15 在各个节点创建并运行存储服务，这里每个节点都需要指定一个物理块设备，`/dev/sdf`改成你的块设备
 #16 查看集群状态大概会向下面这样，当然，只要helth是HEALTH_OK，即代表集群创建成功

```
cluster:
    id:     6775bb7e-3fd6-4745-a613-4ff045e4c712
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum worker1,worker3,node1 (age 90m)
    mgr: worker3(active, since 3h), standbys: worker1, node1
    osd: 5 osds: 5 up (since 90m), 5 in (since 3d)
    rgw: 1 daemon active (worker1)
 
  data:
    pools:   6 pools, 104 pgs
    objects: 1.57k objects, 3.6 GiB
    usage:   15 GiB used, 236 GiB / 251 GiB avail
    pgs:     104 active+clean
 
  io:
    client:   29 KiB/s wr, 0 op/s rd, 2 op/s wr
```