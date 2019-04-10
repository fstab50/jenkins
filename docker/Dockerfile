##############################################################################
##                                                                          ##
##    RPM Build Dockerfile: Jenkins Base image                              ##
##                                                                          ##
##    Purpose:                                                              ##
##       Build container for Jenkins Server Headless Setup                  ##
##                                                                          ##
##    Originally written by:                                                ##
##       "Blake Huber" <blakeca00@@gmail.com>                               ##
##                                                                          ##
##############################################################################

FROM maven:3.5.4-jdk-8 as builder
MAINTAINER Amazon AWS

RUN yum -y update; yum clean all
RUN yum -y groups mark convert
RUN yum -y groupinstall "Development Tools"

RUN yum install -y gcc gcc-c++ \
                   libtool libtool-ltdl \
                   make cmake \
                   git vim jq \
                   pkgconfig which util-linux \
                   sudo man-pages \
                   automake autoconf \
                   yum-utils shadow-utils wget && \
    yum clean all

# user operations
RUN export USER='dblake'
RUN useradd $USER -d /home/$USER -u 1000 -m -G users,wheel

COPY .mvn/ /jenkins/src/.mvn/
COPY cli/ /jenkins/src/cli/
COPY core/ /jenkins/src/core/
COPY src/ /jenkins/src/src/
COPY test/ /jenkins/src/test/
COPY war/ /jenkins/src/war/
COPY *.xml /jenkins/src/
COPY LICENSE.txt /jenkins/src/LICENSE.txt
COPY licenseCompleter.groovy /jenkins/src/licenseCompleter.groovy
COPY show-pom-version.rb /jenkins/src/show-pom-version.rb

WORKDIR /jenkins/src/
RUN mvn clean install --batch-mode -Plight-test

# The image is based on the previous weekly, new changes in jenkinci/docker are not applied
FROM jenkins/jenkins:latest

LABEL Description="This is an experimental image for the master branch of the Jenkins core" Vendor="Jenkins Project"

COPY --from=builder /jenkins/src/war/target/jenkins.war /usr/share/jenkins/jenkins.war
ENTRYPOINT ["tini", "--", "/usr/local/bin/jenkins.sh"]

# epel repository
RUN wget -O epel.rpm –nv https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
RUN yum -y install epel.rpm
RUN yum -y install figlet
RUN yum -y update; yum clean all

# install python3.6 (requires epel)
#RUN yum -y install python3.6 python36-libs python3.6-devel python3-pip python3-setuptools
#RUN pip3 install -U pip setuptools

# mount volume here to cp completed rpm to on the host
RUN mkdir /mnt/rpm
VOLUME /mnt/rpm

# configure sudoers
RUN sed -i '/Defaults    secure_path/d' /etc/sudoers
RUN echo "$USER ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

ADD ./bashrc  /home/$USER/.bashrc
RUN chown -R $USER /home/$USER

EXPOSE 8080

USER $USER

# configure home for $USER
RUN mkdir -p /home/$USER/.config/bash
ADD ./colors.sh /home/$USER/.config/bash/
ADD ./motd-amazonlinux2.sh /home/$USER/.config/bash/motd.sh

# environment variables
ENV CONTAINER=jenkinsserver OS=amazonlinux DIST=amzn2

##

# end rpm build Dockerfile
