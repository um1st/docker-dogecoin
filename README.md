# docker-dogecoin

A [dogecoin](https://dogecoin.com) docker image.

## Tags

- `1.14.2`, `latest` ([1.14/Dockerfile](https://github.com/um1st/docker-dogecoin/blob/master/1.14/Dockerfile))
- `1.14` *(hereinafter repeats blockchain releases, without `.0` at the end)* ([1.14/Dockerfile](https://github.com/um1st/docker-dogecoin/blob/master/1.14/Dockerfile))
- `1.10.0` ([1.14/Dockerfile](https://github.com/um1st/docker-dogecoin/blob/master/1.10/Dockerfile))
- `1.8.3` ([1.8/Dockerfile](https://github.com/um1st/docker-dogecoin/blob/master/1.8/Dockerfile))

## Usage

### How to use this image

This image contains the main binaries from the Dogecoin project - `dogecoind`, `dogecoin-cli` and `dogecoin-tx`. It
behaves like a binary, so you can pass any arguments to the image, and they will be forwarded to the `dogecoind` binary:

```shell
docker run --rm -it um1st/dogecoind -printtoconsole -server -regtest -txindex -rpcuser=username -rpcpassword=password -rpcallowip=0.0.0.0/0
```

`docker-compose` example:

```yaml
version: '3'

services:
  dogecoin:
    image: um1st/dogecoind
    volumes:
      - dogecoin:/home/dogecoin/.dogecoin
    ports:
      - "18332:18332"
    command:
      -printtoconsole
      -server
      -regtest
      -txindex
      -rpcuser=username
      -rpcpassword=password
      -rpcallowip=0.0.0.0/0

volumes:
  dogecoin:
```

## Local build

Before build, you need to choose a version and a proper directory for it.
Every `Dockerfile` has a build argument `DOGECOIN_VERSION`, default value is the latest version for the directory.

### The latest version for a directory

1.14.x:

```shell
docker build -f 1.14/Dockerfile -t um1st/dogecoind:1.14-latest ./1.14
```

### A specified version for a directory

1.14.1:

```shell
docker build -f 1.14/Dockerfile --build-arg DOGECOIN_VERSION=v1.14.1 -t um1st/dogecoind:1.14.1 ./1.14
```
