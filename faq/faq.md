## 基于Nginx ssl搭建Nexus3的配置

```bash
location / {
  proxy_set_header        Host $http_host;
  proxy_set_header        X-Real-IP $remote_addr;
  proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header  X-Forwarded-Proto https; # 一定要加上，转发时使用https协议

  proxy_pass              http://nexus3:port;
  proxy_read_timeout      90;
}
```
