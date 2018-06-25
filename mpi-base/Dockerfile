FROM ubuntu:latest AS base

LABEL maintainer="brian.skjerven@pawsey.org.au"

RUN perl -p -i.orig -e 's/archive.ubuntu.com/mirror.aarnet.edu.au\/pub\/ubuntu\/archive/' /etc/apt/sources.list \
  && sed -i '0,/# deb-src/{s/# deb-src/deb-src/}' /etc/apt/sources.list

RUN apt-get update \
  && apt-get install -y autoconf automake gcc g++ make gfortran wget python python-dev \
  && apt-get clean all \
  && rm -rf /var/lib/apt/lists/*


FROM base AS builder

LABEL maintainer="brian.skjerven@pawsey.org.au"

### Build MPICH ###

ARG MPICH_VERSION="3.1.4"
ARG MPICH_CONFIGURE_OPTIONS=
ARG MPICH_MAKE_OPTIONS="-j4"

RUN mkdir /tmp/mpich-build \
  && cd /tmp/mpich-build \
  && wget http://www.mpich.org/static/downloads/${MPICH_VERSION}/mpich-${MPICH_VERSION}.tar.gz \
  && tar xvzf mpich-${MPICH_VERSION}.tar.gz \
  && cd mpich-${MPICH_VERSION}  \
  && ./configure ${MPICH_CONFIGURE_OPTIONS} \
  && make ${MPICH_MAKE_OPTIONS} \
  && make install \
  && rm -rf /tmp/mpich-build

### Build MPI4PY ###

ARG MPI4PY_VERSION="3.0.0"

RUN mkdir /tmp/mpi4py-build \
  && cd /tmp/mpi4py-build \
  && wget https://bitbucket.org/mpi4py/mpi4py/downloads/mpi4py-3.0.0.tar.gz \
  && tar xzvf mpi4py-${MPI4PY_VERSION}.tar.gz \
  && cd mpi4py-${MPI4PY_VERSION} \
  && python setup.py build \
  && python setup.py install \
  && rm -rf /tmp/mpi4py-build

### Build OSU Benchmarks ###

ARG OSU_BENCH_VERSION="5.4"
ARG OSU_BENCH_CONFIGURE_OPTIONS="--prefix=/usr/local/osu CC=mpicc CXX=mpicxx"
ARG OSU_BENCH_MAKE_OPTIONS="-j4"

RUN mkdir -p /tmp/osu-benchmark-build \
  && cd /tmp/osu-benchmark-build \
  && wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-${OSU_BENCH_VERSION}.tar.gz \
  && tar xzvf osu-micro-benchmarks-${OSU_BENCH_VERSION}.tar.gz \
  && cd osu-micro-benchmarks-${OSU_BENCH_VERSION} \
  && ./configure ${OSU_BENCH_CONFIGURE_OPTIONS} \
  && make ${OSU_BENCH_MAKE_OPTIONS} \
  && make install \
  && rm -rf /tmp/osu-benchmark-build


FROM base AS main

LABEL maintainer="brian.skjerven@pawsey.org.au"

COPY --from=builder /usr/local/osu /usr/local/osu
COPY --from=builder /usr/local/lib /usr/local/lib
COPY --from=builder /usr/local/bin /usr/local/bin
COPY --from=builder /usr/local/share /usr/local/share
COPY --from=builder /usr/local/include /usr/local/include
COPY --from=builder /usr/local/man /usr/local/man
RUN ldconfig

CMD ["/bin/bash"]


FROM main AS test

LABEL maintainer="brian.skjerven@pawsey.org.au"

# Test MPICH
RUN mkdir /tmp/mpich-test
WORKDIR /tmp/mpich-test
COPY mpich-test .

CMD ["/bin/sh", "test.sh"]
