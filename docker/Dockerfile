FROM alpine

ENV LANG=zh_CN.UTF-8 \
    TZ=Asia/Shanghai \
    WORKDIR=/var/www \
    PS1="\u@\h:\w \$ "

RUN apk add --no-cache \
       bash \
       busybox-suid \
       caddy \
       git \
       php80 \
       php80-curl \
       php80-fpm \
       php80-gd \
       php80-json \
       php80-mbstring \
       php80-mysqli \
       php80-session \
       php80-zip \
       s6-overlay \
       shadow \
       tzdata \
    && caddy upgrade \
    && ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo -e "${TZ}" > /etc/timezone \
    && echo -e "max_execution_time = 3600\nupload_max_filesize=128M\npost_max_size=128M\nmemory_limit=1024M\ndate.timezone=${TZ}" > /etc/php80/conf.d/99-overrides.ini \
    && echo -e "[global]\nerror_log = /dev/stdout\ndaemonize = no\ninclude=/etc/php80/php-fpm.d/*.conf" > /etc/php80/php-fpm.conf \
    && echo -e "[www]\nuser = caddy\ngroup = caddy\nlisten = 127.0.0.1:9000\nlisten.owner = caddy\nlisten.group = caddy\npm = ondemand\npm.max_children = 75\npm.max_requests = 500\npm.process_idle_timeout = 10s\nchdir = $WORKDIR" > /etc/php80/php-fpm.d/www.conf \
    && echo -e "{\n    admin off\n}\n:8080\nroot * $WORKDIR\nlog {\n    level warn\n}\nphp_fastcgi 127.0.0.1:9000\nfile_server" > /etc/caddy/Caddyfile \
    && rm -rf $WORKDIR/* /var/cache/apk/* /tmp/* \
    && git config --global pull.ff only \
    && git clone --depth=1 -b dav https://github.com/KoWming/Tieba-Cloud-Sign $WORKDIR \
    && mkdir /etc/cont-init.d \
    && mkdir /etc/services.d \
    && cp -r /var/www/docker/s6-overlay/etc/* /etc/

ENTRYPOINT ["/init"]
