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

## Database Schema
1. PostgreSQL
```SQL
CREATE TABLE IF NOT EXISTS ServerInfo (uniquerow INTEGER DEFAULT 1, id VARCHAR(1024), proto BYTEA, version INTEGER, PRIMARY KEY (uniquerow));
CREATE TABLE IF NOT EXISTS Clients (id VARCHAR(1024), hbinbox TEXT, PRIMARY KEY (id));
CREATE TABLE IF NOT EXISTS Channels (id INTEGER, name VARCHAR(1024) NOT NULL, maxseq BIGINT DEFAULT 0, maxmsgs INTEGER DEFAULT 0, maxbytes BIGINT DEFAULT 0, maxage BIGINT DEFAULT 0, deleted BOOL DEFAULT FALSE, PRIMARY KEY (id));
CREATE INDEX Idx_ChannelsName ON Channels (name(256));
CREATE TABLE IF NOT EXISTS Messages (id INTEGER, seq BIGINT, timestamp BIGINT, size INTEGER, data BYTEA, CONSTRAINT PK_MsgKey PRIMARY KEY(id, seq));
CREATE INDEX Idx_MsgsTimestamp ON Messages (timestamp);
CREATE TABLE IF NOT EXISTS Subscriptions (id INTEGER, subid BIGINT, lastsent BIGINT DEFAULT 0, proto BYTEA, deleted BOOL DEFAULT FALSE, CONSTRAINT PK_SubKey PRIMARY KEY(id, subid));
CREATE TABLE IF NOT EXISTS SubsPending (subid BIGINT, row BIGINT, seq BIGINT DEFAULT 0, lastsent BIGINT DEFAULT 0, pending BYTEA, acks BYTEA, CONSTRAINT PK_MsgPendingKey PRIMARY KEY(subid, row));
CREATE INDEX Idx_SubsPendingSeq ON SubsPending (seq);
CREATE TABLE IF NOT EXISTS StoreLock (id VARCHAR(30), tick BIGINT DEFAULT 0);

-- Updates for 0.10.0
ALTER TABLE Clients ADD proto BYTEA;
```
2. MySQL
```SQL
CREATE TABLE IF NOT EXISTS ServerInfo (uniquerow INT DEFAULT 1, id VARCHAR(1024), proto BLOB, version INTEGER, PRIMARY KEY (uniquerow));
CREATE TABLE IF NOT EXISTS Clients (id VARCHAR(1024), hbinbox TEXT, PRIMARY KEY (id(256)));
CREATE TABLE IF NOT EXISTS Channels (id INTEGER, name VARCHAR(1024) NOT NULL, maxseq BIGINT UNSIGNED DEFAULT 0, maxmsgs INTEGER DEFAULT 0, maxbytes BIGINT DEFAULT 0, maxage BIGINT DEFAULT 0, deleted BOOL DEFAULT FALSE, PRIMARY KEY (id), INDEX Idx_ChannelsName (name(256)));
CREATE TABLE IF NOT EXISTS Messages (id INTEGER, seq BIGINT UNSIGNED, timestamp BIGINT, size INTEGER, data BLOB, CONSTRAINT PK_MsgKey PRIMARY KEY(id, seq), INDEX Idx_MsgsTimestamp (timestamp));
CREATE TABLE IF NOT EXISTS Subscriptions (id INTEGER, subid BIGINT UNSIGNED, lastsent BIGINT UNSIGNED DEFAULT 0, proto BLOB, deleted BOOL DEFAULT FALSE, CONSTRAINT PK_SubKey PRIMARY KEY(id, subid));
CREATE TABLE IF NOT EXISTS SubsPending (subid BIGINT UNSIGNED, `row` BIGINT UNSIGNED, seq BIGINT UNSIGNED DEFAULT 0, lastsent BIGINT UNSIGNED DEFAULT 0, pending BLOB, acks BLOB, CONSTRAINT PK_MsgPendingKey PRIMARY KEY(subid, `row`), INDEX Idx_SubsPendingSeq(seq));
CREATE TABLE IF NOT EXISTS StoreLock (id VARCHAR(30), tick BIGINT UNSIGNED DEFAULT 0);

# Updates for 0.10.0
ALTER TABLE Clients ADD proto BLOB;
```

## Reference
1. [Nats Docs](https://docs.nats.io)
2. [Docker Image](https://hub.docker.com/_/nats-streaming)
3. [Docker SH](https://github.com/fwidjaya20/docker-sh)
4. [Nats DB Persistence](https://developpaper.com/nats-nats-streaming-persistence)
