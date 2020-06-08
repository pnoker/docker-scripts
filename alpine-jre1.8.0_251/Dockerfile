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

FROM alpine:3.10
MAINTAINER pnoker <pnokers.icloud.com>

ENV JAVA_HOME /usr/local/java
ENV PATH ${PATH}:${JAVA_HOME}/bin

ADD ./jre1.8.0_251 ${JAVA_HOME}
ADD ./sgerrand.rsa.pub /etc/apk/keys/sgerrand.rsa.pub
COPY ./glibc-2.29-r0.apk /opt/glibc-2.29-r0.apk

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk add --no-cache --update --update-cache ca-certificates \
    && cd /opt \
    && apk add glibc-2.29-r0.apk \
    && rm glibc-2.29-r0.apk