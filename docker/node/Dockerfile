FROM nanocurrency/nano-env:gcc

ARG NETWORK=live
ARG TRAVIS_TAG=DEV_BUILD
ARG CI_BUILD=OFF
ADD ./ /tmp/src

RUN mkdir /tmp/build && \
    cd /tmp/build && \
    cmake /tmp/src -DCI_BUILD=${CI_BUILD} -DBOOST_ROOT=${BOOST_ROOT} -DACTIVE_NETWORK=nano_${NETWORK}_network -DNANO_ROCKSDB=ON && \
    make nano_node -j $(nproc) && \
    make nano_rpc -j $(nproc) && \
    cd .. && \
    echo ${NETWORK} > /etc/nano-network

FROM ubuntu:16.04
COPY --from=0 /tmp/build/nano_node /usr/bin
COPY --from=0 /tmp/build/nano_rpc /usr/bin
COPY --from=0 /etc/nano-network /etc
COPY docker/node/entry.sh /entry.sh
COPY docker/node/config /usr/share/nano/config
RUN chmod +x /entry.sh
RUN ln -s /usr/bin/nano_node /usr/bin/rai_node
ENV PATH="${PATH}:/usr/bin"
ENTRYPOINT ["/bin/bash",  "/entry.sh"]
CMD ["nano_node daemon -l"]
