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
全新安装的duckdb没有任何存储实例，需要先创建一个名为new_table的新存储实例
```shell
 podman exec -it duckdb-interactive duckdb new_table.duckdb
```


从csv导入数据到新表my_table
```sql
CREATE TABLE my_table AS
SELECT * FROM
read_csv('/import/user_online.csv', auto_detect =true);
```
