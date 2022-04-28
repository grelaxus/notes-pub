
# Build

## Build for Linus amd64
```sh
GOOS=linux GOARCH=amd64 go build -o build/bin/my-k8s-operator ./cmd/manager
```
## Upgrading dependencies
As it described [here](https://go.dev/blog/using-go-modules#upgrading-dependencies)..

List all dependencies
```sh
$ go list -m all
github.com/my-k8s-operator
...
k8s.io/api v0.0.0-20191016110408-35e52d86657a
```
we can see we’re using an untagged version of k8s.io/api. Let’s upgrade to the latest tagged version


```sh
$ go get k8s.io/api
go: k8s.io/api upgrade => v0.23.6
go: downloading k8s.io/api v0.23.6
```

Now check that it is upgraded
```sh
$ go list -m all | grep k8s.io/api
k8s.io/api v0.23.6
...
```

# Install on Ubuntu
## Install Go
Download the Go binary file:
```sh
cd /tmp
wget https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz
```

(Check [here](https://golang.org/dl/) for the latest version)  

Extract the downloaded archive and install it to the desired location on the system. Conventional way is to install into `/usr/local`.
```sh
sudo tar -xvf go1.14.2.linux-amd64.tar.gz
sudo mv go /usr/local
```
## Setup Go env
Set up Go language environment variables `GOROOT`, `GOPATH` and `PATH`.

`GOROOT` is the location where the Go package is installed.
`GOPATH` is the location of the working directory. For example, here directory is ~/go .

Add a global variable to .bashrc file:
```sh
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```
```sh
source .bashrc
```

## Verify installation

```sh
go version
```
```sh
go env
```

```
GOARCH="amd64"
GOBIN=""
GOCACHE="/home/ridham/.cache/go-build"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOOS="linux"
GOPATH="/home/ridham/go"
GOPROXY=""
GORACE=""
GOROOT="/usr/local/go"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"
GCCGO="gccgo"
CC="gcc"
CXX="g++"
CGO_ENABLED="1"
GOMOD=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build476037005=/tmp/go-build -gno-record-gcc-switches"
```
