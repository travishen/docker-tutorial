# TOC
* [Docker簡介](#docker簡介)
* [安裝](#安裝)
* [容器（Containers）](#容器containers)
* [映像檔(image)](#映像檔image)
* [持久化數據（Persisting Data）](##持久化數據persisting-data)
* [Docker Compose](#docker-compose-官方文件-)
* [Swarm Mode](#swarm-mode)
	- [Stacks](#stacks)
	- [Secrets](#secrets)
	- [Healthchecks](#healthchecks)
* [Docker Registry](#docker-registry)

![image](https://www.docker.com/sites/default/files/horizontal.png)

# Docker簡介 

* Docker是2013年由DotCloud開發的開源專案，因為軟體的成功，公司之後也改名為Docker.Inc
* 其實Docker的前身是象龜(@gordonTheTurtle)，之後改成鯨魚

### Docker的重要性

* 容器將會是未來最有影響力的基礎架構
* 不管你是開發、維運、系統管理、部屬都會須要學一下
* 成長最快的雲端技術
* Infrastructure as code

過去到現在基礎技術的發展，可以參考以下文章

* [從Mainframe到PC](http://www.dublincity.ie/story/evolution-computer-mainframes-current-technologies)
* [從Baremetal到Virtual](https://kopu.chat/2017/08/11/%E9%9B%B2%E7%AB%AF%E9%81%8B%E7%AE%97%E7%9A%84%E9%97%9C%E9%8D%B5%E5%9F%BA%E7%A4%8E%EF%BC%9A%E8%99%9B%E6%93%AC%E6%A9%9F/)
* [從Datacenter到Cloud](https://kopu.chat/2017/06/02/cloud-industry/)

Docker的優點

* 過去底層的基礎架構都是為sysops或sysadmins設計的，但Docker的解決方案考慮了developer的使用情境
* 就是快：develop faster, build faster, test faster, deploy faster, update faster, recover faster
* 使用容器來減少不同應用程式、不同系統、不同相依性的複雜度
* 大部分現存的軟體開發得少維護得多，Docker可以減輕我們維護的難度，增加開發的時間

### 相關工具
學會了Docker你可能需要多了解[其他工具](https://landscape.cncf.io/format=landscape)

### 安裝前要注意的

在不同環境下的安裝：

* Linux: 直接安裝，不過有分版本
* Windows: Win10不直接支援，需要透過virtual machine來安裝，在安裝的流程中會設定到；因為Windows Containers的出現，新的Windows Server 2016之後會開始直接支援Docker，不用再透過Linux Container
* Mac: 也是要透過vm，據說不要用brew來裝
* Cloud: Docker for GCP/AWS/Azure

看更多有關windows container：

* [Windows Containers and Docker 101](https://www.youtube.com/watch?v=066-9yw8-7c)
* [Beyond \ - the path to Windows and Linux parity in Docker](https://www.youtube.com/watch?v=4ZY_4OeyJsw)
* [Docker + Microsoft - Investing in the Future of your Applications](https://www.youtube.com/watch?v=QASAqcuuzgI)

### Stable vs Edge(beta)

開源版本的Edge每個月會更新一次，Stable大概四個月更新一次，付費的企業版本會有更穩定的支援

# 安裝

### Windows 10 Pro / Enterprise

* 使用Pro跟Enterprice的用戶算是比較吃香，會有比較好的體驗
* 請在此下載安裝[Docker for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows) 
* 命令界面(CLI)會建議使用PowerShell
* 如果你的系統已經有在使用VirtualBox或VMware，Hyper-V啟用可能會發生資源互搶的問題

### Windows 7, 8, or 10 Home Edition

* Win7, 8與Pro/Enterprise的主要差別是Hyper-V過舊不支援，Win10 Home則是沒有Hyper-V，因此要額外安裝[Docker Toolbox](https://docs.docker.com/toolbox/overview/)，然後透過VirtualBox安裝Linux VM(Linux Container)，也就是`docker-machine`
使用網路位址轉換(NAT)存取網路
* 需要把Toolbox的`http://localhost`改成`http://192.168.99.100`

### Mac

* OSX Yosemite 10.10.3以下版本的需要裝Toolbox，以上版本安裝[Docker for Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

### Linux

* **不要用系統預設的packages直接下指令安裝**，例如`apt install docker.io`，你可能會裝到過舊的版本
* 可以經由Docker automated script來安裝相依的程式，例如`curl -ssl https://get.docker.com/ | sh`
* 或者先看過[官網](https://store.docker.com/editions/community/docker-ce-server-ubuntu)上的要求來手動下載安裝檔

```
$ sudo curl -ssl https://get.docker.com/ | sh
```
Docker會動用到系統核心的功能，需要root權限來操作
你可以將使用者加入Docker group，註：有些版本的linux如Red Hat, Fedora沒有此選項，每個指令都要透過`sudo`
```
$ sudo usermod -aG docker <--username-->
$ docker version

Client:
 Version:           18.06.1-ce
 API version:       1.38
 Go version:        go1.10.3
 Git commit:        e68fc7a
 Built:             Tue Aug 21 17:24:56 2018
 OS/Arch:           linux/amd64
 Experimental:      false

Server:
 Engine:
  Version:          18.06.1-ce
  API version:      1.38 (minimum version 1.12)
  Go version:       go1.10.3
  Git commit:       e68fc7a
  Built:            Tue Aug 21 17:23:21 2018
  OS/Arch:          linux/amd64
  Experimental:     false
```

##### Docker Machine & Docker Compose
Windows和Mac會自動幫你安裝好，Linux系統則要自己安裝這兩個項目
```
$ base=https://github.com/docker/machine/releases/download/v0.14.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
```
Docker compose
```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
使用官方文件提供的指令可能會安裝到非最新的Docker compose版本，你可以到[github/docker/compose](https://github.com/docker/compose/releases)去安裝最新的版本
```
$ sudo -i curl -L https://github.com/docker/compose/releases/download/1.23.0-rc3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ sudo -i chmod +x /usr/local/bin/docker-compose
$ docker-compose version

docker-compose version 1.23.0-rc3, build ea3d406e
docker-py version: 3.5.0
CPython version: 3.6.6
OpenSSL version: OpenSSL 1.1.0f  25 May 2017
```

### 版本格式
現今版本的格式為`YY.MM`，如`18.06.0`就是2018年6月出的版本的第一個release

### 其他選項
你可以試試[Play with Docker](https://labs.play-with-docker.com/)，無須安裝任何環境就可以透過瀏覽器體驗Docker的強大，可以參考以下文章：

* [Play with Docker | 非常酷的Docker在线实验室 (上)](https://bingohuang.com/docker-labs-1/)

### 指令格式
舊的指令格式為：`docker <command> (options)`，例如`docker run`，而新的Docker指令的格式已改成：
```
docker <command> <sub-command> (options)
```
例如`docker run`現在要寫成`docker container run`，不過舊的指令格式目前還能使用

安裝完成你可以試著執行以下指令：

* `docker`: 檢視所有管理選項
* `docker version`: 確認你可以跟docker engine溝通，和檢查你的版本號
* `docker info`: 檢視docker engine的各項設定值
* `docker image ls`: 檢視本機所有映像檔
* `docker pull <-- IMAGE -->`: 取得映像檔

# 容器（Containers）

### 什麼是容器
容器是基於特定映像檔所創造出來的程序

### 映像檔(image)和容器(container)的差別

* 映像檔是我們想執行的應用程式的模板，一個映像檔可以包含一個完整的作業系統環境，裡面安裝了需要的應用程式，同一個映像檔可以建立多個容器
* 容器是從映像檔建立的執行實例，可以被啟動、開始、停止、刪除
* 大部分的映像檔都可以從[Docker Hub](hub.docker.com)下載並取用

### 基本指令

```
$ docker container run --publish 80:80 --name webhost1 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
f17d81b4b692: Pull complete 
d5c237920c39: Pull complete 
a381f92f36de: Pull complete 
Digest: sha256:b73f527d86e3461fd652f62cf47e7b375196063bbbd503e853af5be16597cb2e
Status: Downloaded newer image for nginx:latest
```
1. 從Docker Hub下載nginx映像檔
2. 建立一個映像檔實例（容器），命名為webhost1並執行
3. Port Forwarding: 啟用主機的80 port並將容器內部使用的80 port映射到主機上

註：

* nginx server預設的對外端口是80 port
* 你可以更改要映射到主機的哪個port，例如`8888:80`，然後用`localhost:8888`訪問

加入detach參數只在背景執行，並回傳容器的id
```
$ docker container run --publish 80:80 --detach --name webhost2 nginx
6950bd542f5d281cddd7691c5032d4424d7f72585e7e36623028212ddebb4490
```

顯示目前所有容器
```
$ docker container ls -a
6950bd542f5d        nginx               "nginx -g 'daemon of…"   3 minutes ago       Up 3 minutes               0.0.0.0:80->80/tcp   webhost2
eb05ed09c19c        nginx               "nginx -g 'daemon of…"   3 hours ago         Exited (0) 3 minutes ago                        webhost1
```

關閉正在運行的容器
```
$ docker container stop 6950bd542f5d
```
顯示webhost2的log
```
$ docker container logs webhost2
```
檢視webhost2的程序，或檢查**是否有正在運行的webhost2**
```
$ docker container top webhost2
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                18871               18852               0                   13:59               ?                   00:00:00            nginx: master process nginx -g daemon off;
systemd+            18917               18871               0                   13:59               ?                   00:00:00            nginx: worker process
```
強制關閉運行中的容器
```
$ docker container rm -f 6950bd542f5d
```

#### 以上這些指令發生了什麼事

`docker container run`

1. 尋找特定的映像檔快取，找不到該映像檔的話會從遠端倉庫(remote image repo)尋找（預設是Docker Hub，若沒提供映像檔版本將下載最新的版本）
2. 新增映像檔實例（容器） 
3. Docker引擎會給這個容器一個實體IP(virtual ip) 
4. 啟用host的特定埠號將容器的特定埠號映射到host
5. 以映像檔的Dockerfile CMD來執行容器

### 容器(container)和虛擬機(virtual machine)的差別

很多介紹容器的文章把容器拿來和vm比較，雖然它們相似的地方很多，但事實上它們是完全不同的概念，因為：

* 容器只是程序(processes)
* 容器可用的資源是受限的
* 容器關閉=程序停止

舉個例子來說：
```
$ docker run --name mongo -d mongo
```
檢視此容器是否正在運行
```
$ docker top mongo
```
檢視所有運行中的docker容器，註：`docker ps`會對象是host，如果系統是mac或windows，需要先連到docker vm
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
2db362bba01a        mongo               "docker-entrypoint.s…"   5 minutes ago       Up 5 minutes        27017/tcp            mongo
```
檢視系統上所有程序，可以清楚的看到容器是一個系統上正在執行的程序
```
$ ps aux | grep mongo
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
999      19617  0.4  0.9 1089996 75788 ?       Ssl  14:28   0:02 mongod --bind_ip_all 
```

### 練習：開啟多個容器

1. 執行一個nginx實例，在背景執行且聽80port
2. 執行一個httpd(apche)實例，在背景執行且聽8080port
3. 執行一個mysql實例，在背景執行、密碼設定為自動產生，聽3306port
```
$ docker container run --name nginx -p 80:80 -d nginx
$ docker container run --name httpd -p 8080:80 -d httpd
$ docker container run --name mysql -p 3306:3306 -e MYSQL_RANDOM_ROOT_PASSWORD=yes -d mysql
```
檢視這三個容器的狀態
```
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                               NAMES
0e723b2571e0        mysql               "docker-entrypoint.s…"   28 seconds ago       Up 27 seconds       0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
22068e0c1ad2        httpd               "httpd-foreground"       38 seconds ago       Up 38 seconds       0.0.0.0:8080->80/tcp                httpd
45b3a7dc26c9        nginx               "nginx -g 'daemon of…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp                  nginx
```
檢視mysql密碼
```
$ docker container logs mysql
Initializing database
...
Database initialized
MySQL init process in progress...
...
GENERATED ROOT PASSWORD: eiCheu9Wae3ohmooxaebooR7quohphai
...
MySQL init process done. Ready for start up.
...
```

### 基本指令：監控執行中的容器

檢視容器的設定(metadata)，會回傳json陣列
```
$ docker container inspect <-- container id or container name -->
```
檢視所有容器的即時狀態(live performance)
```
$ docker container stats
```

### 基本指令：在容器中使用終端機
* `run -t`: Allocate a pseudo-TTY
* `run -i`: Keep STDIN open even if not attached

根據格式`docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]`，在指令後面可以再帶入`[COMMAND]`及參數`[ARG...]`，以nginx為例，預設程式(default program)是`nginx`，參數是`-g`、`'daemon off;'`：
```
{
  ...
  "Path": "nginx",
  "Args": [
      "-g",
      "daemon off;"
  ],
  ...
}
```
加入bash參數來改變預設程式，進入bash shell之後exit，可以看到容器隨之停止：
```
$ docker container run --name proxy -it nginx bash
root@e78f7e17edbc:/# exit
$ codker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
e78f7e17edbc        nginx               "bash"              25 seconds ago      Exited (0) 5 seconds ago                       proxy
```
這邊可以看出預設的程式變成bash：
```
{
  ...
  "Path": "bash",
  "Args": [],
  ...
}
```

* `start -a`: Attach STDOUT/STDERR and forward signals
* `start -i`： Attach container's STDIN

重啟容器，注意這裡一樣會開啟bash shell因為我們建立容器時就把command改成bash了：
```
$ docker container start -ai proxy
root@e78f7e17edbc:/#
```
如果容器正在執行中，我們如何透過殼程式操作呢（這很常用，在容器執行時debug或設定參數）：

* `exec`: 在執行中的容器上執行額外的程序
* `exec -t`: Allocate a pseudo-TTY
* `exec -i`: Keep STDIN open even if not attached
```
$ docker container run --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -d  mysql
$ docker container exec -it mysql bash
```
另外要注意的是，並非所有映像檔都有bash程式，例如Linux的超迷你分支alpine[[ 延伸閱讀 ](https://blog.wu-boy.com/2015/12/a-super-small-docker-image-based-on-alpine-linux/)]：
```
$ docker container run --it alpine bash
docker: Error response from daemon: OCI runtime create failed: container_linux.go:348: starting container process caused "exec: \"bash\": executable file not found in $PATH": unknown.
```
要使用bash要先透過alpine內建的殼程式sh來安裝
```
docker container run -it alpine sh
/ # apk add bash
/ # bash
bash-4.4# 
```

### Docker背後的網路運作

docker讓你可以建立虛擬網路(virtual network)，並將container加到網路內，建立起屬於你自己應用程式的網路拓墣
[[來源]](http://blog.maxkit.com.tw/2017/04/dcoker-network-bridge.html)
```
$ docker container run --name nginx -p 80:80 -d nginx 
$ docker container port nginx
80/tcp -> 0.0.0.0:80
```
docker daemon運作的時候，會建立三個網路
```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
81978b8f756d        bridge              bridge              local
4bad3f6e57ed        host                host                local
da7c350f0dc0        none                null                local
```
當你建立容器時，使用的ip跟主機並不同，例如以下例子：我的主機內部ip是`192.168.43.63`，而我的nginx容器的內部ip則是`172.17.0.2`
```
$ docker container inspect --format '{{ .NetworkSettings.IPAddress }}' nginx
172.17.0.2
$ ip addr show wlp58s0
3: wlp58s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 9c:b6:d0:f1:f2:55 brd ff:ff:ff:ff:ff:ff
    inet 192.168.43.63/24 brd 192.168.43.255 scope global dynamic wlp58s0
       valid_lft 2871sec preferred_lft 2871sec
    inet6 fe80::6228:4a16:e94a:7822/64 scope link 
       valid_lft forever preferred_lft forever
```
它們之間透過docker birdge network的模式（預設），此模式透過一個叫docker0的NAT server來掌管容器的網路連線
![image](https://cdn-images-1.medium.com/max/800/1*csmPlbZX1WkBVyn4IiKMuw.png)
[[圖片來源]](https://medium.com/@tomdeore/docking-a-docker-container-part-3-networking-edd779f068cb)

透過以下指令可以看出bridge網路裡有哪些容器

```
$ docker network inspect bridge
{
...
  "Containers": {
      "b8b8b86a8825166954069c7884d8ed8181234f5399a9d8e7bac860493c5a132f": {
          "Name": "nginx",
          "EndpointID": "b80f6f698bcf305689752dd9356ecc14c114275caa28ff7cd9815545d92f74be",
          "MacAddress": "02:42:ac:11:00:02",
          "IPv4Address": "172.17.0.2/16",
          "IPv6Address": ""
      }
  },
...
}
```
新增一個網路(預設是bridge driver)，然後建立新的容器
```
$ docker network create new_net
$ docker container run --name new_nginx -d --network new_net nginx
```
連結容器至多個網路
```
$ docker network connect new_net nginx
$ docker container inspect --format '{{ .NetworkSettings.Networks }}' nginx
map[bridge:0xc4205300c0 my_app_net:0xc420530180]
```

#### DNS設置
容器隨著不同設置會改變其狀態，容器間用ip位址來連線是不可靠的，因此我們會需要DNS server

先來看看新網路的DNS能不能運作
```
$ docker network insepct new_net
{
...
 "Containers": {
     "1a6c3e7c03caba424cbc5d3cbbdb9e86fd5c39741f5af1835d7446b1758e35a7": {
         "Name": "nginx",
         "EndpointID": "3958183cb1514d597067aeeb01931ab3e62ce4267848f4471c5c465cecbd6b91",
         "MacAddress": "02:42:ac:14:00:03",
         "IPv4Address": "172.20.0.3/16",
         "IPv6Address": ""
     },
     "e285eb6071b133177db6ec2cc922b01b1acfd7000199c927659fd4e0f023742f": {
         "Name": "new_nginx",
         "EndpointID": "3d451d2884418b5ee7efb2311530ceb255a86688148e9ec637f280fd7f24289c",
         "MacAddress": "02:42:ac:14:00:02",
         "IPv4Address": "172.20.0.2/16",
         "IPv6Address": ""
     }
 },
...
}

$ docker container exec -it new_nginx bash
root@e285eb6071b1:/# apt-get update 
root@e285eb6071b1:/# apt-get install -y iputils-ping
root@e285eb6071b1:/# exit

$ docker container exec -it new_nginx ping nginx
PING nginx (172.20.0.3) 56(84) bytes of data.
64 bytes from nginx.new_net (172.20.0.3): icmp_seq=1 ttl=64 time=0.074 ms
64 bytes from nginx.new_net (172.20.0.3): icmp_seq=2 ttl=64 time=0.121 ms
```
同樣在nginx容器安裝ping套件後，測試能不能雙向溝通
```
$ docker container exec -it nginx ping new_nginx
PING new_nginx (172.20.0.2) 56(84) bytes of data.
64 bytes from new_nginx.new_net (172.20.0.2): icmp_seq=1 ttl=64 time=0.063 ms
64 bytes from new_nginx.new_net (172.20.0.2): icmp_seq=2 ttl=64 time=0.071 ms
```
註：如果我以上的測試在預設的bridge network做的話會出現錯誤訊息`Name or service not known`，原因是預設的bridge網路並沒有內建的DNS server，容器要連線必須手動使用`--link`指令為容器設定連線到bridge網路。**建議建立新的網路**來省去這一步驟

### 練習：快速更新Linux分支的CLI套件

1. 分別檢查容器不同分支的Linux上的curl版本
2. 分別在centos:7和ubuntu:14.04的容器中開啟終端機

分別取得各版本的映像檔
```
$ docker image pull centos:7
$ docker image pull ubuntu:14.04
```
建立ubuntu映像檔實例，開啟終端機並檢查curl版本
```
$ docker container run --name ubuntu -it --rm ubuntu:14.04
root@66c81252095f:/# curl --version
bash: curl: command not found
root@66c81252095f:/# apt-get update && apt-get install -y curl

```
`container run --rm`: exit之後預期容器被移除
```
root@66c81252095f:/# exit
$ docker container ls -f "name=ubuntu"
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
建立centos映像檔實例，開啟終端機並檢查curl版本
```
$ docker container run --name ubuntu -it --rm centos:7
root@66c81252095f:/# curl --version
curl 7.29.0 (x86_64-redhat-linux-gnu) libcurl/7.29.0 NSS/3.34 zlib/1.2.7 libidn/1.28 libssh2/1.4.3
root@66c81252095f:/# yum update curl
root@66c81252095f:/# exit
```

### 練習:輪替式DNS(DNS Round Robin aka poor man's load balancer)

1. 建立一個network包含兩個elasticsearch:2的映像檔實例
2. 將兩個容器的網路拓墣別名(network-alias)都命名為search

```
$ docker network create new_net
$ docker pull elasticsearch:2

$ docker container run --name es1 -d --network new_net --network-alias search elasticsearch:2
$ docker container run --name es2 -d --network new_net --network-alias search elasticsearch:2
```
透過一個內網centos容器來測試連線

`curl -s`: Silent mode. Don't output anything
```
$ docker container run --name centos --network new_net -it --rm centos:7
[root@570493921bec /]# curl -s search:9200
{
  "name" : "Stuntmaster",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "dRpxX5qNQTqm-RDSKy8gaw",
  "version" : {
    "number" : "2.4.6",
    "build_hash" : "5376dca9f70f3abef96a77f4bb22720ace8240fd",
    "build_timestamp" : "2017-07-18T12:17:44Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.4"
  },
  "tagline" : "You Know, for Search"
}
[root@570493921bec /]# curl -s search:9200
{
  "name" : "Midas",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "k-8YUFb5TrCFpOqQWLP2Xg",
  "version" : {
    "number" : "2.4.6",
    "build_hash" : "5376dca9f70f3abef96a77f4bb22720ace8240fd",
    "build_timestamp" : "2017-07-18T12:17:44Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.4"
  },
  "tagline" : "You Know, for Search"
}
```

# 映像檔(image)
 
什麼是映像檔？根據官方定義跟我隨翻：
 
> An image is an ordered collections of root filesystem changes and the corresponding execution parameters for use within a container runtime

> 映像檔 = 檔案系統變動的有序集合 + 執行一個實例時相對應的執行參數

* 不是作業系統、沒有內核(kernal)、沒有核心模組（kernal module）[[ 延伸閱讀 ]](http://linux.vbird.org/linux_basic/0540kernel.php)
* 體積非常輕量，小則幾KB（golang static binary），大則幾GB（ubuntu distro + apache + php）

### Docker Hub
目前最多人用的映像檔的集散地(image registry)，映像檔repo分成`official`和`non-official`，映像檔會用`tag`做區別（正確來說tag不算版本或分支），舉例來說，官方的nginx repo：

* `1.15.5`, `mainline`, `1`, `1.15`, `latest` (mainline/stretch/Dockerfile)
* `1.15.5-perl`, `mainline-perl`, `1-perl`, `1.15-perl`, `perl` (mainline/stretch-perl/Dockerfile)
* `1.15.5-alpine`, `mainline-alpine`, `1-alpine`, `1.15-alpine`, `alpine` (mainline/alpine/Dockerfile)

分別pull下來，可以看出同一個映像檔(相同image id)可以有多個tags
```
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              dbfc48660aeb        2 weeks ago         109MB
nginx               1                   dbfc48660aeb        2 weeks ago         109MB
nginx               alpine              aae476eee77d        4 weeks ago         17.7MB
```
 
Docker Hub的操作跟Github很類似，可以fork其他repo或是上傳新的repo，你所需要的資訊也都能在detail page或github上找到。
* 映像檔可以從Github或Bitbucket的repo來建置，從首頁點選`Create Automated Build`連結帳戶即可
	- 可以選擇你的映像檔要從哪個分支來build
	- 對於dockerfile的`FROM`指令，Docker Hub可以設定Repository Links，設定連結後，來源映像檔如果有偵測到更新，你的映像檔就會跟著rebuild
	- 自定義Rebuild的Trigger

### Union檔案系統(Union file system) && 映像檔的資料層(Image layers) 
映像檔的形成並非一個大的資料區塊，例如當我pull nginx:alpine時，可以看到有些資料層我已經有了
```
$ docker pull nginx:alpine
alpine: Pulling from library/nginx
4fe2ade4980c: Already exists 
c3f09dfaf47d: Pull complete 
83283d0e9bb9: Pull complete 
e2e530da9538: Pull complete
```
透過`image history`來檢視映像檔的資料更動紀錄：
```
$ docker image histroy nginx:alpine
IMAGE               CREATED             CREATED BY                                      SIZE
aae476eee77d        4 weeks ago         /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B                  
<missing>           4 weeks ago         /bin/sh -c #(nop)  STOPSIGNAL [SIGTERM]         0B                  
<missing>           4 weeks ago         /bin/sh -c #(nop)  EXPOSE 80/tcp                0B                  
<missing>           4 weeks ago         /bin/sh -c #(nop) COPY file:1d1ac3b9a14c94a7…   1.09kB              
<missing>           4 weeks ago         /bin/sh -c #(nop) COPY file:af94db45bb7e4b8f…   643B                
<missing>           4 weeks ago         /bin/sh -c GPG_KEYS=B0F4253373F8F6F510D42178…   13.3MB              
<missing>           4 weeks ago         /bin/sh -c #(nop)  ENV NGINX_VERSION=1.15.5     0B                  
<missing>           5 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B                  
<missing>           7 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B                  
<missing>           7 weeks ago         /bin/sh -c #(nop) ADD file:25c10b1d1b41d46a1…   4.41MB   
```
註：IMAGE ID為`<missing>`只是為了區別——這些資料層不完整代表這個映像檔，只是被這個映像檔所用

所有映像檔都是繼承於特定基礎映像檔（blank layer, scratch），再往上繼承堆疊，不同映像檔之間可以共享基礎的檔案系統層，提升儲存效率 [[ 延伸閱讀 ]](https://philipzheng.gitbooks.io/docker_practice/content/underly/ufs.html)

![image](https://tech.peoplefund.co.kr/assets/img/2017-04-03-Docker/docker-layer-system.png) [[ 圖片來源 ]](https://tech.peoplefund.co.kr/2017/04/03/what-is-docker-eng.html)

![image](https://nvisium.com/articles/2014/2014-10-15-docker-cache-friend-or-foe/docker-filesystems-multilayer.png) [[ 圖片來源 ]](https://nvisium.com/blog/2014/10/15/docker-cache-friend-or-foe.html)

透過`image inspect`來檢視映像檔的metadata，可以看到這個映像檔開了哪些port、有哪些環境變數以及建立的時候會執行的指令等
```
$ docker image inspect nginx:alpine
{
    "ContainerConfig": {
        ...
        "ExposedPorts": {
            "80/tcp": {}
        },
        "Env": [
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
            "NGINX_VERSION=1.15.5"
        ],
        "Cmd": [
            "/bin/sh",
            "-c",
            "#(nop) ",
            "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
        ],
        ...
    },
}
```
fork一份映像檔到自己的repo
```
$ docker login
$ docker image tag nginx:alpine ssivart/nginx
$ docker image tag ssivart/nginx ssivart/nginx:testing
docker image push ssivart/nginx:testing
```
註：

* 在遠端機器使用`docker login`的時候，操作完要登出才會移除機器上儲存的密碼
* 欲上傳private映像檔要先建立一個私人的repo

### Dockerfile基本指令

**範例1**

```
FROM debian:stretch-slim

ENV NGINX_VERSION 1.13.6-1~stretch
ENV NJS_VERSION   1.13.6.0.1.14-1~stretch

RUN apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y gnupg1 \
	&& \
	NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; \
	found=''; \
	for server in \
		ha.pool.sks-keyservers.net \
		hkp://keyserver.ubuntu.com:80 \
		hkp://p80.pool.sks-keyservers.net:80 \
		pgp.mit.edu \
	; do \
		echo "Fetching GPG key $NGINX_GPGKEY from $server"; \
		apt-key adv --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY" && found=yes && break; \
	done; \
	test -z "$found" && echo >&2 "error: failed to fetch GPG key $NGINX_GPGKEY" && exit 1; \
	apt-get remove --purge -y gnupg1 && apt-get -y --purge autoremove && rm -rf /var/lib/apt/lists/* \
	&& echo "deb http://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list \
	&& apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y \
						nginx=${NGINX_VERSION} \
						nginx-module-xslt=${NGINX_VERSION} \
						nginx-module-geoip=${NGINX_VERSION} \
						nginx-module-image-filter=${NGINX_VERSION} \
						nginx-module-njs=${NJS_VERSION} \
						gettext-base \
	&& rm -rf /var/lib/apt/lists/*

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
```

* `FROM`: 開頭一定要加的，指定一個**Base Image**來初始化，通常是取用較輕量的分支如alpine
* `ENV`: 設定環境變數
* `RUN`: 每行指令都會往上建築新的**layer**(new layer on top)，上面的範例用`&&`來連結每一行指令是常見的作法
* `EXPOSE`: 容器沒有任何預設開啟的TCP/UDP埠號，而加了也**不代表**這些埠號會自動打開，要開啟埠號還是要透過`container run -p`
* `CMD`: 容器執行或停止的時候都會執行的指令
註：docker能自動幫我們處理logging，上面的範例將log送到stdout跟stderr，讓docker可以捕捉到這些log然後做後續處理

在dockerfile所在的目錄下build image
```
$ docker image build -t customnginx:customtag .
```
稍微更改一下dockerfile再安裝一次，可以看出union file system怎麼運作的，dockfile頂端指令會動到的資料幾乎沒什麼變動：`Using cache`，而越接近後頭的指令，會有較多的變動
```
$ docker image build -t customnginx:customtag .
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM debian:stretch-slim
 ---> 4b4471f624dc
Step 2/7 : ENV NGINX_VERSION 1.13.6-1~stretch
 ---> Using cache
 ---> 5993fe34a11a
Step 3/7 : ENV NJS_VERSION   1.13.6.0.1.14-1~stretch
 ---> Using cache
 ---> b1b9fa913700
Step 4/7 : RUN apt-get update 	&& apt-get install --no-install-recommends --no-install-suggests -y gnupg1 	&& 	NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; 	found=''; 	for server in 		ha.pool.sks-keyservers.net 		hkp://keyserver.ubuntu.com:80 		hkp://p80.pool.sks-keyservers.net:80 		pgp.mit.edu 	; do 		echo "Fetching GPG key $NGINX_GPGKEY from $server"; 		apt-key adv --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY" && found=yes && break; 	done; 	test -z "$found" && echo >&2 "error: failed to fetch GPG key $NGINX_GPGKEY" && exit 1; 	apt-get remove --purge -y gnupg1 && apt-get -y --purge autoremove && rm -rf /var/lib/apt/lists/* 	&& echo "deb http://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list 	&& apt-get update 	&& apt-get install --no-install-recommends --no-install-suggests -y 						nginx=${NGINX_VERSION} 						nginx-module-xslt=${NGINX_VERSION} 						nginx-module-geoip=${NGINX_VERSION} 					nginx-module-image-filter=${NGINX_VERSION} 						nginx-module-njs=${NJS_VERSION} 						gettext-base 	&& rm -rf /var/lib/apt/lists/*
 ---> Using cache
 ---> 10da6c1aa8b6
Step 5/7 : RUN ln -sf /dev/stdout /var/log/nginx/access.log 	&& ln -sf /dev/stderr /var/log/nginx/error.log
 ---> Using cache
 ---> 9e33d35615e8
Step 6/7 : EXPOSE 80 443 8080
 ---> Running in 1a79c9e87968
Removing intermediate container 1a79c9e87968
 ---> efa93a97a5e1
Step 7/7 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in d53275212fcd
Removing intermediate container d53275212fcd
 ---> 873793860fdf
Successfully built 873793860fdf
Successfully tagged customnginx:customtag
```

**範例2**
```
FROM nginx:latest

WORKDIR /usr/share/nginx/html

COPY index.html index.html
```

* 如果你能從更末端的映像檔來build，例如`FROM`官方的nginx再做一些客製化，在維護dockerfiles時就會更加容易。如範例A到範例B
* `WORKDIR`: 意同`RUN cd /some/path`，不過使用`WORKDIR`會更好
* `COPY`: 這個範例用本地的index.html複寫原本nginx的index.html
* 這個範例並沒有改寫`CMD`，這個指令會繼承`nginx:latest`的command

建立一個容器，訪問主頁面時預期會看到客製後的index.html
```
$ docker image build -t customnginx:customtag .
$ docker container run --rm -p 80:80 customnginx:customtag
```

### 練習：建立Dockerfile(alpine + node.js + tini)
```
FROM node:6-alpine  

RUN apk update \
    && apk add --update tini \
    && mkdir -p /usr/src/app

WORKDIR /usr/src/app

COPY package.json /usr/src/app/

RUN npm install && npm cache clean --force

COPY . /usr/src/app/ 

EXPOSE 3000

CMD ["/sbin/tini", "--", "node", "./bin/www"]
```
```
$ docker image build -t node:tini .
$ docker container run -p 80:3000 node:tini
```
發布到Docker Hub
```
$ docker image tag node:tiny ssivart/node:tini
$ docker image push ssivart/node
```

# 持久化數據（Persisting Data）

容器的設計理念有兩個特性:
1. `immutable intrastucture`: 當你需要更改設定，皆是透過重新建立新的容器
2. `ephemeral`：無狀態，代表容器可以被關閉、銷毀或取代

延伸閱讀：
[Pets vs Cattle Analogy](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/)

容器的特性帶來可靠性(iability)和一致性(consistency)，不過相對地帶來一個問題，即如何維持持久化數據（如資料庫），針對這種關注點分離(Seperation of concerns, SOC)的架構設計問題，Docker提供了幾個方案：

1. Volumes: 由Docker管理，存儲在宿主機的某個地方（在linux上是/var/lib/docker/volumes/）。非Docker應用程序不能改動這一位置的數據。Volumes是Docker最好的數據持久化方法
2. Bind mounts: 數據可以存放在宿主機的任何地方。數據甚至可以是重要的系統文件或目錄。非Docker應用程序可以改變這些數據；適用於local 開發測試
3. tmpfs mounts: 數據只存儲在宿主機的內存中，不會寫入到宿主機的文件系統
[[ 來源 ]](https://michaelyou.github.io/2017/09/17/Docker%E6%95%B0%E6%8D%AE%E7%AE%A1%E7%90%86-Volume%EF%BC%8C-bind-mount%E5%92%8Ctmpfs-mount/)

![image](https://docs.docker.com/storage/images/types-of-mounts-volume.png)

### Volumes
先來看一下mysql:8 Dockerfile的`VOLUME`指令
```
VOLUME /var/lib/mysql
```
意即當mysql的容器建立的時候，docker會在本機上新增一個`volume location`然後跟容器裡的數據目錄互通，這兩個路徑指向host同一個位址，資料只有靠手動方式才能移除，不會隨著容器被移除而消失

建立一個mysql容器然後看metadata
```
$ docker container run --name mysql -d -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql
$ docker container inspect mysql
{
	...
        "Mounts": [
            {
                "Type": "volume",
                "Name": "c7eaf7ddfa3ad5d90abbe0372d628f97ddcc2384dae45a35d60c74bfdac37416",
                "Source": "/var/lib/docker/volumes/c7eaf7ddfa3ad5d90abbe0372d628f97ddcc2384dae45a35d60c74bfdac37416/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
	...
	"Config": {
		...
		"Volumes": {
			"/var/lib/mysql": {}
		},
		...
	},
}
```
可以看到data儲存在主機`/var/lib/docker/volumes/c7eaf7ddfa3ad5d90abbe0372d628f97ddcc2384dae45a35d60c74bfdac37416/_data`的位置

而這在使用上會比較不友善：沒辦法從volume看出哪個容器是連結到自身
```
$ docker volume ls
DRIVER              VOLUME NAME
local               0ad326a8fbd002aed0d6ab977140b9ac45a3b056f9885d6cc308b981ff599e4c
local               0ef3e7bc6e024c53b166fb6a2b5a1dead036d703f40608f7094c93569016fd62
```
這裡稍微改善的方式是用參數`-v`為volume命名（以專案來命名之類的）
```
$ docker container run --name mysql -d -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
$ docker volume ls
DRIVER              VOLUME NAME
local               mysql-db
```
source的path也變得比較乾淨易讀
```
...
"Source": "/var/lib/docker/volumes/mysql-db/_data",
...
```

另外Volume也可以是匿名的(anonymous volume)，會分配一個隨機的名字，在同一個主機中不會重覆[[ 官方文件 ]](https://success.docker.com/article/different-types-of-volumes)

### Bind Mounting

* host優先於container
* 不能在Dockerfile裡使用，只能透過`container run`
* 通常是用`-v`指令，或`--mount`，格式為`/path/host:/path/container`，如
```
$ docker container run -v /Users/ssivart/stuff:/path/container  # linux / mac
$ docker container run -v //c/Users/ssivart/stuff:/path/container  # windows
```
#### 範例
新增一個nginx容器，把`/home/ssivart/桌面`目錄mount至容器的`/usr/share/nginx/html`
```
$ cd /home/ssivart/桌面
$ docker container run --name nginx -d -p 80:80 -v $(pwd):/usr/share/nginx/html nginx
```
接著我在`/home/ssivart/桌面`新增一個index.html，內容為`<h1>Hello World! Bind Mount</h1>`
```
$ vim index.html
```
預期在localhost:80會看到我更改過的首頁內容

### `-v`與`--mount`的差別[[ 文件 ]](https://docs.docker.com/storage/bind-mounts/#differences-between--v-and---mount-behavior)
現在這兩個指令差別僅在於如果host上目錄不存在，使用`-v`會幫你建立新的目錄，使用`--mount`會顯示錯誤

### 練習：postgres版本更新用Volumes保持數據
```
$ docker container run --name pg1 -d -v psql:/var/lib/postgresql/data postgres:9.6.1-alpine
$ docker container stop pg1
$ docker container run --name pg2 -d -v psql:/var/lib/postgresql/data postgres:9.6.2-alpine
```
```
$ docker volume ls
DRIVER              VOLUME NAME
local               psql
```
```
$ docker container logs pg1
...
LOG:  database system is ready to accept connections
LOG:  autovacuum launcher started
LOG:  received smart shutdown request
LOG:  autovacuum launcher shutting down
LOG:  shutting down
LOG:  database system is shut down

$ docker container logs pg2
LOG:  database system was shut down at 2018-11-05 08:07:27 UTC
LOG:  MultiXact member wraparound protections are now enabled
LOG:  database system is ready to accept connections
LOG:  autovacuum launcher started
```

練習：使用Bind Mount架設ruby + jekyll[[ 來源 ]](https://hub.docker.com/r/bretfisher/jekyll-serve/~/dockerfile/)
```
$ docker container run -p 80:4000 -v $(pwd):/site -it bretfisher/jekyll-serve sh
/site # bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java
/site # bundle exec jekyll serve --force_polling -H 0.0.0.0 -P 4000
```

# Docker Compose[[ 官方文件 ]](https://docs.docker.com/compose/compose-file/)

* 應用程式常常需要結合多個容器如SQL、proxy、網頁和後端排程等，docker compose便是用來設置容器之間的關係
* 一鍵完成
* 可以透過Docker Swarm 1.13以上版本部屬compose file

Docker compose包含兩個部份：

1. `docker-compose.yml`: YAML格式的文件來設定容器、網路、Volumes的hierarchy

	* 有區分版本，如1, 2, 2.1
	* 用`-f`來命名，預設是`docker-compose.yml`
	
2. `docker-compose`: 命令列(CLI)工具用來測試compose file

以下是compose file範例：
```yaml
version: '3.1'  # 預設是v1，但v1功能有限，建議最少設為v2

services:

  servicename: # 容器名稱，這個名稱就是容器的DNS name
    image: # 非必須，如果你需要build映像檔使用
    command: # 非必須, 同docker container run [OPTIONS] IMAGE [COMMAND]的[COMMAND]
    environment: # 非必須, 同docker run的-e
    volumes: # 非必須, 同docker run的-v
    
  servicename2:

volumes: # 非必須, 同docker volume create

networks: # 非必須, 同docker network create
```
一個簡單的proxy network設定如下
```yaml
services:
  proxy:
    image: nginx:1.13 # this will use the latest version of 1.13.x
    ports:
      - '80:80' # expose 80 on host and sent to 80 in container
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
  web:
    image: httpd  # this will use httpd:latest
```

### docker compose CLI
* 請參考上方提到的安裝流程
* 僅用於開發測試端，非正式部屬使用

常用指令
* `docker-compose up`: 設定volumes/networks，執行所有容器
* `docker-compose down`: 停止並移除所有容器，移除所有volumes/networks

### 練習: drupal + postgres架站
```yaml
version: '3'

services:
  psql:
    image: postgres:latest
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: mypassword
    restart: always

  web:
    image: drupal:latest # this will use the latest version
    ports:
      - '8080:80' # expose 80 on host and sent to 80 in container
    volumes:
      - drupal-modules:/var/www/html/modules 
      - drupal-profiles:/var/www/html/profiles 
      - drupal-sites:/var/www/html/sites 
      - drupal-themes:/var/www/html/themes 
    links:
      - psql:postgres

volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
```

### 使用compose來建置(build)客製化映像檔
* 使用`docker-compose up`，如果找不到該映像檔的cache，會在當下建置
* re-build使用`docker-compose up --build`或`docker-compose build`

先來看以下compose範例：
```yaml
version: '2'

# based off compose-sample-2, only we build nginx.conf into image
# uses sample site from https://startbootstrap.com/template-overviews/agency/

services:
  proxy:
    build:
      context: .
      dockerfile: nginx.Dockerfile
    image: nginx-custom
    ports:
      - '80:80'
  web:
    image: httpd
    volumes:
      - ./html:/usr/local/apache2/htdocs/
```
當proxy容器執行時，會先在cache找nginx-custom這個映像檔，找不到會build，這邊示範了在`context`提供的目錄下用`dockerfile`來建置映像檔
```
$ docker-compose down
$ docker image ls
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
nginx-custom    latest              4e0717a21563        6 minutes ago       109MB
```
執行完`down`，docker並不會主動移除客製化映像檔，針對要移除上面範例經過命名的映像檔，`down`指令加上`--rmi all`參數，如果是不提供映像檔名稱，docker會以`<bulid directory>_<container name>`的規則來命名映像檔，可以透過`--rmi local`移除

### compose文件配置

**這個部份待更新**
針對部署端的compose還會有許多變動（swarm、stack、secrets...），往後再陸續更新進來

```
docker-compose.yml
├── docker-compose.override.yml  # local
├── docker-compose.test.yml  # ci test
└── docker-compose.prod.yml  # deploy
```
docker-compose會自動辨識`.override.yml`的檔案，`.test.yml`及`.prod.yml`或其他自訂的compose file則需要手動透過指令`-f`來操作

本地開發端
```
$ docker-compose up
```
CI測試
```
$ docker-compose -f docker-compose.yml -f docker-compose.test.yml up -d
```
部署通常會用`config`輸出成一個完整的compose file
```
$ docker-compose -f docker-compose.yml -f docker-compose.prod.yml config > production.yml
$ docker-compose -f production.yml
```

# Swarm Mode

* Swarm用來做(叢集)架構管理
* Swarm內建於Docker
* 多個Docker host組成1個Swarm mode
* Swarm程式獨立於Docker，在Docker環境下透過swarmkit運行（須先啟用）

### Node
Swarm中的Docker的實例（Docker host）

![image](https://docs.docker.com/engine/swarm/images/tls.png)

* Worker:
	- 負責容器的執行
* Manager:
	- 在Swarm中透過Raft Database的設定進行協調與同步
	- 負責管理Worker與協調容器的部署工作
	- 也可以當Worker，Manager可以想像成有Swarm控制權限的Worker
	
兩個角色可以互換，node之間透過雙向TLS(mutual Transport Layer Security, 前身是SSL)協定溝通

![image](https://docs.docker.com/engine/swarm/images/swarm-diagram.png)



### Service and Task

* Task: Container + Command（怎麼run這個容器）
* Service: Task A + Task B + Task C（任務的堆疊），基於`docker run`的再封裝

Swarm會確保services持續運作

> 在新的處事方式上，服務器被編好號，就像牛在牛群中。比如，www001到www100。當一個服務器宕機了，它將會被取出替換上線 —— Randy Bias

![image](https://docs.docker.com/engine/swarm/images/services-diagram.png)

### Swarm運作

![image](https://foxutech.com/wp-content/uploads/2017/07/docker-service.png) [[ 圖片來源 ]](https://foxutech.com/deploy-application-image-using-docker-service/docker-service-2/)

## 基本指令

`docker swarm init`可以新增一個swarm，其中完成這些動作[[ 文件 ]](https://docs.docker.com/engine/swarm/how-swarm-mode-works/pki/)：

1. 初始公開金鑰基礎架構(public key infrastructure, PKI)
    - Docker先扮演第一個Manager node(root manager)
    - 產生一組root certificate authority(.ca)
    - 產生token: worker token + manager token
    - 其他node可以用這組token加入(join)swarm 
2. 初始Raft database
    - 儲存憑證
    - 在control plane讓Manager之間共享log，透過TLS
    - 儲存config data[[ 文件 ]](https://docs.docker.com/engine/swarm/configs/)
  
初始後可以看到第一個manager node
```
$ docker swarm init
$ docker swarm ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
uvemtlsi7743iqxih5tkwt9v8 *   xps13               Ready               Active              Leader             
```

建立一個service，指定task的容器執行的指令及參數(`docker run`)
```
$ docker service create --name myservice -d alpine ping 8.8.8.8
$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               
u2br67fsvqsi        myservice           replicated          1/1                 alpine:latest   
```

檢視service的task，可以看到一個執行中的容器
```
$ docker service ps myservice
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
sfge35webgpo        myservice.1         alpine:latest       xps13               Running             Running about a minute ago   
```

更新service的參數，提高task數量(replicas)來sacle up
```
$ docker service update myservice --replicas 3
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 

$ docker service ps myservice
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
sfge35webgpo        myservice.1         alpine:latest       xps13               Running             Running 8 minutes ago                        
57mukap2p6ck        myservice.2         alpine:latest       xps13               Running             Running 30 seconds ago                       
mfui4zrgdop4        myservice.3         alpine:latest       xps13               Running             Running 30 seconds ago
```

手動停止其中一個容器，service會再add一個task到service queue取代之，確保同時會有3個正常運作的task
```
$ docker container rm -f myservice.1.sfge35webgpo0nnmejuqsbvnz
$ docker service ps myservice
1o792ip8k75i        myservice.1         alpine:latest       xps13               Running             Running 6 seconds ago                                 
sfge35webgpo         \_ myservice.1     alpine:latest       xps13               Shutdown            Failed 11 seconds ago   "task: non-zero exit (137)"   
57mukap2p6ck        myservice.2         alpine:latest       xps13               Running             Running 4 minutes ago                                 
mfui4zrgdop4        myservice.3         alpine:latest       xps13               Running             Running 4 minutes ago      
```  

要終止task必須移除整個service，同時會把當中所有的程序清理掉
```
$ docker service rm myservice
```

### 更新services
指令範例
```
$ docker service scale web=2
$ docker service update --image nginx:1.13.6 web
$ docker service update --publish-rm 80 --publish-add 8080:80 web
```

service更新其中的tasks都會重建，task建立時docker會挑負載較小的node做較多分配，所以對service做**force update**可以平衡node之間的負載
```
$ docker service update --force web
```

### 使用GCP Compute Engine Instance Group來試作多節點Swarm

```
node-group-1-5188:~$ docker swarm init
Swarm initialized: current node (jxq78ujvpd196whvzos74vqjg) is now a manager.

node-group-1-c1m7:~$ docker swarm join --token SWMTKN-1-0hjsesqene40g6w9pw5usrcfti72bwhqwwccp57xclcozqtn5
h-ab4c4ihrdaekal7sbpww3ga38 10.132.0.2:2377
This node joined a swarm as a worker.
```

worker node沒有swarm的控制權
```
node-group-1-c1m7:~$ docker node ls
Error response from daemon: This node is not a swarm manager. Worker nodes can't be used to view or modify cluster sta
te. Please run this command on a manager node or promote the current node to a manager.
```

提昇host的worker成manager，node狀態變成Reachable
```
node-group-1-5188:~$ docker node update --role manager node-group-1-c1m7
node-group-1-5188:~$ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGIN
E VERSION
jxq78ujvpd196whvzos74vqjg *   node-group-1-5188   Ready               Active              Leader              18.09
.0
exs01fiaqanf0efveae7d610c     node-group-1-c1m7   Ready               Active              Reachable           18.09
.0
```

### Overley Network
跨Node間的網路拓樸

```
node-group-1-5188:~$ docker network create --driver overlay mydrupal
node-group-1-5188:~$ docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=example postgres
node-group-1-5188:~$ docker service create --name drupal --network mydrupal -p 80:80 drupal
```

psql在node1，drupal在node2，node2可以透過dns name:psql來存取node1的資料庫，用80 port連到node1也能看到drupal的歡迎頁面
```
node-group-1-5188:~$ docker service ps psql
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE     
       ERROR               PORTS
w35eiu38yfii        psql.1              postgres:latest     node-group-1-5188   Running             Running 10 minutes
 ago   
 
node-group-1-5188:~$ docker service ps drupal
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE     
           ERROR               PORTS
0blpaalwe5qi        drupal.1            drupal:latest       node-group-1-c1m7   Running             Running about a mi
nute ago                       
```

### Routing Mesh
* ingress(incomming) network
* stateless
* load balancer in OSI layer 3(TCP), not in layer 4(DNS)
* 使用linux既有的IPVS(IP Virtual Server)實現load balancing

1. 無論訪問網路中的哪個節點，即使該節點上沒有運行該service的副本，最終都能訪問到該service
舉例來說，如果後端資料庫有3個副本，當前端web server要取資料時，並非直接訪問某個資料庫副本的ip，而是透過swarm為所有service搭建的Virtual IP(VIP)

2. 外部流量導向所有節點共同監聽的public port

![image](https://storage.googleapis.com/ssivart/super9-blog/docker-swarm-vip.png)

![image](https://docs.docker.com/engine/swarm/images/ingress-routing-mesh.png)

### 練習：voting app[[ dockersamples ]](https://github.com/dockersamples/example-voting-app)

![image](https://raw.githubusercontent.com/dockersamples/example-voting-app/master/architecture.png)

首先先建立前後端的網路，跨node要用overlay模式
```
$ docker network create --driver overlay backend
$ docker network create --driver overlay frontend
```

```
$ docker service create --name vote --network frontend --replicas 3 -p 80:80 dockersamples/examplevotingapp_vote:before
$ docker service create --name redis --network frontend redis:3.2
$ docker service create --name worker --network frontend --network backend dockersamples/examplevotingapp_worker
$ docker service create --name db --network backend --mount type=volue,source=/var/lib/postgresql/data postgres:9.4
$ docker service create --name result --network backend -p 5001:80 dockersamples/examplevotingapp_result:before
```

```
ssivart@ds-1z84:~$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE                                          PORTS
wxh8d5b9vb45        db                  replicated          1/1                 postgres:9.4                                   
8c9t5hehhl6c        redis               replicated          1/1                 redis:3.2                                      
zka68kizvla8        result              replicated          1/1                 dockersamples/examplevotingapp_result:before   *:5001->80/tcp
gbed0pcrympq        vote                replicated          3/3                 dockersamples/examplevotingapp_vote:before     *:80->80/tcp
s0ese5a8wlna        worker              replicated          1/1                 dockersamples/examplevotingapp_worker:latest
```

# Stacks
* dockerfile版本3以上
* 適用於部署端的compose(services + volumes + overlay networks + secrets...)
* composefile指令跟local端的差異：開發端忽略`deploy`指令，部署端忽略`build`
* 部署不透過docker-compose
* 針對已經存在的service，再部署會update這些service

上述的範例只要透過一個[Composefile](https://github.com/dockersamples/example-voting-app/blob/master/docker-stack.yml)就能完成所有部署
```
$ docker stack deploy -c example-voting-app-stack.yaml voteapp

$ docker stack services voteapp
ID                  NAME                 MODE                REPLICAS            IMAGE                                          PORTS
7alt1ix5vefl        voteapp_worker       replicated          1/1                 dockersamples/examplevotingapp_worker:latest   
7zx6l55sybey        voteapp_vote         replicated          2/2                 dockersamples/examplevotingapp_vote:before     *:5000->80/tcp
bp4hdcrig4pb        voteapp_redis        replicated          1/1                 redis:alpine                                   
lbejdwn5d9je        voteapp_visualizer   replicated          1/1                 dockersamples/visualizer:stable                *:8080->8080/tcp
qvp0jwokpi40        voteapp_result       replicated          1/1                 dockersamples/examplevotingapp_result:before   *:5002->80/tcp
thzyq45ib40w        voteapp_db           replicated          1/1                 postgres:9.4   
```

swarm的可視化工具visualizer

![image](https://storage.googleapis.com/ssivart/super9-blog/visualizer.png)

### Secrets
* 儲存：
	- 使用者帳密
	- TLS憑證、金鑰
	- SSH金鑰
	- 自訂設定檔
* 支援動態字串或二進位的內容
* 儲存在Raft database，只存在於各個manager node的硬碟空間
* secrets產生後會先存在swarm再分配到特定service，只有特定的container能訪問
* 乍看之下是個實體檔案，但實際上是ramfs files system透過記憶體儲存
* secrets只適用於swarm，而swarm只適用於部署端(production)，但docker讓docker-compose也能使用，僅提供開發測試，不具真的功能(fake secure)

```
$ docker secret create psql_user psql_user.txt
zbqddocct68y2evhxy7krfxbd
$ echo "mypassword" | docker secret create psql_pass -
zy2da4ok40jxjc6t3kliw91io

$ docker secret ls
ID                          NAME                DRIVER              CREATED             UPDATED
zy2da4ok40jxjc6t3kliw91io   psql_pass                               2 minutes ago       2 minutes ago
zbqddocct68y2evhxy7krfxbd   psql_user  
```

secrets無法透過指令檢視內容（廢話），容器要存取secret得先經過指定
```
$ docker service create --secret psql_user --secret psql_pass 
```
但這樣還少了怎麼配置這些secret，docker提供一個方便的查找方式，前提是映像檔要有這些變數
```
$ docker service create --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass
```

終端機進容器檢視密碼
```
$ docker container exec -it psql.1.g2wh4ybeidc70vifm4xvtl9lf bash
root@0727ea1882ad:/# cat /run/secrets/psql_pass
mypassword
```

針對已經建立的service可以透過`update --secret-rm`或`update --secret-add`來更新secret，但這樣做會導致整個service重新部署

### secrets透過stack部署
* dockerfile版本3.1以上

```yaml
version: "3.1"

services:
  psql:
    image: postgres
    secrets:
      - psql_user
      - psql_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/psql_password
      POSTGRES_USER_FILE: /run/secrets/psql_user

secrets:
  psql_user:
    file: ./psql_user.txt
  psql_password:
    file: ./psql_password.txt
```
透過實體檔案儲存密碼的方式是有風險的，記得在部署完成後移除這些檔案

### 開發端測試secrets
* docker-compose CLI版本11以上適用
* 開發端是使用bind mount的方式把secrets mount到本機目錄，只是用於開發並無secret實際功能
* 不適用內含external用法，如果部署是用`external:true`，可以另外維護一個開發用的compose file，secret的部份改用file來寫入
```
$ docker-compose up -d
$ docker-compose exec psql cat /run/secrets/psql_pass
mypassword
```

# Healthchecks
* 回傳0(OK)或1(Error)
* 支援Dockerfile、Compose、`docker run`及swarm services
* 容器有三種健康狀態: starting, healthy, unhealthy

**container**
```
$ docker container run --name psql -d --health-cmd="pg_isready -U postgres || exit 1" postgres

$ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                    PORTS               NAMES
44d6df2dcb5a        postgres            "docker-entrypoint.s…"   38 seconds ago       Up 37 seconds (healthy)   5432/tcp            psql
```

```
$ docker container inspect psql
{
	...
	"State": {
		...
		"Health": {
                	"Status": "healthy",
			"FailingStreak": 0,
			"Log": [
			    {
				"Start": "2018-11-14T05:52:14.562405278Z",
				"End": "2018-11-14T05:52:16.32755068Z",
				"ExitCode": 0,
				"Output": "/var/run/postgresql:5432 - accepting connections\n"
			    },
			    {
				"Start": "2018-11-14T05:52:46.338006182Z",
				"End": "2018-11-14T05:52:46.588202392Z",
				"ExitCode": 0,
				"Output": "/var/run/postgresql:5432 - accepting connections\n"
			    },
			    ...
			]
		},
		...
	}
	...
}
```
**services**
```
$ docker service create --name psql -d --health-cmd="pg_isready -U postgres || exit 1" postgres
```

# Docker Registry

```
$ docker container run -d -p 5000:5000 --name registry registry
$ docker image tag hello-world 127.0.0.1:5000/hello-world
$ docker image ls
127.0.0.1:5000/hello-world   latest              4ab4c602aa5e        2 months ago        1.84kB
hello-world                  latest              4ab4c602aa5e        2 months ago        1.84kB
```

```
$ docker image push 127.0.0.1:5000/hello-world
```
push之後映像檔的實體檔案儲存在/var/lib/registry/

## Registry in Swarm mode
* 主要問題是解決nodes之間要訪問同一份映像檔
* 如果只在其中一個manager node build image，其他node是沒辦法取得該image
* 解法：
    - 使用Docker hub、AWS、Quay來儲存管理映像檔
    - 借助Routing Mesh，所有node都能藉由127.0.0.1:5000訪問
    
以下示範如何使用Routing Mesh建立registry
```
$ docker service create --name registry --publish 5000:5000 registry
$ docker pull nginx
$ docker tag hello-world 127.0.0.1:5000/nginx
$ docker push 127.0.0.1:5000/nginx
```
將映像檔push到registry後，所有nodes都能從127.0.0.1:5000取得映像檔
```
$ docker service create -p 80:80 --replicas 5 -d 127.0.0.1/nginx
```
