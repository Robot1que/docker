FROM node:10.15.3-jessie

# PREREQUISITES

USER root
WORKDIR /tmp
RUN useradd --user-group --shell /bin/bash --create-home agent

# INSTALL UPDATES

RUN apt update && apt --yes upgrade

# DOCKER CLIENT

ARG DOCKER_GID
RUN groupadd --gid ${DOCKER_GID} docker \
&& gpasswd --add agent docker

ENV DOCKER_VERSION=18.09.6

RUN wget -O docker.tgz --no-verbose https://download.docker.com/linux/static/stable/x86_64/docker-${DOCKER_VERSION}.tgz \
&& tar -zxf docker.tgz --no-same-owner \
&& rm docker.tgz \
&& mkdir /usr/docker \
&& mv docker/docker /usr/docker/docker \
&& rm -rf docker/
ENV PATH=$PATH:/usr/docker

# GOOGLE CHROME

RUN wget -O google-chrome.deb --no-verbose https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
&& dpkg --install google-chrome.deb || true \
&& apt-get --fix-broken --yes install \
&& rm google-chrome.deb

# POWER SHELL (PWSH)

ENV PWSH_VERSION=6.2.1

RUN url=https://github.com/PowerShell/PowerShell/releases/download/v${PWSH_VERSION}/powershell-${PWSH_VERSION}-linux-x64.tar.gz \
&& wget -O powershell.tar.gz --no-verbose $url \
&& mkdir /usr/pwsh \
&& tar -C /usr/pwsh -zxf powershell.tar.gz --no-same-owner \
&& rm powershell.tar.gz \
&& chmod o+x /usr/pwsh/pwsh

ENV PATH=${PATH}:/usr/pwsh

# INSTALL AZURE PIPELINES AGENT

ENV AGENT_VERSION=2.150.3

RUN url=https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-x64-${AGENT_VERSION}.tar.gz \
&& wget -O agent.tar.gz --no-verbose $url \
&& tar -C /home/agent/ -zxf agent.tar.gz --no-same-owner --owner=agent \
&& rm agent.tar.gz \
&& chown --recursive agent:agent /home/agent/ \
&& /home/agent/bin/installdependencies.sh

# ENTRYPOINT
USER agent
WORKDIR /home/agent
CMD ./config.sh --replace && ./run.sh

# HOT TO USE
# docker build --tag agent:node-10.15.3 --build-arg DOCKER_GID=999 .
# docker run -it -h agent01 --name agent01 -v /var/run/docker.sock:/var/run/docker.sock --privileged agent:node-10.15.3