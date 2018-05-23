## Docker

Docker上简单快速的EOSIO设置也是可用的.  您可以在中找到有关EOSIO Docker的最新信息 [Docker Readme](https://github.com/EOSIO/eos/blob/master/Docker/README.md).

### 安装依赖关系
 - [Docker](https://docs.docker.com) Docker 17.05 或更高是必需的
 
### 构建EOSIO映像

```bash
$ git clone https://github.com/EOSIO/eos.git --recursive
$ cd eos/Docker
$ docker build . -t eosio/eos
```

### 仅启动nodeos docker容器

```bash
$ docker run --name nodeos -p 8888:8888 -p 9876:9876 -t eosio/eos start_nodeos.sh arg1 arg2
```
默认情况下，所有数据都保存在docker volume中。 如果数据过期或损坏，可以被删除:
``` bash
$ docker inspect --format '{{ range .Mounts }}{{ .Name }} {{ end }}' nodeos
fdc265730a4f697346fa8b078c176e315b959e79365fc9cbd11f090ea0cb5cbc
$ docker volume rm fdc265730a4f697346fa8b078c176e315b959e79365fc9cbd11f090ea0cb5cbc
```

或者，您可以直接将主机目录挂载到容器中

```bash
$ docker run --name nodeos -v /path-to-data-dir:/opt/eos/bin/data-dir -p 8888:8888 -p 9876:9876 -t eosio/eos start_nodeos.sh arg1 arg2
```
### 获取链信息

```bash
$ curl http://127.0.0.1:8888/v1/chain/get_info
```

### 启动nodeos和keosd容器

```bash
$ docker-compose up
```

在`docker-compose up`之后，将启动两个名为nodeos和keosd的服务。 nodeos服务将把端口8888和9876暴露给主机。 keosd服务不会将任何端口暴露给主机，只有在keosd容器内运行cleos时才可以使用cleos，如“执行cleos命令”一节中所述.


#### 执行cleos命令

您可以通过bash别名运行`cleos`命令.

```bash
$ alias cleos='docker-compose exec keosd /opt/eos/bin/cleos -H nodeos'
$ cleos get info
$ cleos get account inita
```


上传sample  exchange智能合约

```bash
$ cleos set contract exchange contracts/exchange/exchange.wast contracts/exchange/exchange.abi
```

如果您以后不需要keosd，则可以使用停止keosd服务

```bash
$ docker-compose stop keosd
```
#### 更改默认配置

您可以使用docker撰写覆盖文件更改默认配置。 例如, 使用以下内容创建备用配置文件`config2.ini`和`docker-compose.override.yml`。

```yaml
version: "2"

services:
  nodeos:
    volumes:
      - nodeos-data-volume:/opt/eos/bin/data-dir
      - ./config2.ini:/opt/eos/bin/data-dir/config.ini
```

然后按照以下步骤重新启动Docker容器:

```bash
$ docker-compose down
$ docker-compose up
```

#### 清空 data-dir

docker-compose创建的数据卷可以删除，如下所示
```bash
$ docker volume rm docker_nodeos-data-volume
```

<a name="4-creating-and-launching-a-single-node-testnet"></a>


                                                                   

