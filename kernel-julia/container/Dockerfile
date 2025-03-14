# Ubuntu 18.04.1 LTS Bionic
ARG BASE_CONTAINER=quay.io/jupyter/julia-notebook:2024-05-27
FROM ${BASE_CONTAINER}

ENV PATH=${PATH}:${CONDA_DIR}/bin

RUN pip install --upgrade ipykernel

RUN conda install --quiet --yes \
    cffi \
    future \
    pycryptodomex && \
    conda clean --all && \
    fix-permissions ${CONDA_DIR} && \
    fix-permissions /home/${NB_USER}

RUN conda update --all && \
    conda clean --all

USER jovyan
ADD jupyter_enterprise_gateway_kernel_image_files_docker-julia.tar.gz /usr/local/bin/

USER root
RUN mv /usr/local/bin/init.jl \
    $(julia --eval "using IJulia; println(pathof(IJulia))" | rev | cut --delimiter='/' --fields=2- | rev)
RUN mv /usr/local/bin/eventloop.jl \
    $(julia --eval "using IJulia; println(pathof(IJulia))" | rev | cut --delimiter='/' --fields=2- | rev)

RUN apt-get update && apt-get install --yes --quiet --no-install-recommends \
    libkrb5-dev \
    && rm --recursive --force /var/lib/apt/lists/*

RUN chown jovyan:users /usr/local/bin/bootstrap-kernel.sh && \
    chmod 0755 /usr/local/bin/bootstrap-kernel.sh && \
    chown --recursive jovyan:users /usr/local/bin/kernel-launchers

USER jovyan

ENV KERNEL_LANGUAGE=julia

HEALTHCHECK NONE

CMD /usr/local/bin/bootstrap-kernel.sh
