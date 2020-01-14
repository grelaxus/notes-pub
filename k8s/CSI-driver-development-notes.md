# gRPC-server
## Install protobuf
[CSI spec](https://github.com/container-storage-interface/spec/blob/master/spec.md
) requres Node and Controller plugins to accept gRPC calls (and hence run as gRPC-servers).  
In order to build a gRPC implementation (whether it is written in golang, java or other language) it is necessary to have protobuf installed.  
Follow these steps to install the protobuf:  
1. download the protobuf-all-[VERSION].tar.gz
https://github.com/protocolbuffers/protobuf/releases

2. Extract the contents and change in the directory
3. ./configure
4. make
5. make check
6. sudo make install
7. sudo ldconfig # refresh shared library cache.
