##连接elastic 服务器失败 `es client connect failed:no active connection found: no Elasticsearch node available`
####参考 https://blog.csdn.net/m1126m/article/details/108751132

<br>

###原因 golang 使用 github.com/olivere/elastic 包进行连接 远程 es 或者docker搭建的es时(只要golang所在主机与es所在主机不同)，报错信息如下
`es client connect failed:no active connection found: no Elasticsearch node available`


<br>



###解决步骤
#### 第一步
##### 1.检查es node 信息

curl -s -XGET 'http://localhost:9200/_nodes/http?pretty=true’
``` json
{
  "_nodes" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "cluster_name" : "docker-cluster",
  "nodes" : {
    "SKi1VP_lQUmge5G2F0aJdA" : {
      "name" : "060cc976dcfb",
      "transport_address" : "172.19.0.2:9300",
      "host" : "172.19.0.2",
      "ip" : "172.19.0.2",
      "version" : "8.0.0",
      "build_flavor" : "default",
      "build_type" : "docker",
      "build_hash" : "1b6a7ece17463df5ff54a3e1302d825889aa1161",
      "roles" : [
        "data",
        "data_cold",
        "data_content",
        "data_frozen",
        "data_hot",
        "data_warm",
        "ingest",
        "master",
        "ml",
        "remote_cluster_client",
        "transform"
      ],
      "attributes" : {
        "ml.machine_memory" : "16791990272",
        "xpack.installed" : "true",
        "ml.max_jvm_size" : "268435456"
      },
      "http" : {
        "bound_address" : [
          "0.0.0.0:9200"
        ],
        "publish_address" : "172.19.0.2:9200",
        "max_content_length_in_bytes" : 104857600
      }
    }
  }
}
```
##### 发现 publish_address（172.17.0.2） 地址是一个内网地址，无法正常访问，所以 会报错上面的信息，无可用的node节点

<br>

### 解决方法(两种)

##### 1.改变golang代码初始化client时的参数.

```go
client, err := elastic.NewClient(elastic.SetSniff(false),elastic.SetURL(host))  新增参数 elastic.SetSniff(false), 用于关闭 Sniff
````


##### 2.调整es node 的 publish_address 配置，新增 network.publish_host: 127.0.0.1, 整体配置文件如下:


````yml
    cluster.name: "docker-cluster"
    network.host: 0.0.0.0
    network.publish_host: 127.0.0.1 # 新增配置项
````
            

