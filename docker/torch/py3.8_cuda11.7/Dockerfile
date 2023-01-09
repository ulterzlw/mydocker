FROM continuumio/conda-ci-linux-64-python3.8

RUN conda create --yes --name torch python=3.8 matplotlib jupyter pandas sympy scikit-learn tqdm torchvision torchaudio pytorch-cuda=11.7 cudatoolkit tensorboard -c nvidia -c pytorch -c conda-forge && \
    conda clean -afy
