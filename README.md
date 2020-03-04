# Nginx autoinstall script for CentOS

## A light rewriting in order to be supported on CentOS.

Entirely based on Angristan's work : https://github.com/angristan/nginx-autoinstall

It's quick, possibly dirty.

Tested and validated on `CentOS Linux release 7.7.1908`.

```sh
wget https://raw.githubusercontent.com/ab-a/nginx-autoinstall-centos/master/nginx-autoinstall.sh
chmod +x nginx-autoinstall.sh
./nginx-autoinstall.sh
```

# Modifications
#### File `nginx-autoinstall.sh` :
```
[root@node-000 nginx-autoinstall-centos]# diff ../nginx-autoinstall/nginx-autoinstall.sh nginx-autoinstall.sh
139a140,142
>               # Create nginx user
>               id -u nginx &>/dev/null || useradd -s /bin/false nginx
>
146,147c149,150
<               apt-get update
<               apt-get install -y build-essential ca-certificates wget curl libpcre3 libpcre3-dev autoconf unzip automake libtool tar git libssl-dev zlib1g-dev uuid-dev lsb-release libxml2-dev libxslt1-dev
---
>               yum update
>               yum install -y gcc gcc-c++ make ca-certificates wget curl autoconf unzip automake libtool tar git zlib-devel uuid-devel libxml2-devel libxslt-devel uuid-devel openssl-devel pcre pcre-devel libuuid-devel libmaxminddb-devel
363,368c366,367
<               # Block Nginx from being installed via APT
<               if [[ $(lsb_release -si) == "Debian" ]] || [[ $(lsb_release -si) == "Ubuntu" ]]
<               then
<                       cd /etc/apt/preferences.d/ || exit 1
<                       echo -e "Package: nginx*\\nPin: release *\\nPin-Priority: -1" > nginx-block
<               fi
---
>               # Block Nginx from being installed via yum
>               echo "exclude=nginx*" >> /etc/yum.conf
407,411c406,407
<               # Remove Nginx APT block
<               if [[ $(lsb_release -si) == "Debian" ]] || [[ $(lsb_release -si) == "Ubuntu" ]]
<               then
<                       rm /etc/apt/preferences.d/nginx-block
<               fi
---
>               # Remove Nginx yum block
>               sed -i '/exclude=nginx\*/d' /etc/yum.conf
```
#### File `conf/nginx.conf` :
```
[root@node-000 nginx-autoinstall-centos]# diff ../nginx-autoinstall/conf/nginx.conf conf/nginx.conf
1c1
< user www-data;
---
> user nginx;
```
#### File `conf/nginx-logrotate` :
```
[root@node-000 nginx-autoinstall-centos]# diff ../nginx-autoinstall/conf/nginx-logrotate conf/nginx-logrotate
8c8
<         create 640 www-data adm
---
>         create 640 nginx adm
```

# Original README.md

# nginx-autoinstall

Compile and install Nginx from source with optional modules.

