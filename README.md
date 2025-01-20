# StreamShield Proxy: pixman HLS MPD流媒体播放代理工具

## 简介

StreamShield Proxy 的目标是解决因 IP 限制而无法直接播放 pixman.io 上的 4gtv.m3u 等流媒体文件的问题。Cloudflare 免费版的流媒体代理功能缺失，并且存在封号风险，因此本项目利用个人 多个VPS（例如甲骨文的 ARM 服务器）作为CDN多源聚合代理，流畅转发所需流量，显著简化了流媒体播放的配置流程。它尤其适用于家中没有卵路由器或难以安装复杂配置环境的用户。

## 核心功能

- 智能代理：支持 4gtv、Beesport、MyTVSuper、TheTV、AKTV 等流媒体源
- 内容聚合：可选集成央视频、中国移动iTV等多个直连源（年久失修，国内的建议直接部署肥羊或者沐晨大佬本地docker）
- 多源支持：支持多VPS部署，形成CDN网络
- 安全加固：集成安全token机制
- 简易部署：提供Docker Compose一键部署方案
- 跨平台：支持arm64和amd64架构
- 自定义导入：支持自定义M3U导入（需Pixman Docker环境）

## 发展现状

最新版 StreamShield Proxy 已集成绝大多数 Pixman 渠道，并支持自定义 M3U 导入，除了 YouTube。

支持Thetv的HLS加密方式。由于pixman的thetv年久失修只能自己改轮子，经过YanG大佬点拨，使用了歪果同胞做的一个docker作为thetv的playlist源https://github.com/dtankdempse/thetvapp-m3u。新增 THETV_SOURCE 环境变量支持 TheTV 源配置;
部署方法一把梭 docker run --name thetv -d -p xxxx:4124 dtankdemp/thetvapp-m3u:latest 先获得thetv的新的m3u

💖 **特别鸣谢：** 衷心感谢 AKTV 的作者！他们发布的程序让大家能更方便地收看海外电视台。本项目已将该程序打包进 Docker，实现对 AKTV 源的支持，并在自己的 VPS 上进行转发。

**最新版本 1.4.2 已完美支持 AKTV 源，并提供相关环境变量配置。请参考下文“定制环境变量”部分获取 `AKTV_HOST`、`AKTV_PORT` 和 `AKTV_EXTERNAL_URL` 的详细说明。**

## 重要事情说三遍 docker thetv只支持美国加拿大IP的vps
## 重要事情说三遍 docker thetv只支持美国加拿大IP的vps
## 重要事情说三遍 docker thetv只支持美国加拿大IP的vps


## 终端播放器

