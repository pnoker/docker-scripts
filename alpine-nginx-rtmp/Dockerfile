#
#  Copyright 2019 Pnoker. All Rights Reserved.
#
#  Licensed under the Apache License, Version 2.0 (the "License");
#  you may not use this file except in compliance with the License.
#  You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.
#

FROM alpine:3.8 AS build

ENV NGINX_VERSION nginx-1.18.0
ENV NGINX_RTMP_MODULE_VERSION 1.2.1

COPY ./nginx-1.18.0.tar.gz /tmp/build/nginx/${NGINX_VERSION}.tar.gz
COPY ./nginx-rtmp-module-1.2.1.tar.gz /tmp/build/nginx-rtmp-module/nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk add --no-cache --update --update-cache ca-certificates libgomp zlib-dev openssl-dev libc-dev pcre-dev gcc make expat
    
# 下载 Nginx
RUN mkdir -p /tmp/build/nginx \
    && cd /tmp/build/nginx \
    && tar -zxf ${NGINX_VERSION}.tar.gz

# 下载 Nginx Rtmp Module
RUN mkdir -p /tmp/build/nginx-rtmp-module \
    && cd /tmp/build/nginx-rtmp-module \
    && tar -zxf nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz \
    && cd nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}

# 安装Nginx和Rtmp Module
RUN cd /tmp/build/nginx/${NGINX_VERSION} \
    && ./configure \
          --prefix=/usr/local/nginx \
          --with-http_ssl_module \
          --with-threads \
          --with-ipv6 \
          --add-module=/tmp/build/nginx-rtmp-module/nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION} \
    && make -j $(getconf _NPROCESSORS_ONLN) \
    && make install \
    && mkdir /var/lock/nginx \
    && rm -rf /tmp/build

FROM alpine:3.8 AS release
MAINTAINER pnoker <pnokers.icloud.com>

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && mkdir -p /usr/local/nginx \
    && apk add --no-cache --update ca-certificates openssl pcre

COPY plugs/wait-for /bin/
COPY --from=build /usr/local/nginx /usr/local/nginx
COPY ./nginx.conf /usr/local/nginx/conf/nginx.conf

EXPOSE 1935
VOLUME /var/log/nginx

CMD ["/usr/local/nginx/sbin/nginx", "-g", "daemon off;"]
