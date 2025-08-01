<!--

********************************************************************************

WARNING:

    DO NOT EDIT "clickhouse/README.md"

    IT IS AUTO-GENERATED

    (from the other files in "clickhouse/" combined with a set of templates)

********************************************************************************

-->

# Quick reference

-	**Maintained by**:  
	[ClickHouse Inc.](https://github.com/ClickHouse/ClickHouse)

-	**Where to get help**:  
	[the Docker Community Slack](https://dockr.ly/comm-slack), [Server Fault](https://serverfault.com/help/on-topic), [Unix & Linux](https://unix.stackexchange.com/help/on-topic), or [Stack Overflow](https://stackoverflow.com/help/on-topic)

# Supported tags and respective `Dockerfile` links

-	[`latest`, `jammy`, `25.7`, `25.7-jammy`, `25.7.1`, `25.7.1-jammy`, `25.7.1.3997`, `25.7.1.3997-jammy`](https://github.com/ClickHouse/docker-library/blob/c3c67371961b5efb723ad9b4755f7178b458f9b7/server/25.7.1.3997/Dockerfile.ubuntu)

-	[`25.6`, `25.6-jammy`, `25.6.6`, `25.6.6-jammy`, `25.6.6.29`, `25.6.6.29-jammy`](https://github.com/ClickHouse/docker-library/blob/c3c67371961b5efb723ad9b4755f7178b458f9b7/server/25.6.6.29/Dockerfile.ubuntu)

-	[`25.5`, `25.5-jammy`, `25.5.9`, `25.5.9-jammy`, `25.5.9.14`, `25.5.9.14-jammy`](https://github.com/ClickHouse/docker-library/blob/c3c67371961b5efb723ad9b4755f7178b458f9b7/server/25.5.9.14/Dockerfile.ubuntu)

-	[`lts`, `lts-jammy`, `25.3`, `25.3-jammy`, `25.3.6`, `25.3.6-jammy`, `25.3.6.56`, `25.3.6.56-jammy`](https://github.com/ClickHouse/docker-library/blob/c3c67371961b5efb723ad9b4755f7178b458f9b7/server/25.3.6.56/Dockerfile.ubuntu)

# Quick reference (cont.)

-	**Where to file issues**:  
	[https://github.com/ClickHouse/ClickHouse/issues](https://github.com/ClickHouse/ClickHouse/issues?q=)

-	**Supported architectures**: ([more info](https://github.com/docker-library/official-images#architectures-other-than-amd64))  
	[`amd64`](https://hub.docker.com/r/amd64/clickhouse/), [`arm64v8`](https://hub.docker.com/r/arm64v8/clickhouse/)

-	**Published image artifact details**:  
	[repo-info repo's `repos/clickhouse/` directory](https://github.com/docker-library/repo-info/blob/master/repos/clickhouse) ([history](https://github.com/docker-library/repo-info/commits/master/repos/clickhouse))  
	(image metadata, transfer size, etc)

-	**Image updates**:  
	[official-images repo's `library/clickhouse` label](https://github.com/docker-library/official-images/issues?q=label%3Alibrary%2Fclickhouse)  
	[official-images repo's `library/clickhouse` file](https://github.com/docker-library/official-images/blob/master/library/clickhouse) ([history](https://github.com/docker-library/official-images/commits/master/library/clickhouse))

-	**Source of this description**:  
	[docs repo's `clickhouse/` directory](https://github.com/docker-library/docs/tree/master/clickhouse) ([history](https://github.com/docker-library/docs/commits/master/clickhouse))

# ClickHouse Server Docker Image

## What is ClickHouse?

![logo](https://raw.githubusercontent.com/docker-library/docs/007e3209490145a9855f4825218a9a08753d425b/clickhouse/logo.svg?sanitize=true)

ClickHouse is an open-source column-oriented DBMS (columnar database management system) for online analytical processing (OLAP) that allows users to generate analytical reports using SQL queries in real-time.

ClickHouse works 100-1000x faster than traditional database management systems, and processes hundreds of millions to over a billion rows and tens of gigabytes of data per server per second. With a widespread user base around the globe, the technology has received praise for its reliability, ease of use, and fault tolerance.

For more information and documentation see https://clickhouse.com/.

## Versions

-	The `latest` tag points to the latest release of the latest stable branch.
-	Branch tags like `22.2` point to the latest release of the corresponding branch.
-	Full version tags like `22.2.3` and `22.2.3.5` point to the corresponding release.

### Compatibility

-	The amd64 image requires support for [SSE3 instructions](https://en.wikipedia.org/wiki/SSE3). Virtually all x86 CPUs after 2005 support SSE3.
-	The arm64 image requires support for the [ARMv8.2-A architecture](https://en.wikipedia.org/wiki/AArch64#ARMv8.2-A) and additionally the Load-Acquire RCpc register. The register is optional in version ARMv8.2-A and mandatory in [ARMv8.3-A](https://en.wikipedia.org/wiki/AArch64#ARMv8.3-A). Supported in Graviton >=2, Azure and GCP instances. Examples for unsupported devices are Raspberry Pi 4 (ARMv8.0-A) and Jetson AGX Xavier/Orin (ARMv8.2-A).
-	Since the Clickhouse 24.11 Ubuntu images started using `ubuntu:22.04` as its base image. It requires docker version >= `20.10.10` containing [patch](https://github.com/moby/moby/commit/977283509f75303bc6612665a04abf76ff1d2468). As a workaround you could use `docker run --security-opt seccomp=unconfined` instead, however that has security implications.

## How to use this image

### start server instance

```bash
docker run -d --name some-clickhouse-server --ulimit nofile=262144:262144 clickhouse
```

By default, ClickHouse will be accessible only via the Docker network. See the **networking** section below.

By default, starting above server instance will be run as the `default` user without password.

### connect to it from a native client

```bash
docker run -it --rm --network=container:some-clickhouse-server --entrypoint clickhouse-client clickhouse
# OR
docker exec -it some-clickhouse-server clickhouse-client
```

More information about the [ClickHouse client](https://clickhouse.com/docs/interfaces/cli/).

### connect to it using curl

```bash
echo "SELECT 'Hello, ClickHouse!'" | docker run -i --rm --network=container:some-clickhouse-server buildpack-deps:curl curl 'http://localhost:8123/?query=' -s --data-binary @-
```

More information about the [ClickHouse HTTP Interface](https://clickhouse.com/docs/interfaces/http/).

### stopping / removing the container

```bash
docker stop some-clickhouse-server
docker rm some-clickhouse-server
```

### networking

> ⚠️ Note: the predefined user `default` does not have the network access unless the password is set, see "How to create default database and user on starting" and "Managing `default` user" below

You can expose your ClickHouse running in docker by [mapping a particular port](https://docs.docker.com/config/containers/container-networking/) from inside the container using host ports:

```bash
docker run -d -p 18123:8123 -p19000:9000 -e CLICKHOUSE_PASSWORD=changeme --name some-clickhouse-server --ulimit nofile=262144:262144 clickhouse
echo 'SELECT version()' | curl 'http://localhost:18123/?password=changeme' --data-binary @-
```

`22.6.3.35`

Or by allowing the container to use [host ports directly](https://docs.docker.com/network/host/) using `--network=host` (also allows achieving better network performance):

```bash
docker run -d --network=host --name some-clickhouse-server --ulimit nofile=262144:262144 clickhouse
echo 'SELECT version()' | curl 'http://localhost:8123/' --data-binary @-
```

`22.6.3.35`

> ⚠️ Note: the user `default` in the example above is available only for the localhost requests

### Volumes

Typically you may want to mount the following folders inside your container to achieve persistency:

-	`/var/lib/clickhouse/` - main folder where ClickHouse stores the data
-	`/var/log/clickhouse-server/` - logs

```bash
docker run -d \
    -v "$PWD/ch_data:/var/lib/clickhouse/" \
    -v "$PWD/ch_logs:/var/log/clickhouse-server/" \
    --name some-clickhouse-server --ulimit nofile=262144:262144 clickhouse
```

You may also want to mount:

-	`/etc/clickhouse-server/config.d/*.xml` - files with server configuration adjustments
-	`/etc/clickhouse-server/users.d/*.xml` - files with user settings adjustments
-	`/docker-entrypoint-initdb.d/` - folder with database initialization scripts (see below).

### Linux capabilities

ClickHouse has some advanced functionality, which requires enabling several [Linux capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html).

They are optional and can be enabled using the following [docker command-line arguments](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities):

```bash
docker run -d \
    --cap-add=SYS_NICE --cap-add=NET_ADMIN --cap-add=IPC_LOCK \
    --name some-clickhouse-server --ulimit nofile=262144:262144 clickhouse
```

Read more in [knowledge base](https://clickhouse.com/docs/knowledgebase/configure_cap_ipc_lock_and_cap_sys_nice_in_docker).

## Configuration

The container exposes port 8123 for the [HTTP interface](https://clickhouse.com/docs/interfaces/http_interface/) and port 9000 for the [native client](https://clickhouse.com/docs/interfaces/tcp/).

ClickHouse configuration is represented with a file "config.xml" ([documentation](https://clickhouse.com/docs/operations/configuration_files/))

### Start server instance with custom configuration

```bash
docker run -d --name some-clickhouse-server --ulimit nofile=262144:262144 -v /path/to/your/config.xml:/etc/clickhouse-server/config.xml clickhouse
```

### Start server as custom user

```bash
# $PWD/data/clickhouse should exist and be owned by current user
docker run --rm --user "${UID}:${GID}" --name some-clickhouse-server --ulimit nofile=262144:262144 -v "$PWD/logs/clickhouse:/var/log/clickhouse-server" -v "$PWD/data/clickhouse:/var/lib/clickhouse" clickhouse
```

When you use the image with local directories mounted, you probably want to specify the user to maintain the proper file ownership. Use the `--user` argument and mount `/var/lib/clickhouse` and `/var/log/clickhouse-server` inside the container. Otherwise, the image will complain and not start.

### Start server from root (useful in case of enabled user namespace)

```bash
docker run --rm -e CLICKHOUSE_RUN_AS_ROOT=1 --name clickhouse-server-userns -v "$PWD/logs/clickhouse:/var/log/clickhouse-server" -v "$PWD/data/clickhouse:/var/lib/clickhouse" clickhouse
```

### How to create default database and user on starting

Sometimes you may want to create a user (user named `default` is used by default) and database on a container start. You can do it using environment variables `CLICKHOUSE_DB`, `CLICKHOUSE_USER`, `CLICKHOUSE_DEFAULT_ACCESS_MANAGEMENT` and `CLICKHOUSE_PASSWORD`:

```bash
docker run --rm -e CLICKHOUSE_DB=my_database -e CLICKHOUSE_USER=username -e CLICKHOUSE_DEFAULT_ACCESS_MANAGEMENT=1 -e CLICKHOUSE_PASSWORD=password -p 9000:9000/tcp clickhouse
```

#### Managing `default` user

The user `default` has disabled network access by default in the case none of `CLICKHOUSE_USER`, `CLICKHOUSE_PASSWORD`, or `CLICKHOUSE_DEFAULT_ACCESS_MANAGEMENT` are set.

There's a way to make `default` user insecurely available by setting environment variable `CLICKHOUSE_SKIP_USER_SETUP` to 1:

```bash
docker run --rm -e CLICKHOUSE_SKIP_USER_SETUP=1 -p 9000:9000/tcp clickhouse
```

## How to extend this image

To perform additional initialization in an image derived from this one, add one or more `*.sql`, `*.sql.gz`, or `*.sh` scripts under `/docker-entrypoint-initdb.d`. After the entrypoint calls `initdb`, it will run any `*.sql` files, run any executable `*.sh` scripts, and source any non-executable `*.sh` scripts found in that directory to do further initialization before starting the service.  
Also, you can provide environment variables `CLICKHOUSE_USER` & `CLICKHOUSE_PASSWORD` that will be used for clickhouse-client during initialization.

For example, to add an additional user and database, add the following to `/docker-entrypoint-initdb.d/init-db.sh`:

```bash
#!/bin/bash
set -e

clickhouse client -n <<-EOSQL
    CREATE DATABASE docker;
    CREATE TABLE docker.docker (x Int32) ENGINE = Log;
EOSQL
```

# License

View [license information](https://github.com/ClickHouse/ClickHouse/blob/master/LICENSE) for the software contained in this image.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in [the `repo-info` repository's `clickhouse/` directory](https://github.com/docker-library/repo-info/tree/master/repos/clickhouse).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.
