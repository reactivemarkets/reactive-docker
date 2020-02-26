# Matchbox

## FIX Sessions

Client FIX applications can connect to the test-harness by initiating a TCP connection to port 8282
on localhost. All FIX sessions should be configured with the following properties:

| Name              |     Value |
|-------------------|-----------|
| BeginString       |   FIX.4.4 |
| HeartBtInt        |        30 |
| ConnectionType    | initiator |
| SocketConnectPort |      8282 |
| SocketConnectHost | 127.0.0.1 |

The test-harness supports the following FIX sessions:

| Name          | Type        | SenderCompID | TargetCompID |
|---------------|-------------|--------------|--------------|
| Maker 1       | Maker       | MAKER1       | MATCHBOX     |
| Market Data 1 | Market Data | TAKERMD1     | MATCHBOX     |
| Market Data 2 | Market Data | TAKERMD2     | MATCHBOX     |
| Taker 1       | Taker       | TAKER1       | MATCHBOX     |
| Taker 2       | Taker       | TAKER2       | MATCHBOX     |

N.B. the SenderCompID and TargetCompID in the table above are from the customer's perspective, so
they can be mapped directly into customer FIX session configuration.
