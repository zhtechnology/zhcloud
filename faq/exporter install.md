## BlackBox-exporter安装

### 容器安装

```bash
docker run  --name blackbox_exporter -d \
    -p 39115:9115 \
    -v /local path:/opt/zhtec/blackbox  \
    prom/blackbox-exporter:v0.25.0  \
    --config.file=/opt/zhtec/blackbox/blackbox.yml
```

### blackbox.yml配置文件

```yaml
modules:  
  http_get_2xx:  
    prober: http  
    http:  
      method: GET  
      valid_status_codes: [200, 201, 202, 204,301, 302, 303, 307, 308]  # 期望的HTTP状态码  
      no_follow_redirects: false  # 是否不跟随重定向  
      preferred_ip_protocol: ip4  # 首选的IP协议  
      ip_protocol_fallback: false  # 如果首选协议失败，是否回退到其他协议  
  
  http_post_2xx:  
    prober: http  
    http:  
      method: POST  # 使用POST方法  
      valid_status_codes: [200, 201, 202, 204,301, 302, 303, 307, 308]  # 期望的HTTP状态码  
      # 其他参数可以根据需要配置，如headers、body等  
  
  # 注意：PUT方法不是Blackbox Exporter默认支持的，但可以通过自定义实现  
  # 需要修改Blackbox Exporter的源代码或使用其他方式模拟PUT请求  
  # 以下是一个假设性的配置，实际使用时需要调整  
  http_put_2xx:  
    prober: http  
    http:  
      method: PUT  # 使用PUT方法  
      valid_status_codes: [200, 201, 202, 204,301, 302, 303, 307, 308]  # 期望的HTTP状态码  
      # 其他参数需要根据实际需求进行配置  
  
  tcp_connect:
    prober: tcp
  icmp_ping:
    prober: icmp
```

## Node-exporter安装

### 容器安装

```bash
docker run -d --name=node-exporter \
  --pid=host \
  -p 39100:9100 \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /:/rootfs:ro \
  bitnami/node-exporter:latest \
  --path.procfs=/host/proc \
  --path.sysfs=/host/sys  \
  --collector.processes
```
