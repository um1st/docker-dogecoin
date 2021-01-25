FROM debian:jessie-slim as builder

# Default version is latest
ARG DOGECOIN_VERSION="v1.8.3"

# Install dependencies
RUN apt-get update -y \
  && apt-get install -y \
  	build-essential libtool autotools-dev autoconf pkg-config libssl-dev \
  	libboost-system-dev libboost-filesystem-dev libboost-chrono-dev \
  	libboost-program-options-dev libboost-test-dev libboost-thread-dev \
  	libminiupnpc-dev \
  	wget git

WORKDIR /src

# Defaults for BerkeleyDB
ENV BDB_VERSION="5.1.29.NC" BDB_SRC="/src/db"

# Install BerkeleyDB, patch it and build
RUN mkdir -p $BDB_SRC \
	&& wget "http://download.oracle.com/berkeley-db/db-$BDB_VERSION.tar.gz" \
	&& echo "08238e59736d1aacdd47cfb8e68684c695516c37f4fbe1b8267dde58dc3a576c db-$BDB_VERSION.tar.gz" | sha256sum -c \
	&& tar -xzvf db-$BDB_VERSION.tar.gz \
	&& sed -i 's/__atomic_compare_exchange/__atomic_compare_exchange_db/g' db-$BDB_VERSION/src/dbinc/atomic.h \
	&& cd db-$BDB_VERSION/build_unix/ \
    && ../dist/configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_SRC \
    && make -j4 \
    && make install

# Clone, build and strip core
RUN  git clone -b $DOGECOIN_VERSION https://github.com/dogecoin/dogecoin.git ./doge \
	&& cd ./doge \
    && ./autogen.sh \
    && ./configure \
        LDFLAGS="-L$BDB_SRC/lib/" \
        CPPFLAGS="-I$BDB_SRC/include/" \
        CXXFLAGS="--param ggc-min-expand=1 --param ggc-min-heapsize=32768" \
        --disable-tests \
        --disable-ccache \
        --without-gui \
        --with-daemon \
        --prefix=/src \
    && make -j4 \
    && make install \
    && strip /src/bin/dogecoin-cli \
    && strip /src/bin/dogecoind

# Clean image
FROM debian:jessie-slim

LABEL maintainer="um1st <um1statl1st@gmail.com>"

ENV USER=dogecoin
ENV DOGECOIN_DATA=/home/$USER/.dogecoin

# Install dependencies
RUN useradd -ms /bin/bash $USER \
  && apt-get update -y \
  && apt-get install -y libssl-dev libminiupnpc-dev \
  	libboost-system-dev libboost-filesystem-dev libboost-chrono-dev \
  	libboost-program-options-dev libboost-test-dev libboost-thread-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

USER $USER
RUN mkdir -p $DOGECOIN_DATA
VOLUME ["$DOGECOIN_DATA"]

COPY --from=builder /src/bin /usr/local/bin

COPY --chown=$USER docker-entrypoint.sh /entrypoint.sh

RUN chmod u+x /entrypoint.sh


EXPOSE 8332 8333 18332 18333 18444 22555 22556 44555 44556

ENTRYPOINT ["/entrypoint.sh"]

CMD ["dogecoind"]