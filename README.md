# FerretDB

[![Go](https://github.com/FerretDB/FerretDB/actions/workflows/go.yml/badge.svg)](https://github.com/FerretDB/FerretDB/actions/workflows/go.yml)
[![Docker](https://github.com/FerretDB/FerretDB/actions/workflows/docker.yml/badge.svg)](https://github.com/FerretDB/FerretDB/actions/workflows/docker.yml)
[![codecov](https://codecov.io/gh/FerretDB/FerretDB/branch/main/graph/badge.svg?token=JZ56XFT3DM)](https://codecov.io/gh/FerretDB/FerretDB)

FerretDB (previously MangoDB) was founded to become the de-facto open-source substitute to MongoDB.
FerretDB is an open-source proxy, converting the MongoDB wire protocol queries to SQL - using PostgreSQL as a database engine.


## Why do we need FerretDB?

MongoDB was originally an eye-opening technology for many of us developers, empowering us to build applications faster than using relational databases.
In its early days, its ease-to-use and well-documented drivers made MongoDB one of the simplest database solutions available.
However, as time passed, MongoDB abandoned its open-source roots; changing the license to SSPL - making it unusable for many open source and early stage commercial projects.

Most MongoDB users are not in need of many advanced features offered by MongoDB; however, they are in need of an easy to use open-source database solution.
Recognizing this, FerretDB is here to fill that gap.


## Scope

FerretDB will be compatible with MongoDB drivers and will strive to serve as a drop-in replacement for MongoDB.


## Current state

What you see here is a tech demo - intended to show a proof of concept.
Over the next couple of months we will be adding more.
See [this example](https://github.com/FerretDB/example) for a short demonstration.

FerretDB is in its very early stages and welcomes all contributors.
See our [CONTRIBUTING.md](CONTRIBUTING.md).


## Quickstart

These steps describe a quick local setup.
They are not suitable for most production use-cases because they keep all data inside containers.

1. Store the following in the `docker-compose.yml` file:

```yaml
version: "3"

services:
  postgres:
    image: postgres:14
    container_name: postgres
    ports:
      - 5432:5432
    environment:
      - POSTGRES_USER=user
      - POSTGRES_DB=ferretdb
      - POSTGRES_HOST_AUTH_METHOD=trust

  postgres_setup:
    image: postgres:14
    container_name: postgres_setup
    restart: on-failure
    entrypoint: ["sh", "-c", "psql -h postgres -U user -d ferretdb -c 'CREATE SCHEMA IF NOT EXISTS test'"]

  ferretdb:
    image: ghcr.io/ferretdb/ferretdb:latest
    container_name: ferretdb
    restart: on-failure
    ports:
      - 27017:27017
    command: ["-listen-addr=:27017", "-postgresql-url=postgres://user@postgres:5432/ferretdb"]

networks:
  default:
    name: ferretdb
```

* `postgres` container runs PostgreSQL 14 that would store data.
* `postgres_setup` container creates a PostgreSQL schema `test` that would act like a FerretDB database of the same name.
* `ferretdb` runs FerretDB.

2. Start services with `docker-compose up -d`.

3. If you have `mongosh` installed, just run it to connect to FerretDB database `test`.
If not, run the following command to run `mongosh` inside the temporary MongoDB container, attaching to the same Docker network:
```
docker run --rm -it --network=ferretdb --entrypoint=mongosh mongo:5 mongodb://ferretdb/
```


## Community

* Website and blog: [https://ferretdb.io](https://ferretdb.io/).
* Twitter: [@ferret_db](https://twitter.com/ferret_db).
* [Slack chat](https://join.slack.com/t/ferretdb/shared_invite/zt-zqe9hj8g-ZcMG3~5Cs5u9uuOPnZB8~A) for quick questions.
* [GitHub Discussions](https://github.com/FerretDB/FerretDB/discussions) for longer topics.
* [GitHub Issues](https://github.com/FerretDB/FerretDB/issues) for bugs and missing features.
* [Open Office House meeting](https://calendar.google.com/event?action=TEMPLATE&tmeid=NjNkdTkyN3VoNW5zdHRiaHZybXFtb2l1OWtfMjAyMTEyMTNUMTgwMDAwWiBjX24zN3RxdW9yZWlsOWIwMm0wNzQwMDA3MjQ0QGc&tmsrc=c_n37tquoreil9b02m0740007244%40group.calendar.google.com&scp=ALL) every Monday at 18:00 UTC at [Google Meet](https://meet.google.com/mcb-arhw-qbq).

If you want to contact FerretDB Inc., please use [this form](https://www.ferretdb.io/contact/).
