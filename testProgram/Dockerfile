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

#build jpegsrc
RUN \
    echo "#include <stdio.h>\nint main(void){\nchar a[70];\nint b;\nscanf(\"%1d\", &b);\nif(b==1)\nscanf(\"%s\", a);\nreturn 0;\n}" > test.c \
    && afl-gcc -o test test.c


RUN echo "echo core >/proc/sys/kernel/core_pattern\nafl-fuzz -i ./seed -m 1024 -o outputs -- ./test &\npython ./CrashMan_client/sender/sender.py /tmp/outputs/crashes \$1 D \$2 \$3 -a \$4" > ./start.sh \
    && chmod 777 ./start.sh
