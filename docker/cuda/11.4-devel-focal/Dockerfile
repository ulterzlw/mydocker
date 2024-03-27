FROM ubuntu:20.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    gnupg2 curl ca-certificates && \
    curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub | apt-key add - && \
    echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
    apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/*

ENV CUDA_VERSION 11.4.3

# For libraries in the cuda-compat-* package: https://docs.nvidia.com/cuda/eula/index.html#attachment-a
RUN apt-get update && apt-get install -y --no-install-recommends \
    #base 
    cuda-cudart-11-4 \
    cuda-compat-11-4 \
    #runtime
    cuda-libraries-11-4 \
    libnpp-11-4 \
    cuda-nvtx-11-4 \
    libcusparse-11-4 \
    libcublas-11-4 \
    libnccl2 \
    #devel
    libtinfo5 libncursesw5 \
    cuda-cudart-dev-11-4 \
    cuda-command-line-tools-11-4 \
    cuda-minimal-build-11-4 \
    cuda-libraries-dev-11-4 \
    cuda-nvml-dev-11-4 \
    cuda-nvprof-11-4 \
    libnpp-dev-11-4 \
    libcusparse-dev-11-4 \
    libcublas-dev-11-4 \
    libnccl-dev \
    cuda-nsight-compute-11-4 \
    && rm -rf /var/lib/apt/lists/*

ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

# nvidia-container-runtime
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
