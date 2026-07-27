ARG BUILD_FROM=ghcr.io/home-assistant/amd64-base:3.18
FROM $BUILD_FROM AS builder

ENV LANG=C.UTF-8

RUN apk add --no-cache \
    build-base \
    argp-standalone \
    git \
    mosquitto-dev \
    cmake

WORKDIR /build

# Zmieniamy lekko komendę git clone (np. dodając parametr), żeby wymusić unieważnienie pamięci podręcznej (no-cache)
RUN git clone --depth 1 https://github.com/john30/ebusd.git /build \
    && cmake . \
    && make \
    && find /build -name "ebusd" -type f \
    && find /build -name "ebusctl" -type f \
    && cp $(find /build -name "ebusd" -type f | head -n 1) /tmp/ebusd \
    && cp $(find /build -name "ebusctl" -type f | head -n 1) /tmp/ebusctl

ARG BUILD_FROM=ghcr.io/home-assistant/amd64-base:3.18
FROM $BUILD_FROM

LABEL Description="ebusd"

RUN apk add --no-cache \
    build-base \
    argp-standalone \
    mosquitto-dev

COPY --from=builder /tmp/ebusd /usr/local/bin/ebusd
COPY --from=builder /tmp/ebusctl /usr/local/bin/ebusctl

WORKDIR /

COPY run.sh /
RUN chmod a+x /run.sh
CMD [ "/run.sh" ]
