---

layout: Post
title: 搭建DST服务器傻瓜式教程
author: ZL

date: 09-06-2020

tags:
    - 服务器搭建
    - DST

Easy way to build up a Don't starve together server

Just run the command

Few problems will be listed below


---

# 搭建DST服务器傻瓜式教程

> 参考教程：[Linux/Centos7搭建饥荒服务器教程](https://blog.csdn.net/zhang41228/article/details/103106298)

-----

基于Centos7 系统搭建DST 服务器

## 步骤一

**购买服务器**  
使用了vultr 10刀/月的伦敦服务器，效果不错，可以节约成本采用5刀/月的，缺点可能会因为内存或者宽带不够而卡顿

**国内搭建**  
推荐使用[腾讯云](https://cloud.tencent.com/act/season?fromSource=gwzcw.3381379.3381379.3381379&utm_medium=cpc&utm_id=gwzcw.3381379.3381379.3381379&from=console&cps_key=3af275f3511e9383b8e9ce25be5f2cf2)或者[阿里云](https://cn.aliyun.com/minisite/goods?userCode=lozrsj6c)，有学生优惠，体量小，搭建服务器足够

## 步骤二

1. 安装服务器基本条件
   
    ``` shell
    sudo yum update

    sudo yum -y install glibc.i686 libstdc++.i686 libcurl4-gnutls-dev.i686 libcurl.i686 screen
    ```

2. 安装服务器端SteamCMD
    ``` shell
    cd /home && mkdir steamcmd && cd steamcmd

    wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz

    tar -xvzf steamcmd_linux.tar.gz
    ```

3. 执行Steam脚本登陆
    ``` shell
    ./steamcmd.sh

    login anonymous

    force_install_dir /home/dstserver

    app_update 343050 validate

    ```

    执行下面命令解决缺少关键组件的问题
    ``` shell
    ln -s /usr/lib/libcurl.so.4 /home/dstserver/bin/lib32/libcurl-gnutls.so.4k
    ```
    之后执行
    ``` shell
    cd /home/dstserver/bin

    echo "./dontstarve_dedicated_server_nullrenderer -console -persistent_storage_root /home/dstsave -conf_dir dst -cluster World1 -shard Master" > master_start.sh

    echo "./dontstarve_dedicated_server_nullrenderer -console -persistent_storage_root /home/dstsave -conf_dir dst -cluster World1 -shard Caves" > cave_start.sh

    chmod +x master_start.sh cave_start.sh
    # 700 755也可以
    ```
4. 启动服务器脚本
    master_start.sh 对应地上世界，cave_start.sh对应地下世界  
    启动一次服务器后，$/home/dstsave/World1/$就会生成一些默认的配置文件


5. 打开游戏将存档转移进服务器
   打开游戏后有一个游戏数据button，点击后打开存档位置，cluster1-5对应5个存档，将需要的存档找到，通过scp复制到服务器的World1下
   > 注意，要将Cluster文件夹下的文件放到World1文件夹下，不能把Cluster文件夹放到World1. World1就等同于是一个Cluster文件夹
   > 同样，用scp将文件复制过去，记住先清空World1 的默认配置文件

6. 获取服务器Token
    进入Klei 账号点击查看我的游戏，右上角Don't starve together servers.  
    添加服务器生成token

7. 正式启动服务器
   
   运行master_start.sh 和cave_start.sh启动服务器  
   有Sim pause就是显示成功了
    ```
    netstat -nlp |grep :10999

    netstat -nlp |grep :10998
    ```
    以上命令用于查看服务器进程  
    ```
    kill -9 portID
    ```
    显示的端口号可以用以上命令来结束，或者实在不行reboot

8. 配置mod （略）
   
9. 配置管理员（略）

### 服务器安全保护

防止服务器被暴力破解密码骇入，使用ssh-key登陆，将本机生成的public key放到服务器中，scp id_rsa.pub到服务器~/.ssh/authorized_keys文件中.  
> 注意，不要粘贴复制，会导致public key中复制进一些空字符从而导致无效。用scp更香
更改权限后，进入服务器ssh config.

``` shell
$ vim /etc/ssh/sshd_config

# 禁用root账户登录，非必要，但为了安全性，请配置
PermitRootLogin no

# 是否让 sshd 去检查用户家目录或相关档案的权限数据，这是为了担心使用者将某些重要档案的权限设错，可能会导致一些问题所致。例如使用者的 ~/.ssh/ 权限设错时，某些特殊情况下会不许用户登入
StrictModes no

# 是否允许用户自行使用成对的密钥系统进行登入行为，仅针对 version 2。至于自制的公钥数据就放置于用户家目录下的 .ssh/authorized_keys 内
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile %h/.ssh/authorized_keys

# 有了证书登录了，就禁用密码登录吧，安全要紧
PasswordAuthentication no
```

然后就可以通过简单的自己设置的密码进入服务器了