![screenshot](https://user-images.githubusercontent.com/11699655/33800227-29565ef6-dd3c-11e7-9967-7232ecd36ee4.png)

## Compatibility

* x86, x64, arm*
* Debian 8 and later
* Ubuntu 16.04 and later

## Features

* Latest mainline or stable version, from source
* Optional modules (see below)
* Removed useless modules
* [Custom nginx.conf](https://github.com/Angristan/nginx-autoinstall/blob/master/conf/nginx.conf) (default does not work)
* [Init script for systemd](https://github.com/Angristan/nginx-autoinstall/blob/master/conf/nginx.service) (not provided by default)
* [Logrotate conf](https://github.com/Angristan/nginx-autoinstall/blob/master/conf/nginx-logrotate) (not provided by default)
* Block Nginx installation from APT using pinning, to prevent conflicts

### Optional modules/features

* [LibreSSL from source](http://www.libressl.org/) (CHACHA20, ALPN for HTTP/2, X25519, P-521)
* [OpenSSL from source](https://www.openssl.org/) (TLS 1.3, CHACHA20, ALPN for HTTP/2, X25519, P-521)
* [ngx_pagespeed](https://github.com/pagespeed/ngx_pagespeed) (Google performance module)
* [ngx_brotli](https://github.com/eustas/ngx_brotli) (Brotli compression algorithm)
* [ngx_headers_more](https://github.com/openresty/headers-more-nginx-module) (Custom HTTP headers)
* [ngx_http_geoip2_module](https://github.com/leev/ngx_http_geoip2_module) with [libmaxminddb](https://github.com/maxmind/libmaxminddb) and [GeoLite2 databases](https://dev.maxmind.com/geoip/geoip2/geolite2/)
* [ngx_cache_purge](https://github.com/FRiCKLE/ngx_cache_purge) (Purge content from FastCGI, proxy, SCGI and uWSGI caches)
* [ngx-fancyindex](https://github.com/aperezdc/ngx-fancyindex) (Fancy indexes module)
* [nginx-dav-ext-module](https://github.com/arut/nginx-dav-ext-module) (nginx WebDAV PROPFIND,OPTIONS,LOCK,UNLOCK support)
* [nginx-module-vts](https://github.com/vozlt/nginx-module-vts) (Nginx virtual host traffic status module)
  * See install instructions: [nginx-module-vts#installation](https://github.com/vozlt/nginx-module-vts#installation)
* [ModSecurity from source](https://github.com/SpiderLabs/ModSecurity) (ModSecurity is an open source, cross platform web application firewall (WAF) engine for Apache, IIS and Nginx)
  * [ModSecurity-nginx](https://github.com/SpiderLabs/ModSecurity-nginx) (ModSecurity v3 Nginx Connector)
* HTTP/3 using [Cloudflare's patch](https://blog.cloudflare.com/experiment-with-http-3-using-nginx-and-quiche/) with [Quiche](https://github.com/cloudflare/quiche) and [BoringSSL](https://github.com/google/boringssl).
* [testcookie-nginx-module](https://github.com/kyprizel/testcookie-nginx-module) (testcookie-nginx-module is a simple robot mitigation module using cookie based challenge/response.)
  * See example configuration [testcookie-nginx-module#example-configuration](https://github.com/kyprizel/testcookie-nginx-module#example-configuration) 
* [lua-nginx-module](https://github.com/openresty/lua-nginx-module) (Embed the power of Lua into Nginx HTTP Servers)
  * [luajit2](https://github.com/openresty/luajit2) (OpenResty's maintained branch of LuaJIT)
  * [ngx_devel_kit](https://github.com/simplresty/ngx_devel_kit) (Nginx Development Kit (NDK))

## Install Nginx

Just download and execute the script :

```sh
wget https://raw.githubusercontent.com/Angristan/nginx-autoinstall/master/nginx-autoinstall.sh
chmod +x nginx-autoinstall.sh
./nginx-autoinstall.sh
```

You can check [configuration examples](https://github.com/Angristan/nginx-autoinstall/tree/master/conf) for the custom modules.

## Uninstall Nginx

Just select the option when running the script :

![update](https://lut.im/Hj7wJKWwke/WZqeHT1QwwGfKXFf.png)

You have the choice to delete the logs and the conf.

## Update Nginx

To update Nginx, run the script and install Nginx again. It will overwrite current Nginx files and/or modules.

## Update the script

The update feature downloads the script from this repository, and overwrites the current `nginx-autoinstall.sh` file in the working directory. This allows you to get the latest features, bug fixes, and module versions automatically.

![update](https://lut.im/uQSSVxAz09/zhZRuvJjZp2paLHm.png)

## Install Bad Bot Blocker

This option will install [Nginx Bad Bot and User-Agent Blocker.](https://github.com/mitchellkrogza/nginx-ultimate-bad-bot-blocker) (Nginx Bad Bot and User-Agent Blocker, Spam Referrer Blocker, Anti DDOS, Bad IP Blocker and Wordpress Theme Detector Blocker)

See additional steps to add a cron job for automatic updating, customization and testing in the link above.

## Headless use

You can run the script without the prompts with the option `HEADLESS` set to `y`.

```sh
HEADLESS=y ./nginx-autoinstall.sh
```

To install Nginx mainline with Brotli:

```sh
HEADLESS=y \
NGINX_VER=2 \
BROTLI=y \
./nginx-autoinstall.sh
```

To uninstall Nginx and remove the logs and configuration files:

```sh
HEADLESS=y \
OPTION=2 \
RM_CONF=y \
RM_LOGS=y \
./nginx-autoinstall.sh
```

All the default variables are set at the beginning of the script.

## Log file

A log file is created when running the script. It is located at `/tmp/nginx-autoinstall.log`.

## LICENSE

GPL v3.0
