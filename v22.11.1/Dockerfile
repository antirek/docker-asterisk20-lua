# Asterisk with Lua support
#
# VERSION 22.11.1
#
# Contain
#  - Asterisk 20.0.0
#  - Lua 5.2
#  - LuaRocks

FROM ubuntu:20.04

MAINTAINER Sergey Dmitriev <serge.dmitriev@gmail.com>


## update ubuntu & install reqs
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get -y install tzdata && \
    apt-get clean

RUN apt-get check && \
    apt-get update && \
    apt-get install -y \
        build-essential zip unzip libreadline-dev curl libncurses-dev mc aptitude \
        tcsh scons libpcre++-dev libboost-dev libboost-all-dev libreadline-dev \
        libboost-program-options-dev libboost-thread-dev libboost-filesystem-dev \
        libboost-date-time-dev gcc g++ git make libmongo-client-dev \
        dh-autoreconf lame sox libzmq3-dev libzmqpp-dev libtiff-tools && \
    apt-get clean


## Asterisk

RUN curl -sf \
        -o /tmp/asterisk.tar.gz \
        -L http://downloads.asterisk.org/pub/telephony/asterisk/releases/asterisk-20.0.0.tar.gz && \
    mkdir /tmp/asterisk && \
    tar -xzf /tmp/asterisk.tar.gz -C /tmp/asterisk --strip-components=1 && \
    cd /tmp/asterisk && \
    contrib/scripts/install_prereq install

RUN cd /tmp/asterisk && \
    ./configure --disable-xmldoc --with-jansson-bundled

RUN cd /tmp/asterisk && \
    make menuselect.makeopts && \
    menuselect/menuselect \
        --disable BUILD_NATIVE \
        --disable DONT_OPTIMIZE \
        --disable COMPILE_DOUBLE \
        --disable DEBUG_THREADS \
        --disable BETTER_BACKTRACES \
        --enable res_endpoint_stats \
        --enable res_chan_stats \
        --enable chan_pjsip \
        menuselect.makeopts && \
    make && make install && \
    apt-get clean


## Lua

ENV LUA_HASH 913fdb32207046b273fdb17aad70be13
ENV LUA_MAJOR_VERSION 5.2
ENV LUA_MINOR_VERSION 4
ENV LUA_VERSION ${LUA_MAJOR_VERSION}.${LUA_MINOR_VERSION}

RUN mkdir /tmp/lua && \
    cd /tmp/lua && \
    echo "${LUA_HASH}  lua-${LUA_VERSION}.tar.gz" > lua-${LUA_VERSION}.md5 && \
    curl -R -O http://www.lua.org/ftp/lua-${LUA_VERSION}.tar.gz && \
    md5sum -c lua-${LUA_VERSION}.md5 && \
    tar -zxf lua-${LUA_VERSION}.tar.gz && \
    cd /tmp/lua/lua-${LUA_VERSION} && \
    make linux && make linux test && make install && \
    cd .. && rm -rf *.tar.gz *.md5 lua-${LUA_VERSION}


## Install luarocks

RUN mkdir /tmp/luarocks && \
    curl -sf -o /tmp/luarocks.tar.gz -L http://luarocks.org/releases/luarocks-3.9.1.tar.gz && \
    tar -zxf /tmp/luarocks.tar.gz -C /tmp/luarocks --strip-components=1 && \
    cd /tmp/luarocks && \
    ./configure && \
    make bootstrap

## Install rocks

RUN luarocks install luasocket 3.0rc1 && \
    luarocks install luasec 0.7 && \
    luarocks install inspect 3.0 && \
    luarocks install redis-lua 2.0.4 && \
    luarocks install luafilesystem 1.7.0 && \
    luarocks install sendmail 0.1.5 && \
    luarocks install lzmq 0.4.4 && \
    luarocks install json-lua 0.1 && \
    luarocks install lua-cjson 2.1.0.6 && \
    luarocks install busted 2.0.rc12

RUN luarocks install luacov 0.13.0 && \
    luarocks install uuid 0.2 && \
    luarocks install moses 1.6.1 && \
    luarocks install httpclient 0.1.0 && \
    luarocks install lualogging 1.3.0 && \
    luarocks install luchia 1.1.2 && \
    luarocks install statsd 3.0.2 && \
    luarocks install ohm 0.1.1-1
