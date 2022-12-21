FROM nvidia/cuda:11.3.1-cudnn8-runtime-ubuntu20.04

ADD https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh /tmp/conda.sh

RUN bash /tmp/conda.sh -b -p /root/miniconda3 && \
    . /root/miniconda3/bin/activate && \
    conda init && \
    conda create --yes --name torch matplotlib jupyter pandas sympy scikit-learn tqdm pytorch torchvision torchaudio cudatoolkit=11.3 -c pytorch && \
    conda clean -afy && \
    find /root/miniconda3/ -follow -type f -name '*.pyc' -delete && \
    rm /tmp/conda.sh

COPY . .
