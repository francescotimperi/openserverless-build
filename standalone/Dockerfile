# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
#
FROM ubuntu:20.04
LABEL maintainer="Michele Sciabarra michele@nuvolaris.io, Francesco Timperi Tiberi francesco.timperi@gmail.com"
# configure dpkg && timezone
ENV TZ=Europe/London
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections ;\
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime ;\
    echo $TZ > /etc/timezone ;\
    useradd -m -d /home openserverless
# add docker and java (amazon corretto) repos
RUN apt-get update && apt-get -y upgrade &&\
    apt-get -y install \
    software-properties-common apt-utils \
    lsb-release ca-certificates \
    curl wget gpg \
    unzip vim telnet
# install java (amazon corretto)
RUN wget -O- https://apt.corretto.aws/corretto.key | apt-key add - ;\
    add-apt-repository 'deb https://apt.corretto.aws stable main' ;\
    apt-get update && apt-get -y install java-11-amazon-corretto-jdk
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu bionic stable" > /etc/apt/sources.list.d/docker.list ;\
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor > /usr/share/keyrings/docker-archive-keyring.gpg ;\
    apt-get update && apt-get -y install docker-ce-cli
RUN WSK_VERSION=1.2.0 && \
    ARCH="$(dpkg --print-architecture)" && \
    WSK_BASE=https://github.com/apache/openwhisk-cli/releases/download && \
    curl -sL "$WSK_BASE/$WSK_VERSION/OpenWhisk_CLI-$WSK_VERSION-linux-$ARCH.tgz" | tar xzvf - -C /usr/bin/    
WORKDIR /home
ENV HOME=/home
RUN mkdir -p /home/etc/ /home/lib/ /home/bin/
ADD lib/* /home/lib/
ADD bin/* /home/bin/
ADD etc/* /home/etc/
RUN chown -Rvf openserverless:root /home ;\
    chgrp -R root /home ;\
    chmod -R g=u /home
USER openserverless
ENTRYPOINT ["/bin/bash", "/home/bin/standalone"]
