# syntax = devthefuture/dockerfile-x

INCLUDE Dockerfile.base

ARG GIT_COMMIT_HASH=""

RUN apt-get update
RUN DEBIAN_FRONTEND="noninteractive" apt-get install -y \
    curl \
    git \
    htop \
    tmux \
    file \
    vim \
    expect \
    mpich \
    libmpich-dev \
    protobuf-compiler \
    python3 python3-pip \
    && rm -rf /var/lib/{apt,dpkg,cache,log}

RUN echo "export PATH=/usr/local/cuda/bin:\$PATH" > /etc/profile.d/50-smc.sh
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 1

RUN DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get install -y python3-packaging

# cassandra
RUN apt-get install -y \
    default-jdk \
    wget \
    sudo
RUN echo "deb https://debian.cassandra.apache.org 41x main" | tee -a /etc/apt/sources.list.d/cassandra.sources.list
RUN curl https://downloads.apache.org/cassandra/KEYS | apt-key add -
RUN apt-get update
RUN apt-get install cassandra -y

# to ping hf
RUN apt-get install -y iputils-ping

COPY . /tmp/app
RUN echo "refact ${GIT_COMMIT_HASH}" >> /refact-build-info.txt
RUN pip install ninja
RUN pip install -U cython
RUN pip install /tmp/app -v --no-build-isolation && rm -rf /tmp/app

ENV REFACT_PERM_DIR "/perm_storage"
ENV REFACT_TMP_DIR "/tmp"
ENV RDMAV_FORK_SAFE 0
ENV RDMAV_HUGEPAGES_SAFE 0

EXPOSE 8008

COPY database-start.sh /
RUN chmod +x database-start.sh
COPY docker-entrypoint.sh /
RUN chmod +x docker-entrypoint.sh

CMD ./docker-entrypoint.sh
