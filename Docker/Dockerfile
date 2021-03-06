FROM eosio/builder as builder

RUN git clone -b master --depth 1 https://github.com/EOSIO/eos.git --recursive \
    && cd eos \
    && cmake -H. -B"/tmp/build" -GNinja -DCMAKE_BUILD_TYPE=Release -DWASM_ROOT=/opt/wasm -DCMAKE_CXX_COMPILER=clang++ \
       -DCMAKE_C_COMPILER=clang -DCMAKE_INSTALL_PREFIX=/tmp/build  -DSecp256k1_ROOT_DIR=/usr/local \
    && cmake --build /tmp/build --target install \
    && mkdir -p /opt/eosio/ && cp -r /tmp/build/bin /opt/eosio/bin && cp -r /tmp/build/contracts /contracts \
    && rm -rf /tmp/build && rm -rf /eos 

FROM ubuntu:16.04

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install openssl && rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/eosio /opt/eosio
COPY --from=builder /contracts /contracts
COPY start_eosiod.sh /opt/eosio/bin/
COPY config.ini /
COPY genesis.json /

ENV EOSIO_ROOT=/opt/eosio
RUN chmod +x /opt/eosio/bin/start_eosiod.sh
ENV LD_LIBRARY_PATH /usr/local/lib
VOLUME /opt/eosio/bin/data-dir
ENV PATH /opt/eosio/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
