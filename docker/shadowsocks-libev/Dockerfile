# Dockerfile for shadowsocks-libev based alpine
# Copyright (C) 2018 - 2024 Teddysun <i@teddysun.com>
# Reference URL:
# https://github.com/shadowsocks/shadowsocks-libev
# https://github.com/shadowsocks/v2ray-plugin
# https://github.com/teddysun/v2ray-plugin
# https://github.com/teddysun/xray-plugin

FROM alpine:3.18
LABEL maintainer="Teddysun <i@teddysun.com>"

WORKDIR /root
COPY v2ray-plugin.sh /root/v2ray-plugin.sh
COPY xray-plugin.sh /root/xray-plugin.sh
COPY config_sample.json /etc/shadowsocks-libev/config.json
RUN set -ex \
	&& runDeps="git build-base c-ares-dev autoconf automake libev-dev libtool libsodium-dev linux-headers mbedtls-dev pcre-dev" \
	&& apk add --no-cache --virtual .build-deps ${runDeps} \
	&& mkdir -p /root/libev \
	&& cd /root/libev \
	&& git clone --depth=1 https://github.com/shadowsocks/shadowsocks-libev.git . \
	&& git submodule update --init --recursive \
	&& ./autogen.sh \
	&& ./configure --prefix=/usr --disable-documentation \
	&& make install \
	&& apk add --no-cache \
		tzdata \
		rng-tools \
		ca-certificates \
		$(scanelf --needed --nobanner /usr/bin/ss-* \
		| awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
		| xargs -r apk info --installed \
		| sort -u) \
	&& apk del .build-deps \
	&& cd /root \
	&& rm -rf /root/libev \
	&& chmod +x /root/v2ray-plugin.sh /root/xray-plugin.sh \
	&& /root/v2ray-plugin.sh \
	&& /root/xray-plugin.sh \
	&& rm -f /root/v2ray-plugin.sh /root/xray-plugin.sh

VOLUME /etc/shadowsocks-libev
ENV TZ=Asia/Shanghai
CMD [ "ss-server", "-c", "/etc/shadowsocks-libev/config.json" ]
