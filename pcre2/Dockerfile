FROM ubuntu:16.04

MAINTAINER ks8171235@naver.com

ENV AFL_INSTALL http://lcamtuf.coredump.cx/afl/releases/afl-latest.tgz

RUN \
    apt-get update \
        --quiet \
    && apt-get install \
        --yes \
        --no-install-recommends \
        --no-install-suggests \
    autoconf \
    automake \
    gcc \
    libtool \
    make \
    nasm \
    subversion \
    wget \
    git \
    ca-certificates \
    python \
    python-pip \
    g++ \

# Clean up packages.
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \

    && pip install \
        requests

# Get ready to build.
WORKDIR /tmp

RUN \
    git clone https://github.com/ssgskid/CrashMan_client.git

# Get and build AFL.
RUN \
    wget $AFL_INSTALL \
    && tar -xzf afl-latest.tgz \
    && cd afl-* \
    && make \
    && make install \
    && rm -rf /tmp/afl-*

# Get and build libjpeg-turbo.

RUN \
    mkdir seed

RUN echo "123" > seed/seed001

RUN \
    mkdir seed_sentence

RUN echo "123" > seed_sentence/seed_sentence001

#build jpegsrc
RUN \
    wget https://ftp.pcre.org/pub/pcre/pcre2-10.30.tar.gz \
    && tar -xf pcre2-10.30.tar.gz \
    && rm pcre2-10.30.tar.gz \
    && cd pcre2-10.30 \
    && CC=afl-gcc CXX=afl-g++ ./configure --disable-shared \
    && make


RUN echo "echo core >/proc/sys/kernel/core_pattern\nafl-fuzz -i ./seed -m 1024 -o outputs -- ./pcre2-10.30/pcre2grep -f @@ ./seed_sentence/seed_sentence001 &\npython ./CrashMan_client/sender/sender.py /tmp/outputs/crashes \$1 D \$2 \$3 -a \$4" > ./start.sh \
    && chmod 777 ./start.sh
