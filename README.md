# Docker

The Docker compose file in this directory aims to provide customers with a test-harness that can be
used for FIX integration testing.

Once the test-harness is running, users can:

- initiate FIX connections to the test-harness.
- send messages to the test-harness via Redis.
- receive market-data requests from the test-harness.
- send market-data snapshots to the test-harness.
- test the new order single workflow.

## Prerequisites

You will need the following from your Reactive Markets representative.

- the latest Reactive FIX specification.
- a secure token for the Reactive container registry.
- passwords for database container images.

Recommended: customers should also install the Redis command-line interface for sending instructions
to the test-harness.

## Container Registry

Access to the Reactive container registry is available to customers and partners of Reactive
Markets. Login to the Reactive container registry using the username and token provided by your
account manager:

```bash
$ docker login -u <username> -p <token> registry.gitlab.com
```

Please contact <mailto:support@reactivemarkets.com> if you unable to access the registry with the
token provided.

## Container Passwords

The Docker compose file runs several containers, including a database. Databases provided by the
database container are password protected. Passwords can be specified by setting environment
variables in a `.env` file alongside the `docker-compose.yml` file.

For example:

```bash
$ MYSQL_ROOT_PASSWORD=XXXX >>.env
$ REACTIVE_SQL_PASS=XXXX >>.env
```

Once set, verify that the passwords are correct expanded by the `docker-compose` command:

```bash
$ cat .env
$ docker-compose config | grep -i pass
```

## FIX Sessions

The test-harness exposes the following FIX sessions:

| Name             | SenderCompID | TargetCompID |
|------------------+--------------+--------------|
| Currenex Quote   | MAKERMD_CNX  | TAKERMD_CNX  |
| Currenex Order   | MAKER_CNX    | TAKER_CNX    |
| EBS London Quote | MAKERMD_EBL  | TAKERMD_EBL  |
| EBS London Order | MAKER_EBL    | TAKER_EBL    |
| FXCM Quote       | MAKERMD_FXC  | TAKERMD_FXC  |
| FXCM Order       | MAKER_FXC    | TAKER_FXC    |
| Hotspot FX Quote | MAKERMD_HSP  | TAKERMD_HSP  |
| Hotspot FX Order | MAKER_HSP    | TAKER_HSP    |

N.B. the SenderCompID and TargetCompID in the table above are from the customer's perspective,
so they can be mapped directly into customer FIX session configuration.

## QuickFIX Example

The [Toolbox Java](https://github.com/reactivemarkets/toolbox-java) project contains a simple,
[QuickFIX](https://www.quickfixj.org/) application that can be used for basic connectivity testing:

```bash
$ git clone git@github.com:reactivemarkets/toolbox-java.git
$ cd toolbox-java/
$ gradle build
$ java -jar build/libs/toolbox-java-x.x-SNAPSHOT-all.jar
```

This sample application can also be run using the Gradle "run" target. This is not recommended,
however, because the Gradle wrapper does not forward signals correctly to the Java application.

## Reference Data

List all venues, instruments and markets:

```bash
> ZRANGE venues.index 0 -1
> ZRANGE instrs.index 0 -1
> ZRANGE markets.index 0 -1
```

Print all fields for a particular market:

```bash
> HGETALL markets:EURUSD-EBL
```

## Subscriptions

Add a market-data subscription:

```bash
> SET "sub.bucket.l2:1::EURUSD-EBL" ""
```

Or with a one minute expiry time:

```bash
> SET "sub.bucket.l2:1::EURUSD-EBL" "" EX 60
```

Remove a market-data subscription:

```bash
> DEL "sub.bucket.l2:1::EURUSD-EBL"
```
