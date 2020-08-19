FROM openjdk:11

ARG CHROME_VERSION="google-chrome-stable"
ARG NODE_VERSION='12'

ENV ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
		ASPNETCORE_VERSION=2.2.6

WORKDIR /tmp
RUN curl -sL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash -
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
	&& echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list \
	&& apt-get update -y \
	&& apt-get -y install --no-install-recommends \
      ca-certificates \
      \
# .NET Core dependencies
      libc6 \
      libgcc1 \
      libgssapi-krb5-2 \
      libicu57 \
      liblttng-ust0 \
      libssl1.0.2 \
      libstdc++6 \
      zlib1g \
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

COPY at-image/wrap_chrome_binary /opt/bin/wrap_chrome_binary
RUN chmod +x /opt/bin/wrap_chrome_binary && /opt/bin/wrap_chrome_binary

# Install .Net runtime
RUN curl -SL --output aspnetcore.tar.gz https://dotnetcli.blob.core.windows.net/dotnet/aspnetcore/Runtime/$ASPNETCORE_VERSION/aspnetcore-runtime-$ASPNETCORE_VERSION-linux-x64.tar.gz \
    && aspnetcore_sha512='b5b86ffa40a4294e2532d22f6b3c173ebf17ed569ccb1e51fa9fe8804128b9541ce5fccabe74e71be9a7d42b80414f74b4f27a4733ca9c1dd50cb0b69c92829f' \
    && echo "$aspnetcore_sha512  aspnetcore.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -zxf aspnetcore.tar.gz -C /usr/share/dotnet \
    && rm aspnetcore.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet
