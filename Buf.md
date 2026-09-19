### Buf

buf是一个更为现代，高效的protobuf管理器，他支持比如代码生成，lint，格式化等等功能。

## 安装

使用go install快速安装

```bash
go install github.com/bufbuild/buf/cmd/buf@v1.59.0
```

## 初始化项目

```bash
buf config init
```
初始化后会提供一个buf.yaml

```yaml
# For details on buf.yaml configuration, visit https://buf.build/docs/configuration/v2/buf-yaml

version: v2
modules:
    - path: pb
lint:
    use:
        - STANDARD
breaking:
    use:
        - FILE
```

注意里面有一个modules，buf 默认以buf.yaml所在文件夹为module。但是有时候可能我们会用某个目录把pb文件包起来，这个时候就要使用module - path来指定pb文件所在的目录。

## 代码生成

需要配置一个buf.gen.yaml的文件

```yaml
version: v2
managed:
	enabled: true
    override:
        - file_option: go_package_prefix
          value: github.com/bufbuild/buf-examples/gen
plugins:
    - remote: buf.build/protocolbuffers/go
      out: gen
      opt: paths=source_relative
    - remote: buf.build/connectrpc/go
      out: gen
      opt: paths=source_relative
inputs:
    - directory: pb
```
这个文件控制了buf的一些属性，在go中，对于override里面的file_option，这个配置决定了生成的go文件的包的地址，也就是我们普通的go生成的go_package属性
```
option go_package=""
```
这个里面还涉及到了一个connectrpc的插件，buf这里可以直接管理远程插件，connectrpc是一个rpc框架，支持直接从pb生成rpc服务文件。

## 生成API测试工具的全量proto文件

对于如buf这类protobuf管理工具生成的protobuf文件，让比如postman这类工具需要导入proto文件进行测试时，尤其是当buf还连接了一些远程的proto插件时候。需要进行导出为protobuf文件夹才能进行接口测试。但是并不是说下载我们使用的protobuf文件即可，因为buf自带远程管理的插件，当我们有远程插件时候，这个时候在postman中机会报错，提示远程的这些proto文件找不到，这个时候在我们buf仓库或者文件夹的根目录，也就是有buf.yml的目录执行以下命令

```bash
buf export . --output /path/postman-protos
```

使用buf的导出功能，把所有proto文件导出到一个文件夹，然后再使用postman工具导入文件夹就不会出现这种问题了。