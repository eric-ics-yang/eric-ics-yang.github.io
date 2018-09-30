### 迁移项目到kubernetes的经验
转自[博客](http://www.360doc.com/content/18/0505/08/33667232_751252111.shtml)

使用非root用户运行时。将构建时的尖端命名为build。制定构建的基础架构，如linux。
使用版本作为标签，如3.6. 如果使用latest的话，可能导致不可预知的情况。

```dockerfile
FROM golang:1.9.4 as build
WORKDIR /go/src/github.com/openfaas/faas/gateway
COPY .  .
RUN CGO_ENABLED=O GOOS=linux go build -a -installsuffix cgo -o gateway .

FROM alpine:3.6
RUN addgroup -S app \
    && adduser -S -g app app
WORKDIR /home/app
EXPOSE 8080
ENV http_proxy  ''
ENV https_proxy  ''
COPY --from=buld /go/src/github.com//openfaas/faas/gateway /gateway
COPY assets assets
RUN chown -R app:app ./
USER app
CMD ['./gateway']
```

把项目迁移到k8s的核心工作包含：
```
1.创建好的Docker镜像
2.书写好的kubernetes清单（yaml文件）。Helm分发应用。
3.使用ConfigMap将配置和代码解耦
4.使用Secret来保护API Key这样的隐私数据
5.使用liveness和readiness来实现弹性和自愈。
```
