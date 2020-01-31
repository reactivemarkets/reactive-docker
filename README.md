# Toolbox Test

The purpose of this project is to provide customers with a test-harness that can be used for FIX
integration testing.

Once the test-harness is running, customers can:

- initiate FIX connections to the test-harness.
- interact with the test-harness using Redis commands.
- receive market-data requests from the test-harness.
- send market-data snapshots to the test-harness.
- test the new order single workflow.

## Prerequisites

To use this test-harness, you will need to obtain the following from your Reactive Markets
representative:

- latest Reactive FIX specification.
- secure token for the Reactive container registry.

## Container Registry

Access to the Reactive container registry is available to customers and partners of Reactive
Markets. Login to the Reactive container registry using the username and token provided by your
account manager:

```bash
docker login -u <username> -p <token> registry.gitlab.com
```

Please contact [support@reactivemarkets.com](mailto:support@reactivemarkets.com) if you unable to
access the registry with the token provided.

## Container Commands

Run the Docker compose `up` command to create and start all containers:

```bash
docker-compose up
```

This command will start all containers in the foreground. Use the `-d` option to start them in the
background, and the `logs` command to tail the logs:

```bash
docker-compose up -d
docker-compose logs -f
```

Containers running in the background should be stopped using the `down` command:

```bash
docker-compose down
```

The following Docker `prune` command will purge all containers, networks, volumes and images:

```bash
docker system prune --all --volumes
```

This can be useful to ensure a pristine system restart.

## FIX Sessions

Client FIX applications can connect to the test-harness by initiating a TCP connection to port 8285
on localhost. All FIX sessions should be configured with the following properties:

| Name              |     Value |
|-------------------|-----------|
| BeginString       |   FIX.4.4 |
| HeartBtInt        |        30 |
| ConnectionType    | initiator |
| SocketConnectPort |      8285 |
| SocketConnectHost | 127.0.0.1 |

The test-harness supports the following FIX sessions:

| Name             | SenderCompID | TargetCompID |
|------------------|--------------|--------------|
| Currenex Quote   | MAKERMD_CNX  | TAKERMD_CNX  |
| Currenex Order   | MAKER_CNX    | TAKER_CNX    |
| EBS London Quote | MAKERMD_EBL  | TAKERMD_EBL  |
| EBS London Order | MAKER_EBL    | TAKER_EBL    |
| FXCM Quote       | MAKERMD_FXC  | TAKERMD_FXC  |
| FXCM Order       | MAKER_FXC    | TAKER_FXC    |
| Hotspot FX Quote | MAKERMD_HSP  | TAKERMD_HSP  |
| Hotspot FX Order | MAKER_HSP    | TAKER_HSP    |

N.B. the SenderCompID and TargetCompID in the table above are from the customer's perspective, so
they can be mapped directly into customer FIX session configuration.

## QuickFIX Example

The [Toolbox Java](https://github.com/reactivemarkets/toolbox-java) project contains a simple,
[QuickFIX](https://www.quickfixj.org/) application that can be used for basic connectivity testing:

```bash
git clone git@github.com:reactivemarkets/toolbox-java.git
cd toolbox-java/
gradle build
java -jar build/libs/toolbox-java-x.x-SNAPSHOT-all.jar
```

This sample application can also be run using the Gradle "run" target. This is not recommended,
however, because the Gradle wrapper does not forward signals correctly to the Java application.

## Redis Commands

The Redis Command-Line Interface (CLI) can be used to interact with the test-harness.
To enter the Redis CLI, first run:

```bash
docker exec -it redis redis-cli
```

Command commands are documented in the sections below.

### Reference Data

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

### Subscriptions

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

### Orders

Orders will be rejected if the user's trading account does not exist or there are insufficient funds
in the trading account.

Create an account and deposit funds into the account:

```bash
> XADD bucket.stream:1 * msg_type 30 symbol 1
> XADD bucket.stream:1 * msg_type 29 accnt 1 action 1 asset USD 50000000
```

Submit a Fill Or Kill (FOK) order:

```bash
> XADD bucket.stream:1 * msg_type 19 accnt 1 market EURUSD-EBL cl_order_id test strat_type FOK side 1 qty 1000000 price 1.1026
```
