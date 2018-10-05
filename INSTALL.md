# Installation

## Official 

See http://caffe.berkeleyvision.org/installation.html for the latest
installation instructions  for `Caffe` itself.

Check the Caffe issue tracker in case you need help: https://github.com/BVLC/caffe/issues

## Installation of Patched Version on Jetson TK1

### Prerequisites

To avoid `ld` linker errors you may symbolize `libhdf5` and `libhdf5_hl` from their install directory to `/usr/local/lib`.

In addition to prerequisites described [here](http://caffe.berkeleyvision.org/installation.html#prerequisites) we are also need:

- `CUDA`. Please install [official CUDA 6.5 binary from nVidia](https://developer.nvidia.com/cuda-zone)
- `CUDNN for Jetson and CUDA 6.5`. Please [get one from nVidia](https://developer.nvidia.com/cudnn) using your nVidia Developer account.
- `Google Protobuf v3.0.0`. It needs to be compiled manually. Note that the code cloned from official repository under the `3.0.0` tag doesn't build due to changes in Google.Test `gmock` repositories. You can fix this yourself or use pre-patched version from unofficial repository https://robotics.bmstu.ru/gitlab/jetson-patches/protobuf-3.0.0 (requires some Python dependencies to be installed).
- `OpenCV 3.1.0` with some patches. It needs to be compiled too. You may use the [instructions from official OpenCV knowledge base](https://docs.opencv.org/3.3.0/d6/d15/tutorial_building_tegra_cuda.html) or grab pre-patched code from https://robotics.bmstu.ru/gitlab/jetson-patches/OpenCV-3.1.0

### Building

Here and below we assume that you keep your `Protobuf` and `OpenCV` installed into `/util` root directory. Also you must set `PATH`, `LD_LIBRARY_PATH`, `PKG_CONFIG_PATH` environment variables to make the compiler available to access your side installations.

If you have another `Protobuf` already installed in system, set the temporary links for `protoc` and `/util/include/google/protobuf` directory using following commands:
```bash
sudo mv /usr/include/google/protobuf /usr/include/google/protobuf_backup
sudo mv /usr/bin/protoc /usr/bin/protoc_backup
sudo ln -s /util/include/google/protobuf /usr/include/google/protobuf
sudo ln -s /util/bin/protoc /usr/bin/protoc
```

Now change the directory to the cloned `caffetk1` and run:
```bash
cp Makefile.config.example Makefile.config
CXXFLAGS="-std=c++11 -I/util/include -L/util/lib" NVCCFLAGS="-std=c++11 -Xcompiler=-fPIC -I/util/include -L/util/lib" LDFLAGS="-L/util/lib" USE_PKG_CONFIG=1 make -j4
```

After that (if the build process produced no errors) you will have your `Caffe` built. 

Now you can run:
```bash
CXXFLAGS="-std=c++11 -I/util/include -L/util/lib" NVCCFLAGS="-std=c++11 -Xcompiler=-fPIC -I/util/include -L/util/lib" LDFLAGS="-L/util/lib" USE_PKG_CONFIG=1 make distribute
```
to obtain `distribute` directory which will contain XDG directory tree in which `Caffe` can be deployed on the machine with the same hardware and configuration. Run
```bash
./distribute/bin/caffe.bin device_query -gpu 0
```
to view the GPU diagnostic message. If you don't see an error message, your `Caffe` is ready for testing.

### Testing

Running tests for `Caffe` on Jetson is quite simple. Just use
```bash
CXXFLAGS="-std=c++11 -I/util/include -L/util/lib" NVCCFLAGS="-std=c++11 -Xcompiler=-fPIC -I/util/include -L/util/lib" LDFLAGS="-L/util/lib" USE_PKG_CONFIG=1 make runtest
```

The testing process duration is about half a hour.

**Congratulations! Your `Caffe for Jetson TK1` is now compiled!**
