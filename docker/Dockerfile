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

# user operations
RUN export USER='dblake'
#RUN useradd $USER -d /home/$USER -u 110 -m -G users

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

LABEL Description="branch of the Jenkins core" Vendor="Jenkins Project"

COPY --from=builder /jenkins/src/war/target/jenkins.war /usr/share/jenkins/jenkins.war
ENTRYPOINT ["tini", "--", "/usr/local/bin/jenkins.sh"]

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
ADD ./motd-centos.sh /home/$USER/.config/bash/motd.sh

# environment variables
ENV CONTAINER=jenkinsserver OS=amazonlinux DIST=amzn2

##

# end rpm build Dockerfile
