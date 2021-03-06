# 哪吒面板

服务期状态监控，被动接收（非 node-exporter 那种主动拉取的方式。）

## 一键脚本

```shell
curl -L https://raw.githubusercontent.com/naiba/nezha/master/script/install.sh -o nezha.sh && chmod +x nezha.sh
sudo ./nezha.sh
```
## 反代配置

使用反向代理时需要针对 `/ws` 路径的 WebSocket 进行特别配置以支持实时更新服务器状态。

- Nginx(宝塔)：在你的 nginx 配置文件中加入以下代码

    ```nginx
    server{

        #server_name blablabla...

        location /ws {
            proxy_pass http://ip:8008;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
            proxy_set_header Host $host;
        }

        #其他的 location blablabla...
    }
    ```

- CaddyServer v1（v2无需特别配置）

    ```Caddyfile
    proxy /ws http://ip:8008 {
        websocket
    }
    ```

## 系统设计

C/S 采用 gRPC 通信，客户端通过添加主机生成的单独 Token 上报监控信息。因为不会做成多用户的，上报信息会储存到内存中，暂不提供历史数据统计。

- 首次连接：上报基本信息（系统、CPU基本信息），后面管理员可从客户端主动拉取更新。
- 监控上报：每隔 3s 向服务器上报系统信息

## 社区文章

 - [哪吒面板：小鸡们的最佳探针](https://www.zhujizixun.com/2843.html) *（已过时）*
