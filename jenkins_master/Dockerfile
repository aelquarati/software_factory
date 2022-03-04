FROM jenkins/jenkins:lts-alpine
USER root

#Edit group (ping) & add (docker) group
RUN delgroup ping
RUN addgroup -g 998 docker && adduser jenkins docker && addgroup ping

# Install packages, create jenkins log folder & add scalway cli
RUN mkdir -p /var/log/jenkins /home/jenkins \
    #&& chown -R jenkins:jenkins /var/log/jenkins /home/jenkins \
    && apk add nano sudo curl openssh-server docker openrc py3-pip py3-wheel python3-dev gcc libffi-dev libc-dev rust cargo openssl-dev \
    && curl -o /usr/local/bin/scw -L "https://github.com/scaleway/scaleway-cli/releases/download/v2.2.4/scw-2.2.4-linux-x86_64"

RUN echo "jenkins ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/users

USER jenkins

ENV JENKINS_HOME="/var/jenkins_home"
ENV PATH="$JENKINS_HOME/.local/bin:$PATH"

#docker exec jenkins-master ps -ef | grep java to if JAVA_OPTS conf is there
ENV JAVA_OPTS="-Xmx3072m -Djenkins.install.runSetupWizard=false"

#--handlerCountMax = the max no of worker threads to allow. Default is 300
ENV JENKINS_OPTS=" --handlerCountMax=300"

#JENKINS_SLAVE_AGENT_PORT => to  clean if I use ssh
#ENV JENKINS_SLAVE_AGENT_PORT=50000
ENV CASC_JENKINS_CONFIG="$JENKINS_HOME/casc_configs"

#Cfg file for ansible
ENV ANSIBLE_CONFIG="/home/jenkins/ansible.cfg"

#Copy & install plugins for jenkins
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt

#generate plugins
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt

RUN pip install ansible==4.0.0 \
    && ansible-galaxy collection install community.docker