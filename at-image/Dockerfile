FROM openjdk:11

ARG CHROME_VERSION="google-chrome-stable"
ARG NODE_VERSION='8'

WORKDIR /tmp
RUN curl -sL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash -
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
	&& echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list \
	&& apt-get update -y \
	&& apt-get -y install --no-install-recommends \
		nodejs \
    	${CHROME_VERSION:-google-chrome-stable} \
    	g++ \
    	build-essential \
	&& rm /etc/apt/sources.list.d/google-chrome.list \
	&& rm -rf /var/lib/apt/lists/* /var/cache/apt/*

RUN wget -q -O /tmp/libpng12.deb http://mirrors.kernel.org/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb \
  && dpkg -i /tmp/libpng12.deb \
  && rm /tmp/libpng12.deb
RUN npm install -g npm@latest && npm -v

COPY wrap_chrome_binary /opt/bin/wrap_chrome_binary
RUN chmod +x /opt/bin/wrap_chrome_binary && /opt/bin/wrap_chrome_binary
