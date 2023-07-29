FROM golang:1.14.1-alpine AS gobuild-base
RUN apk add --no-cache \
	git \
	make

FROM gobuild-base AS acr-cli
WORKDIR /go/src/github.com/Sviatik/acr-cli
RUN git clone https://github.com/Sviatik/acr-cli.git .
RUN make binaries && mv bin/acr /usr/bin/acr

FROM ubuntu:20.04
# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN DEBIAN_FRONTEND=noninteractive apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get upgrade -y

# Install Docker Engine (latest stable version) and Docker Compose 
RUN apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        wget \
        software-properties-common \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    && apt-get update \
    && apt-get install docker-ce \
    && curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" \
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

# Install kubectl
RUN curl -LO https://dl.k8s.io/release/v1.27.4/bin/linux/arm64/kubectl \
    && chmod +x ./kubectl \
    && mv ./kubectl /usr/local/bin/kubectl

# Install helm
RUN curl -L https://get.helm.sh/helm-v3.8.1-linux-amd64.tar.gz | tar xz && mv linux-amd64/helm /bin/helm && rm -rf linux-amd64

# Install trivy
RUN wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | apt-key add - \
    && echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | tee -a /etc/apt/sources.list.d/trivy.list \
    && apt-get update \
    && apt-get install trivy

# install yq
RUN wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq \
    && chmod +x /usr/bin/yq

# ==========Install and launch Azure DevOps Agent==========
RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-utils \
    git \
    iputils-ping \
    jq \
    libcurl4 \
    libicu66 \
    libssl1.0 \
    libunwind8 \
    lsb-release \
    netcat \
    software-properties-common \
  && rm -rf /var/lib/apt/lists/*

RUN curl -LsS https://aka.ms/InstallAzureCLIDeb | bash \
  && rm -rf /var/lib/apt/lists/*

ENV TARGETARCH=linux-x64

WORKDIR /azp

COPY --from=acr-cli /usr/bin/acr /usr/bin/acr
COPY ./start.sh .
RUN chmod +x start.sh

ENTRYPOINT [ "./start.sh" ]