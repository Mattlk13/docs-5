# What is ArangoDB?

ArangoDB is a scalable graph database system to drive value from connected data, faster. Native graphs, an integrated search engine, and JSON support, via a single query language.

ArangoDB runs everywhere: On-prem, in the cloud, and as a managed cloud service: [ArangoGraph Insights Platform](https://cloud.arangodb.com/home).

> [arangodb.com](https://arangodb.com)

%%LOGO%%

## Key Features in ArangoDB

**Native Graph** Store both data and relationships, for faster queries even with multiple levels of joins and deeper insights that simply aren't possible with traditional relational and document database systems.

**Document Store** Every node in your graph is a JSON document: flexible, extensible, and easily imported from your existing document database.

**ArangoSearch** Natively integrated cross-platform indexing, text-search and ranking engine for information retrieval, optimized for speed and memory.

#### ArangoDB Documentation

-	[Learn ArangoDB](https://arangodb.com/learn/)
-	[Documentation](https://docs.arangodb.com/)

## How to use this image

### Start an ArangoDB instance

In order to start an ArangoDB instance, run:

```console
docker run -d -p 8529:8529 -e ARANGO_RANDOM_ROOT_PASSWORD=1 --name arangodb-instance %%IMAGE%%
```

Docker chooses the processor architecture for the image that matches your host CPU by default. If this is not the case, for example, because you have the `DOCKER_DEFAULT_PLATFORM` environment variable set to a different architecture, you can pass the `--platform` flag to the `docker run` command to specify the appropriate operating system and architecture for the container. For x86-64, use `linux/amd64`. On ARM, especially Apple silicon with no emulation for the required AVX instruction set extension, use `linux/arm64/v8`:

```console
docker run -d -p 8529:8529 -e ARANGO_RANDOM_ROOT_PASSWORD=1 --name arangodb-instance --platform linux/arm64/v8 %%IMAGE%%
```

This creates and launches the %%IMAGE%% Docker instance as a background process. The Identifier of the process is printed. By default, ArangoDB listens on port `8529` for requests.

In order to get the IP ArangoDB listens on, run:

```console
docker inspect --format '{{ .NetworkSettings.IPAddress }}' arangodb-instance
```

### Initialize the server language

When using Docker, you need to specify the language you want to initialize the server to on the first run in one of the following ways:

-	Set the environment variable `LANG` to a locale in the `docker run` command, e.g. `-e LANG=sv` for a Swedish locale.

-	Use an `arangod.conf` configuration file that sets a language and mount it into the container. For example, create a configuration file on your host system in which you set `icu-language = sv` at the top (before any `[section]`) and then mount the file over the default configuration file like `docker run -v /your-host-path/arangod.conf:/etc/arangodb3/arangod.conf ...`.

If you don't specify a language explicitly, the default is `en_US` up to ArangoDB v3.11 and `en_US_POSIX` from ArangoDB v3.12 onward.

### Using the instance

To use the running instance from an application, link the container:

```console
docker run -e ARANGO_RANDOM_ROOT_PASSWORD=1 --name my-app --link arangodb-instance:db-link %%IMAGE%%
```

This uses the instance named `arangodb-instance` and links it into the application container. The application container contains environment variables, which can be used to access the database.

	DB_LINK_PORT_8529_TCP=tcp://172.17.0.17:8529
	DB_LINK_PORT_8529_TCP_ADDR=172.17.0.17
	DB_LINK_PORT_8529_TCP_PORT=8529
	DB_LINK_PORT_8529_TCP_PROTO=tcp
	DB_LINK_NAME=/naughty_ardinghelli/db-link

### Exposing the port to the outside world

If you want to expose the port to the outside world, run:

```console
docker run -e ARANGO_RANDOM_ROOT_PASSWORD=1 -p 8529:8529 -d %%IMAGE%%
```

ArangoDB listen on port 8529 for request and the image includes `EXPOSE
8529`. The `-p 8529:8529` exposes this port on the host.

### Choosing an authentication method

The ArangoDB image provides several authentication methods which can be specified via environment variables (-e) when using `docker run`

1.	`ARANGO_RANDOM_ROOT_PASSWORD=1`

	Generate a random root password when starting. The password will be printed to stdout (may be inspected later using `docker logs`)

2.	`ARANGO_NO_AUTH=1`

	Disable authentication. Useful for testing.

	**WARNING** Doing so in production will expose all your data. Make sure that ArangoDB is not directly accessible from the internet!

3.	`ARANGO_ROOT_PASSWORD=somepassword`

	Specify your own root password.

Note: this way of specifying logins only applies to single server installations. With clusters you have to provision the users via the root user with empty password once the system is up.

### Command line options

You can pass arguments to the ArangoDB server by appending them to the end of the Docker command:

```console
docker run -e ARANGO_RANDOM_ROOT_PASSWORD=1 %%IMAGE%% --help
```

The entrypoint script starts the `arangod` binary by default and forwards your arguments.

You may also start other binaries, such as the ArangoShell:

```console
docker run -it %%IMAGE%% arangosh --server.database myDB ...
```

Note that you need to set up networking for containers if `arangod` runs in one container and you want to access it with `arangosh` running in another container. It is easier to execute it in the same container instead. Use `docker ps` to find out the container ID / name of a running container:

```console
docker ps
```

It prints something similar to the following:

```console
CONTAINER ID   IMAGE     COMMAND                 CREATED      STATUS      PORTS                   NAMES
1234567890ab   arangodb  "/entrypoint.sh aran…"  2 hours ago  Up 2 hours  0.0.0.0:8529->8529/tcp  jolly_joker
```

Then use `docker exec` and the ID / name to run something inside of the existing container:

```console
docker exec -it jolly_joker arangosh
```

For more information, see the ArangoDB documentation about [Configuration](https://docs.arangodb.com/stable/operations/administration/configuration/).

### Limiting resource utilization

`arangod` checks the following environment variables, which can be used to restrict how much memory and how many CPU cores it should use:

-	`ARANGODB_OVERRIDE_DETECTED_TOTAL_MEMORY` *(introduced in v3.6.3)*

	This variable can be used to override the automatic detection of the total amount of RAM present on the system. One can specify a decimal number (in bytes). Furthermore, if `G` or `g` is appended, the value is multiplied by `2^30`. If `M` or `m` is appended, the value is multiplied by `2^20`. If `K` or `k` is appended, the value is multiplied by `2^10`. That is, `64G` means 64 gigabytes.

	The total amount of RAM detected is logged as an INFO message at server start. If the variable is set, the overridden value is shown. Various default sizes are calculated based on this value (e.g. RocksDB buffer cache size).

	Setting this option can in particular be useful in two cases:

	1.	If `arangod` is running in a container and its cgroup has a RAM limitation, then one should specify this limitation in this environment variable, since it is currently not automatically detected.
	2.	If `arangod` is running alongside other services on the same machine and thus sharing the RAM with them, one should limit the amount of memory using this environment variable.

-	`ARANGODB_OVERRIDE_DETECTED_NUMBER_OF_CORES` *(introduced in v3.7.1)*

	This variable can be used to override the automatic detection of the number of CPU cores present on the system.

	The number of CPU cores detected is logged as an INFO message at server start. If the variable is set, the overridden value is shown. Various default values for threading are calculated based on this value.

	Setting this option is useful if `arangod` is running in a container or alongside other services on the same machine and shall not use all available CPUs.

## Persistent Data

ArangoDB supports two different storage engines from version 3.2 to 3.6. You can choose them while instantiating the container with the environment variable `ARANGO_STORAGE_ENGINE`. With `mmfiles` you choose the classic storage engine (not available in 3.7 and later), `rocksdb` will choose the storage engine based on [RocksDB](http://rocksdb.org/). The default choice is `rocksdb` from version 3.4 on.

ArangoDB uses the volume `/var/lib/arangodb3` as database directory to store the collection data and the volume `/var/lib/arangodb3-apps` as apps directory to store any extensions. These directories are marked as docker volumes.

See `docker inspect --format "{{ .Config.Volumes }}" %%IMAGE%%` for all volumes.

A good explanation about persistence and docker container can be found here: [Docker In-depth: Volumes](http://container42.com/2014/11/03/docker-indepth-volumes/), [Why Docker Data Containers are Good](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e)

### Using host directories

You can map the container's volumes to a directory on the host, so that the data is kept between runs of the container. This path `/tmp/arangodb` is in general not the correct place to store you persistent files - it is just an example!

```console
unix> mkdir /tmp/arangodb
unix> docker run -e ARANGO_RANDOM_ROOT_PASSWORD=1 -p 8529:8529 -d \
        -v /tmp/arangodb:/var/lib/arangodb3 \
        %%IMAGE%%
```

This will use the `/tmp/arangodb` directory of the host as database directory for ArangoDB inside the container.

### Using a data container

Alternatively you can create a container holding the data.

```console
docker create --name arangodb-persist %%IMAGE%% true
```

And use this data container in your ArangoDB container.

```console
docker run -e ARANGO_RANDOM_ROOT_PASSWORD=1 --volumes-from arangodb-persist -p 8529:8529 %%IMAGE%%
```

If want to save a few bytes you can alternatively use [busybox](https://hub.docker.com/_/busybox) or [alpine](https://hub.docker.com/_/alpine) for creating the volume only containers. Please note that you need to provide the used volumes in this case. For example

```console
docker run -d --name arangodb-persist -v /var/lib/arangodb3 busybox true
```

### Usage as a base image

If you are using the image as a base image please make sure to wrap any CMD in the [exec](https://docs.docker.com/reference/dockerfile/#cmd) form. Otherwise the default entrypoint will not do its bootstrapping work.

When deriving the image, you can control the instantiation via putting files into `/docker-entrypoint-initdb.d/`.

-	`*.sh` - files ending with .sh will be run as a bash shellscript.
-	`*.js` - files will be executed with arangosh. You can specify additional arangosh arguments via the `ARANGOSH_ARGS` environment variable.
-	`dumps/` - in this directory you can place subdirectories containing database dumps generated using [arangodump](https://docs.arangodb.com/stable/components/tools/arangodump/). They can be restored using [arangorestore](https://docs.arangodb.com/stable/components/tools/arangorestore/).
