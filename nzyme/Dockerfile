# syntax=docker/dockerfile:1
FROM debian:buster

# Update system, install first set of tools
RUN apt-get update
RUN apt-get dist-upgrade -y
RUN apt-get install -y wireless-tools wget curl openjdk-11-jre-headless procps usbutils sudo

# Running custom steps for nzyme
RUN apt purge libpcap0.8
RUN wget http://security.debian.org/debian-security/pool/updates/main/libp/libpcap/libpcap0.8_1.8.1-3+deb9u1_armhf.deb
RUN dpkg -i libpcap0.8_1.8.1-3+deb9u1_armhf.deb 
RUN wget https://assets.nzyme.org/releases/nzyme-1.2.2.jar
RUN mkdir /usr/share/nzyme

# Install python3.8 by compiling from source
RUN apt install -y build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libsqlite3-dev libreadline-dev libffi-dev curl libbz2-dev
RUN curl -O https://www.python.org/ftp/python/3.8.2/Python-3.8.2.tar.xz
RUN tar -xf Python-3.8.2.tar.xz
RUN cd Python-3.8.2 && ./configure --enable-optimizations
RUN cd Python-3.8.2 && make -j 4
RUN cd Python-3.8.2 && make install

# copy over config
COPY nzyme.conf nzyme.conf
COPY log4j2-debian.xml log4j2-debian.xml

# replace config values with arg variables
ARG GRAYLOG_SERVER
ARG GRAYLOG_GELF_TCP_PORT
ARG WIFI_INTERFACE
ARG WIFI_CHANNELS
ARG POSTGRES_DATABASE
ARG POSTGRES_USER
ARG POSTGRES_PASSWORD
ARG NZYME_PASSWORD
RUN sed -i "s/GRAYLOG_SERVER/${GRAYLOG_SERVER}/g" nzyme.conf
RUN sed -i "s/GRAYLOG_GELF_TCP_PORT/${GRAYLOG_GELF_TCP_PORT}/g" nzyme.conf
RUN sed -i "s/WIFI_INTERFACE/${WIFI_INTERFACE}/g" nzyme.conf
RUN sed -i "s/WIFI_CHANNELS/${WIFI_CHANNELS}/g" nzyme.conf
RUN sed -i "s/POSTGRES_DATABASE/${POSTGRES_DATABASE}/g" nzyme.conf
RUN sed -i "s/POSTGRES_USER/${POSTGRES_USER}/g" nzyme.conf
RUN sed -i "s/POSTGRES_PASSWORD/${POSTGRES_PASSWORD}/g" nzyme.conf
RUN sed -i "s/NZYME_PASSWORD/$(echo -n ${NZYME_PASSWORD} | sha256sum | awk '{print $1}')/g" nzyme.conf


# expose the web interface
EXPOSE 22900

# Final entrypoint
ENTRYPOINT ["java", "-Xmx1g", "-jar", "-Dlog4j.configurationFile=file:///log4j2-debian.xml", "nzyme-1.2.2.jar", "-c", "nzyme.conf"]