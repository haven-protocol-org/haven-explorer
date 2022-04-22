FROM ubuntu:20.04 as builder

ENV CXXFLAGS="-DSTACK_TRACE:BOOL -DELPP_FEATURE_CRASH_LOG"
ARG HAVEN_VERSION=v2.2.3
ARG TARGETOS
ARG TARGETARCH
ARG TARGETVARIANT
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
 && apt-get --no-install-recommends --yes install ca-certificates git wget libmysql++-dev libunwind-dev libcurl4-openssl-dev libzmq5 libzmq3-dev build-essential cmake pkg-config libssl-dev libunbound-dev libsodium-dev liblzma-dev libreadline6-dev libldns-dev libexpat1-dev doxygen graphviz libpgm-dev libudev-dev

# Libboost
RUN wget https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz
RUN tar -xvf boost_1_77_0.tar.gz
RUN cd boost_1_77_0 \
  && ./bootstrap.sh \
  && ./b2 install -j2

WORKDIR /explorer
COPY . .
RUN git submodule update --init --recursive

# Building Haven
WORKDIR src/haven-main/build/Linux/master/release/
RUN cmake -D CMAKE_BUILD_TYPE=Release ../../../..
RUN cmake --build . --target wallet --
RUN cmake --build . --target daemonizer --
RUN cmake --build . --target cryptonote_protocol --

WORKDIR /explorer/build

RUN cmake -DMONERO_DIR=$PWD/../src/haven-main ..
RUN make

