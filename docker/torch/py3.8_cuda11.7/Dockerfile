FROM continuumio/conda-ci-linux-64-python3.8

COPY environment.yml .

RUN conda env create -f environment.yml && \
    conda clean -afy
