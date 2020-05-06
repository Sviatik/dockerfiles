FROM ubuntu:16.04

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

# Install Docker Engine (latest stable version) and Docker Compose 
RUN apt-get update \
    && apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    && apt-get update \
    && apt-get install docker-ce \
    && curl -L "https://github.com/docker/compose/releases/download/1.18.0/docker-compose-$(uname -s)-$(uname -m)" \
        -o /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose

# Install cSpell
RUN apt-get install dirmngr \
    && curl -sL https://deb.nodesource.com/setup_12.x | bash - \
    && apt-get install nodejs \
    && npm install -g cspell

# Install AzCopy
RUN curl -o azcopy_v10.tar.gz https://azcopyvnext.azureedge.net/release20200124/azcopy_linux_amd64_10.3.4.tar.gz \
    && tar -C /usr/local/bin -xvf azcopy_v10.tar.gz --strip-components=1 \
    && rm -f azcopy_v10.tar.gz \
    && chmod +x /usr/local/bin/azcopy

# Install Azure Cli
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# Install kubectl
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.18.2/bin/linux/amd64/kubectl \
    && chmod +x ./kubectl \
    && mv ./kubectl /usr/local/bin/kubectl

# ==========Install and launch Azure DevOps Agent==========
RUN apt-get install --no-install-recommends \
        jq \
        git \
        iputils-ping \
        libcurl3 \
        libicu55 \
        libunwind8 \
        netcat
                   
WORKDIR /azp

COPY ./start.sh .
RUN chmod +x start.sh

CMD ["./start.sh"]