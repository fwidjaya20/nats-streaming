# Nats Streaming (Message Broker)
NATS Streaming is an open-source, high-performance, cloud native messaging streaming system.

##### Prerequisites
- Docker
- [Docker SH](https://github.com/fwidjaya20/docker-sh)

## Docker Image
You can get the image from the official [Docker Hub](https://hub.docker.com/_/nats-streaming).

## How to
### Run
1. Manual Shell Command:
```shell
docke run -it -d -p 4222:4222 -p 8222:8222 --name nats_streaming nats-streaming:latest --mm 0 --msu 0 --mb 0 -st SQL --sql_driver [YOUR_DB_DRIVER] --sql_source '[DRIVER]://[USERNAME]:[PASSWORD]@[LOCALHOST|IP]:[PORT]/[DB_NAME]?sslmode=disable'
```
2. Using Docker SH:
**Create the Bash File**:
```shell
vim nats_streaming
```
**Paste the Shells below**:
```shell
#!/usr/bin/env docker.sh

name=nats_streaming
image=nats-streaming:latest
opts="
	-p 4222:4222
	-p 8222:8222
"
args="
	-st SQL
	-msu 0
	-mm 0
	-mb 0
	--sql_driver [YOUR_DB_DRIVER]
	--sql_source '[DRIVER]://[USERNAME]:[PASSWORD]@[LOCALHOST|IP]:[PORT]/[DB_NAME]?sslmode=disable'
"
```
**Grant the `executable` access**:
```shell
chmod +x nats_streaming
```
**Run it**
```shell
./nats_streaming start
```

## Reference
1. [Nats Docs](https://docs.nats.io)
2. [Docker Image](https://hub.docker.com/_/nats-streaming)
3. [Docker SH](https://github.com/fwidjaya20/docker-sh)
