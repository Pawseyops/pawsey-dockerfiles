FROM ubuntu:17.10

LABEL maintainer="brian.skjerven@pawsey.org.au"

RUN apt-get update \
      && apt-get install -y \
      cython \
      python-minimal \
      python-pip

RUN pip install\
      astropy \
      h5py \
      matplotlib \
      nose \
      numpy \
      pytest \
      scipy \
      setuptools

CMD ["/bin/bash"]
