ARG BASE_IMAGE_URI=ubuntu
ARG TAG=18.04

# === Base image. We can use FROM and use the same of the stage to "inherit" the base image===
FROM ${BASE_IMAGE_URI}:${TAG} as base


## === apt cacher magic ! ===
## Unfortunately the apt package for Xenial is not available, so using the deb is more flexible
## You can apt-get install auto-apt-proxy in Bionic and later
## https://mirror.pit.teraswitch.com/debian/pool/main/a/auto-apt-proxy/
## Also, using the apt package seems to causing hanging if CACHING_PROXY isn't set, so the deb is superior

ADD https://mirror.pit.teraswitch.com/debian/pool/main/a/auto-apt-proxy/auto-apt-proxy_12_all.deb \
        /auto-apt-proxy.deb
RUN     dpkg -i /auto-apt-proxy.deb

# Eventually I want to script all of this.
# auto-apt-proxy loads CACHING_PROXY into the env I believe
# update the apt config files so that it'll auto-detect and use apt-cacher-ng as a proxy, but only if it finds it
# The nice part about auto-apt-proxy is that it is optional, this dockerfile will work fine even if it can't find the proxy
# It'll hang if it's unable to find the proxy, so we give it a kill-9 after 10 seconds.
RUN     timeout --signal=9 10 auto-apt-proxy && echo "found proxy: $CACHING_PROXY" \
    &&  echo "Acquire::http::Proxy \"$CACHING_PROXY\";" | tee -a /etc/apt/apt.conf.d/00proxy \
    &&  echo "Acquire::https::Proxy-Auto-Detect \"/usr/bin/auto-apt-proxy\";" | tee -a /etc/apt/apt.conf.d/00proxy \
    &&  echo "Acquire::http::Proxy-Auto-Detect \"/usr/bin/auto-apt-proxy\";" | tee /etc/apt/apt.conf.d/auto-apt-proxy.conf \
    ||  echo "Unable to detect apt-cacher proxy, skipping. Removing auto-apt-proxy.conf to prevent hangs on some systems"

## === end apt cacher magic ! any  ===

# Some install which requires a bunch of packages
RUN apt-get update \
 && apt-get install --no-install-recommends -y \
      python3-dev \
 && rm -rf /var/lib/apt/lists/*

# this is for debugging the proxy
#RUN apt-get update \
# && DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y \
#      iputils-ping \
#      dnsutils \
# && rm -rf /var/lib/apt/lists/*


