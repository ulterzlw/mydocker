FROM continuumio/conda-ci-linux-64-python3.8

RUN conda create --yes --name torch python=3.8 matplotlib jupyter pandas sympy scikit-learn tqdm pytorch=1.13.1 torchvision torchaudio cudatoolkit pytorch-cuda=11.7 -c nvidia -c pytorch && \
    conda clean -afy
