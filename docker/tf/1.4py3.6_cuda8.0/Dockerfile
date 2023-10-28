FROM condaforge/mambaforge

RUN mamba create -n tf -y -c anaconda -c free python=3 tensorflow-gpu=1.4  && \
    mamba clean -afy && \
    mamba init
