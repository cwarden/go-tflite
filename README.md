# go-tflite

Go binding for TensorFlow Lite

## Usage

See `_example/main.go`

## Requirements

* TensorFlow Lite

## Installation

At the first, you must install Tensorflow Lite C API. The repository of tensorflow must be located on `$GOPATH/src/github.com/tensorflow/tensorflow`.

```
$ cd /path/to/gopath/src/github.com/tensorflow/tensorflow
$ bazel build --config opt --config monolithic tensorflow:libtensorflow_c.so
```

Then go build on go-tflite. If you don't love bazel, you can use this Makefile. Put this on the `tensorflow/lite/experimental/c`, and `make`. Sorry, I don't make sure this works on Linux or Mac.

```make
SRCS = \
        c_api.cc \
        c_api_experimental.cc

OBJS = $(subst .cc,.o,$(subst .cxx,.o,$(subst .cpp,.o,$(SRCS))))

TENSORFLOW_ROOT = $(shell go env GOPATH)/src/github.com/tensorflow/tensorflow
CXXFLAGS = -DTF_COMPILE_LIBRARY -I$(TENSORFLOW_ROOT) -I$(TENSORFLOW_ROOT)/tensorflow/lite/tools/make/downloads/flatbuffers/include
TARGET = libtensorflowlite_c
ifeq ($(OS),Windows_NT)
OS_ARCH = windows_x86_64
TARGET := $(TARGET).dll
else
ifeq ($(shell uname -m),armv6l)
OS_ARCH = linux_armv6l
else
OS_ARCH = rpi_armv7l
endif
TARGET := $(TARGET).so
endif
LDFLAGS += -L$(TENSORFLOW_ROOT)/tensorflow/lite/tools/make/gen/$(OS_ARCH)/lib
LIBS = -ltensorflow-lite

.SUFFIXES: .cpp .cxx .o

all : $(TARGET)

$(TARGET) : $(OBJS)
        g++ -shared -o $@ $(OBJS) $(LDFLAGS) $(LIBS)

.cxx.o :
        g++ -std=c++14 -c $(CXXFLAGS) -I. $< -o $@

.cpp.o :
        g++ -std=c++14 -c $(CXXFLAGS) -I. $< -o $@

clean :
        rm -f *.o $(TARGET)
```

## License

MIT

## Author

Yasuhrio Matsumoto (a.k.a. mattn)
