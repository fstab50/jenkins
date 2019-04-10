##############################################################################
##                                                                          ##
##    RPM Build Dockerfile: CentOS 7 Base image                             ##
##                                                                          ##
##    Purpose:                                                              ##
##       Build container for rpm package assembly                           ##
##                                                                          ##
##    Dependencies:  file list                                              ##
##      - bashrc                          - colors.sh                       ##
##      - .rpmmacros                      - motd-centos.sh                  ##
##      - docker-buildrpm.sh                                                ##
##                                                                          ##
##    Originally written by:                                                ##
##       "Blake Huber" <blakeca00@@gmail.com>                               ##
##                                                                          ##
##############################################################################

FROM amazonlinux:2
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
RUN yum -y install rpm-build rpmdevtools

# user operations
RUN export USER='dblake'
RUN useradd $USER -d /home/$USER -u 1000 -m -G users,wheel && \
    echo "# macros"                      >  /home/$USER/.rpmmacros && \
    echo "%_topdir    /home/$USER/rpmbuild" >> /home/$USER/.rpmmacros && \
    echo "%_sourcedir %{_topdir}"        >> /home/$USER/.rpmmacros && \
    echo "%_builddir  %{_topdir}"        >> /home/$USER/.rpmmacros && \
    echo "%_specdir   %{_topdir}"        >> /home/$USER/.rpmmacros && \
    echo "%_rpmdir    %{_topdir}"        >> /home/$USER/.rpmmacros && \
    echo "%_srcrpmdir %{_topdir}"        >> /home/$USER/.rpmmacros

# epel repository
RUN wget -O epel.rpm –nv https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
RUN yum -y install epel.rpm
RUN yum -y install figlet
RUN yum -y update; yum clean all

# install python3.6 (requires epel)
RUN yum -y install python3.6 python36-libs python3.6-devel python3-pip python3-setuptools
RUN pip3 install -U pip setuptools

# mount volume here to cp completed rpm to on the host
RUN mkdir /mnt/rpm
VOLUME /mnt/rpm

# configure sudoers
RUN sed -i '/Defaults    secure_path/d' /etc/sudoers
RUN echo "$USER ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

# rpmbuild deps
RUN cd && rpmdev-setuptree
RUN mv ~/rpmbuild /home/$USER/

ADD ./bashrc  /home/$USER/.bashrc
ADD ./.rpmmacros /home/$USER/
ADD ./docker-buildrpm.sh /home/$USER/

RUN chown -R $USER /home/$USER

EXPOSE 8080

USER $USER

# configure home for $USER
RUN mkdir -p /home/$USER/.config/bash
ADD ./colors.sh /home/$USER/.config/bash/
ADD ./motd-amazonlinux2.sh /home/$USER/.config/bash/motd.sh

# environment variables
ENV CONTAINER=rpmbuild OS=amazonlinux DIST=amzn2

##

# end rpm build Dockerfile
