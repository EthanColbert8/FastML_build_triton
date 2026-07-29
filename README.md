# Build Triton

Instructions to build a minimal Triton container for CMS.

## Server build

1. Checkout:
    ```bash
    git clone git@github.com:triton-inference-server/server
    ```

2. Build:
    ```bash
    export TRITON_BUILD_EXPERIMENTAL=1
    ./build.py \
    --target-platform linux -j 24 --no-container-interactive \
    --version 2.68.0 --container-version r26.04 --upstream-container-version 26.04 --ort-version 1.24.4 \
    --backend ensemble --backend python --backend pytorch --backend onnxruntime --backend tensorflow \
    --endpoint grpc --endpoint http \
    --repoagent checksum \
    --enable-logging --enable-stats --enable-metrics --enable-gpu-metrics --enable-cpu-metrics --enable-tracing --enable-nvtx --enable-gpu \
    --build-presets-file ../build_triton/presets.json \
    -v &> log_build.log &
    ```

3. Tag for later use:
    ```bash
    docker tag tritonserver:latest fastml/triton-cms:26.04-py3
    ```

## PyTorch Geometric libraries

1. Add PyTorch Geometric libraries (based on [triton-torchgeo-gat-example](https://github.com/kpedro88/triton-torchgeo-gat-example)):
    ```bash
    docker build -t fastml/triton-torchgeo:26.04-py3-geometric -f Dockerfile.torchgeo -m 16g . &> log_build_geo.log &
    ```

2. Push to DockerHub:
    ```bash
    docker push fastml/triton-torchgeo:26.04-py3-geometric
    ```

This automatically triggers the Apptainer conversion and cvmfs synchronization via [unpacked](https://gitlab.cern.ch/unpacked/sync).
