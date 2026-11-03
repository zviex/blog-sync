## 安装（podman）

```yaml
services:
  duckdb:
    image: duckdb/duckdb:latest
    container_name: duckdb-interactive
    stdin_open: true 
    tty: true
    volumes:
      - ./data:/workspace:z # 对应 -v，将当前data挂载到容器的 /workspace
      - ./raw:/import:z # 导入数据目录
    working_dir: /workspace # 对应 -w，设置工作目录
    network_mode: host 
```

## WEBUI
```bash
podman exec -it duckdb-interactive duckdb
```

```sql
CALL start_ui();
```


## 创建新表
```shell
 podman exec -it duckdb-interactive duckdb new_table.duckdb
```
