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
# Myriad - YARN Resource Manager
#
# VERSION 0.1.0

FROM centos:7
MAINTAINER Apache Myriad dev@myriad.incubator.apache.org

ENV YARN_USER="yarn"
ENV YARN_UID="108"
ENV HADOOP_GROUP="hadoop"
ENV HADOOP_GID="112"
ENV YARN_GROUP="yarn"
ENV YARN_GID="113"
ENV HADOOP_VER="2.7.1"
ENV HADOOP_HOME=/usr/local/hadoop
ENV JAVA_HOME=/usr
ENV MESOS_NATIVE_LIBRARY=/usr/local/lib/libmesos.so
ENV MESOS_VER=1.2.0

COPY yumrepos/* /etc/yum.repos.d/
COPY gpgkey/*  /etc/pki/rpm-gpg/
COPY hadoop-2.7.1.tar.gz /root/

# Setup mesosphere repositories
RUN yum -y update
RUN yum install -y java-1.7.0-openjdk-devel.x86_64 wget redhat-lsb which mesos curl tar


# Run local YARN install
ADD myriad-bin/install-yarn.sh /install-yarn.sh
RUN sh /install-yarn.sh
ADD myriad-bin/configure-yarn.sh /configure-yarn.sh
RUN /configure-yarn.sh

# Copy over myriad libraries
ADD ./libs/* /usr/local/hadoop/share/hadoop/yarn/lib/

# Initialize hadoop confs with env vars
ADD myriad-etc/* /usr/local/hadoop/etc/hadoop/
RUN chown -R root:root /usr/local/hadoop/etc

# Initialize hadoop confs with env vars
ADD myriad-bin/run-myriad.sh /run-myriad.sh
RUN mkdir /myriad-conf/ && chmod -R 777 /usr/local/hadoop/etc/hadoop/ && cp /usr/local/hadoop/etc/hadoop/* /myriad-conf/

#Configure directory permissions for yarn
RUN mkdir /var/yarn/ && mkdir /var/yarn/rm/ && mkdir /var/yarn/rm/system/ && mkdir /var/yarn/rm/system/FSRMStateRoot && chown -R yarn:yarn /var/yarn

# Run the YARN resource manager
USER root
CMD sh /run-myriad.sh
