## 操作容器

###1.修改端口映射

>容器的运行文件存放在 /var/lib/docker/containers/【容器名称】，其中该目录下的hostconfig.json 的 PortBindings 项 为端口映射，

````
{
	"Binds": null,
	"ContainerIDFile": "",
	"LogConfig": {
		"Type": "json-file",
		"Config": {}
	},
	"NetworkMode": "default",
	"PortBindings": {
		"3306/tcp": [{
			"HostIp": "",
			"HostPort": "3308"
		}]
	},
	"RestartPolicy": {
		"Name": "no",
		"MaximumRetryCount": 0
	},
	"AutoRemove": false,
	"VolumeDriver": "",
	"略":"略"
	"CpuPercent": 0,
	"IOMaximumIOps": 0,
	"IOMaximumBandwidth": 0
}
````

>"3306/tcp": [{"HostIp": "","HostPort": "3308"}]  :容器内的3306端口 映射到主机 即宿主的3308端口

>修改相应端口后 重启容器即可 

>缺点是要停止docker 服务  ，停止所有容器运行