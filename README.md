# docker-dogecoin
A [dogecoin](https://dogecoin.com) docker image.

## Tags
- `1.14.2`, `latest` ([1.14.2/Dockerfile](https://github.com/um1st/docker-dogecoin/blob/master/1.14.2/Dockerfile))

## Usage

### How to use this image

This image contains the main binaries from the Dogecoin project - `dogecoind`, `dogecoin-cli` and `dogecoin-tx`. 
It behaves like a binary, so you can pass any arguments to the image, and they will be forwarded to the `dogecoind` binary:

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