在 Android 环境下，您需要使用 [https://github.com/FongMi/Release/tree/fongmi/apk/release](https://github.com/FongMi/Release/tree/fongmi/apk/release) 支持 mpd 加密解码播放。

Telegram channel https://t.me/tvb_ys

# Docker Compose部署指南

一键部署只支持在美国加拿大vps部署，才能正常收看thetv，别的ip机器部署完不能播放thetv。如想分别部署thetv和播放在不同vps上 可以在美国vps上安装thetv docker 然后自行修改env和yml里的地址和端口号

## 前置条件

- Docker 和 Docker Compose安装完成

## 克隆本仓库
```bash
git clone https://github.com/pppyyyccc/streamshield-proxy.git

cd streamshield-proxy
```

## 配置

编辑 .env 文件，设置您的个人配置：
```bash
  nano .env
```
## 主要配置项：

- VPS_HOST: 设置为您的服务器 IP 或域名
- SECURITY_TOKEN: 设置一个安全的访问令牌
- MYTVSUPER_TOKEN: 如果使用 MyTVSuper，填入您的令牌
- AKTV_HOST: AKTV 服务器的IP/host(去掉http前缀！）
- AKTV_PORT: AKTV 服务的端口号

主动修改端口号，根据需要调整其他配置项。

根据需要调整其他配置项。

## 使用
启动服务 在项目目录中运行：
```bash
  docker-compose up -d
 ``` 
## 访问
在ok影视终端播放器内-设置-直播 填入以下格式的 URL 访问您的频道列表：

  http://[您的服务器IP或域名]:[STREAMSHIELD_PORT]/[SECURITY_TOKEN]
## 例如：
  http://100.100.100.100:4994/your_security_token 

## ⚠️ **强烈建议用 NGINX HTTPS 反向代理一下播放地址和aktv的地址，要注意防止被白嫖。反向代理设置要注意关闭HTTP2，aktv程序不支持HTTP2**


## 设置定时更新 mytvsuper_tivimate.m3u 文件： 为自动化运行，每日早晚执行更新。或遵循 https://pixman.io/topics/17 手动调整。
```bash
(crontab -l 2>/dev/null | grep -v "docker exec pixman sh -c 'flask mytvsuper_tivimate'"; echo "0 5,17 * * * docker exec pixman sh -c 'flask mytvsuper_tivimate'") | crontab -
  ```  
确保所有需要的端口都已开放（默认为 4994, 5000, 4124）
检查 .env 文件中的配置是否正确
如果遇到任何问题，请在 Issues 页面提出。


# Docker 部署指南
1. **预置** Pixman Docker 镜像：

   [https://pixman.io/topics/17](https://pixman.io/topics/17)

2. **加载** StreamShield Docker 镜像：

```bash
   docker pull ppyycc/streamshield-proxy:latest
```
设置定时更新 mytvsuper_tivimate.m3u 文件：
为自动化运行，每日早晚执行更新。或遵循 https://pixman.io/topics/17 手动调整。
(crontab -l 2&gt;/dev/null | grep -v "docker exec pixman sh -c 'flask mytvsuper_tivimate'"; echo "0 5,17 * * * docker exec pixman sh -c 'flask mytvsuper_tivimate'") | crontab -



## 启动 Docker 容器
```bash
docker run -d -p 4994:4994 --name streamshield-proxy \
    -e CUSTOM_DOMAIN="http://100.100.100.100:5000" \
    -e VPS_HOST="http://200.200.200.200:4994" \
    -e SECURITY_TOKEN="test11" \
    -e INCLUDE_MYTVSUPER="free" \
    -e CHINAM3U="true" \
    --restart always \
    ppyycc/streamshield-proxy:latest 
```
## 定制环境变量

| 变量 | 描述 |
| --- | --- |
| CUSTOM_DOMAIN | 已运行pixman docker的URL，不需附带 m3u 后缀，已包含 YSP 和 4GTV 等聚合。示例：http://1.1.1.1:5000 或 https://bb.bb.bb |
| VPS_HOST | 个人的 VPS HOST，支持 HTTP/HTTPS，可以是 IP 地址或定制域名。示例：http://2.2.2.2:4994 或 https://cc.cc.cc |
| INCLUDE_MYTVSUPER="true"/"free" | 是否启用 mytvsuper_tivimate.m3u 渠道加载，缺省不加载。有自己的key就用ture，没有key就用free看12个免费电视台 |
| DEBUG="true" | 是否要开启DEBUG， 不写这个扩展默认不开启。 |
| SECURITY_TOKEN="testtoken" | 输入自己设置的安全token防止扫到端口被爆破。 |
| CHINAM3U="true" | 是否要开启大陆电视台， 不写这个扩展默认不开启。 |
| CUSTOM_M3U=“test.m3u”| 是否要开倒入自定义M3U，名字和pixman docker内一致。 |
| CUSTOM_M3U_PROXY="true" | 是否要用本程序代理自定义M3U流量，不写这个扩展默认默认不开启自定义M3U代理。 |
| CUSTOM_M3U_PROXY_HOST | 写入自定义M3U需要代理的host，方便程序识别并代理。 |
| EXTRA_M3U_URLS | 写入多个别的VPS订阅地址进行多源聚合，多源优先级按照写入先后排定优先级，本机优先级在最后。 |
| AKTV_HOST | 	(1.4.2 新增)AKTV 服务器的主机名。例如：10.10.10.10或者aktv.xxx.xxx |
| AKTV_PORT | (1.4.2 新增)AKTV 服务的端口号。例如：30001 |
| AKTV_EXTERNAL_URL | 	(1.4.2 新增)可选，AKTV 服务的外部访问地址（例如：https://aktv.yourdomain.com |
| INCLUDE_ADULT_CONTENT | 	(1.4.3 新增)可选,m默认关闭 |
| THETV_SOURCE | 写入新的thetv m3u https://thetv.example.com/playlist 或者 http://thetv.example.com:xxxx/playlist 不写默认不加载thetv |




## 部署案例


### 仅使用 IP 同一VPS地址部署pixman和代理以及加载thetv和aktv新的播放源头：
```bash
docker run --name thetv -d -p 41244:4124 dtankdemp/thetvapp-m3u:latest
docker pull ppyycc/streamshield-proxy:latest \
docker run -d -p 8888:4994 --name streamshield-proxy \
-e CUSTOM_DOMAIN="http://100.100.100.100:5000" \
-e VPS_HOST="http://100.100.100.100:8888" \
-e SECURITY_TOKEN="test11" \
-e INCLUDE_MYTVSUPER="true" \
-e CHINAM3U="true"
-e THETV_SOURCE='http://100.100.100.100:41244/playlist' \
-e AKTV_HOST="100.100.100.100" \
-e AKTV_PORT="30001" \
-e AKTV_EXTERNAL_URL="https://aktv.yourdomain.com" \
--restart always \
ppyycc/streamshield-proxy:latest
```

访问地址：http://100.100.100.100:8888/test11， 并已自动导入 mytvsuper_tivimate.m3u，并且能收看大陆电视台。


### 仅使用 IP 地址部署并只看free mytvsuper：

```bash
docker pull ppyycc/streamshield-proxy:latest \
docker run -d -p 8888:4994 --name streamshield-proxy \
-e CUSTOM_DOMAIN="http://100.100.100.100:5000" \
-e VPS_HOST="http://200.200.200.200:8888" \
-e SECURITY_TOKEN="test22" \
-e INCLUDE_MYTVSUPER="free" \
-e CHINAM3U="true"
--restart always \
ppyycc/streamshield-proxy:latest
```

访问地址：http://200.200.200.200:8888/test22， 并已自动导入 mytvfree.m3u，并且能收看大陆电视台。


### 多VPS CDN部署：
```bash
docker pull ppyycc/streamshield-proxy:latest \
docker run -d -p 444:4994 --name streamshield-proxy \
-e CUSTOM_DOMAIN="https://pixman.aaaa.com" \
-e VPS_HOST="https://iptv.bbbb.com" \
-e SECURITY_TOKEN="test33" \
-e EXTRA_M3U_URLS='https://iptv.cccc.com/test123,https://iptv.dddd.com/test123'
--restart always \
ppyycc/streamshield-proxy:latest
```

访问地址：https://iptv.bbbb.com/test33， 默认不包含 mytvsuper 频道清单，没有大陆电视台，聚合别的两个VPS作为多源源头。别的两个vps部署上可以不用加'EXTRA_M3U_URLS'参数启动，只是作为单源播放点。优先级1.cccc 2.dddd 3.bbbb
效果如下
![image](https://github.com/user-attachments/assets/a7862d7c-fec5-4d9e-9d7e-a67779ed4e7b)


## 媒体终端配置

在直播配置中，如 影视，只需填入 https://iptv.bbbb.com/testtoken 或 http://200.200.200.200:8888/testtoken 即可实现流媒体播放。


## 社区互动与支持

更多详情、问题解决或加群探讨，敬请加入 Pixman 的 Telegram 群组：https://t.me/livednowgroup


## 项目贡献

欢迎代码提交、提出功能需求与错误汇报！有意向贡献者，请访问 问题页面，参与共建。


## 使用协议

本项目遵循 MIT 开源协议。
```
