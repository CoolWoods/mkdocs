# tms系统文档
tms系统模块繁多，目前还没有一个统一的文档记录下各个模块的功能。本文旨在构建tms知识体系。每个模块的介绍内容包括：git仓库地址、elk地址、数据数库以及其下的主要功能模块。
## 构建
目前tms系统有两个构建平台：
1. [paas](https://paasv2.banggood.cn)平台是主要的构建平台，目前国内服务以及美仓服务都在paas构建。
2. 使用[jenkins](https://jenkins.banggood.cn)构建的服务只有英仓面单服务、澳仓面单服务以及香港仓面单服务。

## 日志及监控
目前tms主要使用elk日志，skywaorking追踪和grafana监控。
### elk日志
elk可以查询应用日志、nginx日志以及数据库慢查询日志等。elk有两个服务：
1. [国内服务](http://kibana.banggood.cn)，国内服务只需要登录OA就能使用。
2. [海外服务](https://elk.banggood.com)，海外服务需要使用账号密码登录。[账号密码](elk2019:RwZVh&@Q1SDliXtK)
#### 应用日志
查询应用日志时，需要选择`app-log*`索引，在此模式下，首先应当选择的字段为app，app的值一般是服务名称。

[app-log*](http://kibana.banggood.cn/app/kibana#/discover?_g=(filters:!(),refreshInterval:(pause:!t,value:0),time:(from:now%2Fd,to:now%2Fd))&_a=(columns:!(_source),filters:!(),index:'5f5a59b0-cfad-11ea-bb28-a5958dd4a59c',interval:auto,query:(language:kuery,query:''),sort:!(!('@timestamp',desc))))
#### nginx日志
查询nginx日志时，国内选择`oa-linux-nginx*`索引、海外选择`ingress-nginx*`索引，查询nginx日志首先应当选择http_host字段，app的值一般是服务名称。

#### 数据库慢查询日志

#### nginx日志

### skywaorking追踪

### grafana监控


## 基础信息（公共服务）
顾名思义，基础信息提供了系统中各个模板通用的信息。例如：国家、处理中心、渠道方式、渠道商、参数表等基础数据。

### git仓库
1. 主仓库地址[tms-framework](http://gitlab.banggood.cn/tms/tms-framework)
2. 子模块地址[tms-common-module](http://gitlab.banggood.cn/tms/tms-common-module)

值得注意的是基础信息仓库名称是**tms-framework**但是其服务名称却是**tmscommons**

### elk地址

## 面单项目

### 国内面单

### 海外面单

### 面单子模块

## 跟踪信息

## 搬搬

## 跟踪号截取

## 计费

## 计费配置

## 报关

## 调拨

## 对账

## 线路配置

## 订单同步

## 调拨同步