FROM alpine:latest AS alpine-base
WORKDIR /usr/local
#更换apline的源为阿里云的
RUN echo "http://mirrors.aliyun.com/alpine/latest-stable/main/" > /etc/apk/repositories && \
echo "http://mirrors.aliyun.com/alpine/latest-stable/community/" >> /etc/apk/repositories && \
apk update && \
#安装wget 和git 我们为了使镜像最小化这个都放在另外一个镜像里面来实现
apk add --no-cache wget git && \
#下载nginx包
wget http://nginx.org/download/nginx-1.19.1.tar.gz && \
tar xvf nginx-1.19.1.tar.gz && \
#克隆我们需要的模块和主题
git clone https://github.com/FRiCKLE/ngx_cache_purge.git && \
mkdir /usr/local/nginx-1.19.1/model && \
mv ./ngx_cache_purge /usr/local/nginx-1.19.1/model/

FROM alpine:latest
MAINTAINER jiuyang yszar@vip.qq.com
WORKDIR /root
#从上面一个镜像中把我们刚才下载安装的包拷贝到这个里面
COPY --from=alpine-base /usr/local/nginx-1.19.1 /usr/local/nginx-1.19.1
RUN echo "http://mirrors.aliyun.com/alpine/latest-stable/main/" > /etc/apk/repositories && \
echo "http://mirrors.aliyun.com/alpine/latest-stable/community/" >> /etc/apk/repositories && \
apk update && \
#安装编译安装需要的依赖
apk add --no-cache gcc libc-dev make openssl-dev pcre-dev zlib-dev linux-headers curl && \
cd /usr/local/nginx-1.19.1/ && \
#执行编译安装
./configure --prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--with-perl_modules_path=/usr/lib/perl5/vendor_perl \
--user=nginx \
--group=nginx \
--with-compat \
--with-file-aio \
--with-threads \
--with-http_addition_module \
--with-http_auth_request_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_mp4_module \
--with-http_random_index_module \
--with-http_realip_module \
--with-http_secure_link_module \
--with-http_slice_module \
--with-http_ssl_module \
--with-http_stub_status_module \
--with-http_sub_module \
--with-http_v2_module \
--with-mail \
--with-mail_ssl_module \
--with-stream \
--with-stream_realip_module \
--with-stream_ssl_module \
--with-stream_ssl_preread_module \
--with-cc-opt='-Os -fomit-frame-pointer' \
--with-ld-opt=-Wl,--as-needed \
--add-module=/usr/local/nginx-1.19.1/model/ngx_cache_purge && \
make && make install && \
mkdir -p /var/cache/nginx/client_temp && \
rm -rf /usr/local/nginx-1.19.1
EXPOSE 80
CMD ["/bin/sh","-c","nginx -g 'daemon off;'"]
