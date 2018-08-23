# Docker笔记
### 18.06.0-ce
docker-containerd 默认会占用10010端口,需要disabled_plugins["cri"]
但目前以服务方式启动docker,会重新覆盖配置文件,因此无效.
需等待补丁发布.	--2018-08-06
> docker-containerd --config /var/run/docker/containerd/containerd.toml

