# 快速上手

## All in one Docker image

这个镜像上为快速本地测试而设计，里面运行了Jaeger UI, collector, query, and agent，存储组件上使用了内存

通过下面这条命令可以非常容易的快速启动Jaeger的Docker镜像。这个镜像已经发布到了DockerHub上

```bash
docker run -d -e COLLECTOR_ZIPKIN_HTTP_PORT=9411 -p5775:5775/udp -p6831:6831/udp -p6832:6832/udp \
  -p5778:5778 -p16686:16686 -p14268:14268 -p9411:9411 jaegertracing/all-in-one:latest
```
Jaeger UI的访问地址是`http://localhost:16686`

这个容器使用了如下端口: 

端口 | 协议 | 组件 | 功能
---- | -------  | --------- | ---
5775 | UDP      | agent     | accept zipkin.thrift over compact thrift protocol
6831 | UDP      | agent     | accept jaeger.thrift over compact thrift protocol
6832 | UDP      | agent     | accept jaeger.thrift over binary thrift protocol
5778 | HTTP     | agent     | serve configs
16686| HTTP     | query     | serve frontend
9411 | HTTP     | collector | Zipkin compatible endpoint


## Kubernetes and OpenShift
Kubernetes and OpenShift 模板可以在Github的网站上找到： [Jaegertracing](https://github.com/jaegertracing/) 

## 应用示例

### HotROD (Rides on Demand)

This is a demo application that consists of several microservices and
illustrates the use of the [OpenTracing API](http://opentracing.io).
A tutorial / walkthough is available in the blog post:
[Take OpenTracing for a HotROD ride][hotrod-tutorial].

It can be run standalone, but requires Jaeger backend to view the
traces.

#### 运行

```bash
mkdir -p $GOPATH/src/github.com/jaegertracing
cd $GOPATH/src/github.com/jaegertracing
git clone git@github.com:jaegertracing/jaeger.git jaeger
cd jaeger
make install
cd examples/hotrod
go run ./main.go all
```

然后浏览器访问 `http://localhost:8080`.


#### 功能

-   Discover architecture of the whole system via data-driven dependency
    diagram.
-   View request time line & errors, understand how the app works.
-   Find sources of latency, lack of concurrency.
-   Highly contextualized logging.
-   Use baggage propagation to

    -   Diagnose inter-request contention (queueing).
    -   Attribute time spent in a service.

-   Use open source libraries with OpenTracing integration to get
    vendor-neutral instrumentation for free.

#### 先决条件

-   必须安装了Go 1.9或更高的版本
-   为了查看traces信息，需要运行[后台运行Jaeger](#all-in-one-docker-image)镜像

## 客户端库

请查看 [传送门](client_libraries.md).

## 运行个人的Jaeger组件
Jaeger后端组件可以通过源码的方式来运行
在`cmd`目录下包含这些组件的入口程序 `main.go`, 举个例子，运行`Jaeger-agent`的命令如下

```bash
mkdir -p $GOPATH/src/github.com/jaegertracing
cd $GOPATH/src/github.com/jaegertracing
git clone git@github.com:jaegertracing/jaeger.git jaeger
cd jaeger
make install
go run ./cmd/agent/main.go
```

## 从Zipkin迁移

Collector服务提供了公开的兼容Zipkin Rest API `/api/v1/spans` and `/api/v2/spans` ，支持JSON和thrift两个格式
但是，默认配置下上关闭的，需要通过`--collector.zipkin.http-port=9411`参数来激活使用
 
Zipkin Thrift 接口语言描述在这里： [jaegertracing/jaeger-idl](https://github.com/jaegertracing/jaeger-idl/blob/master/thrift/zipkincore.thrift).
他和Zipkin官方的 [openzipkin/zipkin-api](https://github.com/openzipkin/zipkin-api/blob/master/thrift/zipkinCore.thrift)兼容的

[hotrod-tutorial]: https://medium.com/@YuriShkuro/take-opentracing-for-a-hotrod-ride-f6e3141f7941
