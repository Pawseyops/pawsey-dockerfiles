FROM ubuntu:latest

LABEL maintainer="brian.skjerven@pawsey.org.au"

RUN apt-get update \
      && apt-get install -y \
      cython \
      python-minimal \
      python-pip

RUN pip install --upgrade pip \
      && pip install \
      astropy \
      h5py \
      matplotlib \
      nose \
      numpy \
      pytest \
      scipy \
      setuptools \
      pandas

CMD ["/bin/bash"]
