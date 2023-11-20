FROM frolvlad/alpine-glibc:alpine-3.17

LABEL maintainer="Anaconda, Inc"

ENV PATH /opt/conda/bin:$PATH

# Leave these args here to better use the Docker build cache
ARG CONDA_VERSION=py310_23.1.0-1
ARG SHA256SUM=32d73e1bc33fda089d7cd9ef4c1be542616bd8e437d1f77afeeaf7afdb019787

# hadolint ignore=DL3018
RUN addgroup -S anaconda && \
    adduser -D -u 10151 anaconda -G anaconda && \
    apk add -q --no-cache bash procps tzdata && \
    cp /usr/share/zoneinfo/Etc/GMT /etc/localtime && \
    wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O miniconda.sh && \
    echo "${SHA256SUM}  miniconda.sh" > miniconda.sha256 && \
    if ! sha256sum -cs miniconda.sha256; then exit 1; fi && \
    mkdir -p /opt && \
    sh miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh miniconda.sha256 && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy && \
    apk del tzdata && \
    \
    chown -R anaconda:anaconda /opt

USER  anaconda
ENV HOME=/home/anaconda

# COPY --chown=anaconda:anaconda .pip/pip.conf ${HOME}/.pip/
# COPY --chown=anaconda:anaconda bin ${HOME}/bin/

RUN conda config --add channels conda-forge && \
    conda config --append channels anaconda && \
    conda config --append  channels pytorch && \
    conda config --append  channels fastai && \
    conda install --yes mamba pyyaml
    # conda update -n base --all

CMD ["/bin/bash"]