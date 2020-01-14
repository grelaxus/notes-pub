# gRPC-server
## Install protobuf
[CSI spec](https://github.com/container-storage-interface/spec/blob/master/spec.md
) requres Node and Controller plugins to accept gRPC calls (and hence run as gRPC-servers).  
In order to build a gRPC implementation (whether it is written in golang, java or other language) it is necessary to have protobuf compiler (protoc) installed.  

Follow these steps to install the protobuf:  
1. download the protobuf-all-[VERSION].tar.gz
https://github.com/protocolbuffers/protobuf/releases

2. Extract the contents and change in the directory
3. ./configure
4. make
5. make check
6. sudo make install
7. sudo ldconfig # refresh shared library cache.

## Install proto-gen-go
Generating .proto files may be needed (e.g. to generate server and client):
```sh
go get -u github.com/golang/protobuf/protoc-gen-go
```
This should download the repo into $GOPATH/src/github.com/golang/protobuf and build protoc-gen-go in $GOPATH/bin. 
If $GOPATH is not explicitly set, the default (on Unix systems) is $HOME/go.
