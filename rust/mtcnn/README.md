# SSVM-Tensorflow Example for Running MTCNN Model

## Setup the wasm32-wasi target of Cargo

```bash
$ rustup target add wasm32-wasi
```

## Build

### Build wasm source

```bash
$ cargo build --release --target=wasm32-wasi
```

The result wasm file will be at `target/wasm32-wasi/release/mtcnn.wasm`.

### Build ssvm-tensorflow

Get [ssvm-tensorflow](https://github.com/second-state/ssvm-tensorflow).

```bash
$ docker pull secondstate/ssvm
$ docker run -it --rm \
    -v <path/to/your/ssvm-tensorflow/source/folder>:/root/ssvm-tensorflow \
    secondstate/ssvm:latest
(docker)$ cd /root/ssvm-tensorflow
(docker)$ mkdir -p build && cd build
# Install the JPEG and PNG library
(docker)$ apt-get update && apt-get install -y libjpeg-dev libpng-dev
# Install the tensorflow library
(docker)$ wget https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow-cpu-linux-x86_64-2.3.0.tar.gz
(docker)$ tar -C /usr/local -xzf libtensorflow-cpu-linux-x86_64-2.3.0.tar.gz
(docker)$ ldconfig
# Build ssvm-tensorflow
(docker)$ cmake -DCMAKE_BUILD_TYPE=Release .. && make -j
```

## Run

Interpreter mode:

```bash
(docker)$ cd /root/ssvm-tensorflow/build/tools
# Copy input image, model, and wasm file to /root/ssvm-tensorflow/build/tools
(docker)$ ./ssvm-tensorflow --dir .:. mtcnn.wasm mtcnn.pb  solvay.jpg
```

AOT mode:

```bash
(docker)$ cd /root/ssvm-tensorflow/build/tools
# Copy input image, model, and wasm file to /root/ssvm-tensorflow/build/tools
(docker)$ ./ssvmc-tensorflow mtcnn.wasm mtcnn.wasm.so
(docker)$ ./ssvm-tensorflow --dir .:. mtcnn.wasm.so mtcnn.pb  solvay.jpg
```
