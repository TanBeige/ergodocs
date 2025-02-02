---
tags:
  - P2P
---

A simple implementation of VLQ and ZigZag can be found [here](https://gist.github.com/satsen/5e7bcc38565ad193cf7d906a856f804e), simply use the methods (you do not have to worry about whether it is ZigZag encoded or not).

# Network Messages


## Message format

----------------

Every message in the P2P protocol has the following format:

| Data type         | Field name              | Details                                                                                                                   |  
|:------------------|:------------------------|:--------------------------------------------------------------------------------------------------------------------------|
| byte\[4\]         | Magic bytes             | For the mainnet, the magic bytes are `{1, 0, 2, 4}`. For testnet, `{2, 0, 0, 1}`.                                         |              
| unsigned byte     | Message code            | One byte describing message type                                                                                          |
| int               | Message body length     | No `VLQ` and `ZigZag` encoding is used for message length (for historical reasons); bytes are coming in big-endian order. |
| byte\[4\]         | Handshake body checksum | First four bytes of blake2b(message body)                                                                                 |                                        
| byte\[bodyLength] | Message body            | Message body                                                                                                              |

# Records

----------------

## Peer

| Data type               | Field name                    | Details                                                           |
|:------------------------|:------------------------------|:------------------------------------------------------------------|
| unsigned byte           | *Length of next field*        |
| UTF-8 String            | Agent name                    |
| byte\[3\]               | Version                       | For example, `{4, 0, 25}`                                         |
| unsigned byte           | *Length of next field*        |
| UTF-8 String            | Peer name                     |
| boolean                 | Whether public address exists |
| (unsigned byte)         | Length of the IP **plus 4**   | When decoding, subtract the value with 4 to get the actual length |
| (byte\[ipLength - 4\])  | The public IP address         |
| (VLQ unsigned **int**)  | Port                          |
| unsigned byte           | Count of peer features        |
| Feature\[featureCount\] | Features                      |

## Feature

| Data type          | Field name   |
|:-------------------|:-------------|
| unsigned byte      | Feature code |
| VLQ unsigned short | Body length  |
| byte\[bodyLength\] | Body         |

## Modifier (Record)

| Data type            | Field name       |
|:---------------------|:-----------------|
| byte\[32\]           | Modifier type ID |
| VLQ unsigned int     | Length of object |
| byte\[objectLength\] | Object           |

## Header
| Data type          | Field name      |
|:-------------------|:----------------|
| VLQ unsigned short | Length of bytes |
| byte\[length]      | Bytes           |

# Messages

----------------

## Get Peers

Code = 1

Body is empty.

## Peers

Code = 2

Sent in response to Get Peers. Contains all the peers that are currently connected to. Used for node discovery.

| Data type         | Field name     |
|:------------------|:---------------|
| VLQ ZZ int        | Count of peers |
| [Peer](#peer)\[\] | Peers          |

## Sync Info

### Sync Info (new)

Code = 65

Added in 4.0.16. It is sent only to nodes that report a version of or higher than 4.0.16. For older nodes, [Sync Info (old)](#sync-info-old) is sent.

Requests an [Inv](#inv) message that provides modifier IDs required to the sender to synchronize his or her blockchain with the recipient. It allows a peer which has been disconnected or started for the first time to get the data it needs to request the blocks it hasn't seen.

| Data type                        | Field name              |
|:---------------------------------|:------------------------|
| VLQ unsigned short               | The constant value `0`  |
| byte                             | The constant value `-1` |
| unsigned byte                    | Count of headers        |
| [Header](#header)\[headerCount\] | Headers                 |

### Sync Info (old)

Code = 65

The old (before 4.0.16) version of the Sync Info message.

| Data type                       | Field name                       |
|:--------------------------------|:---------------------------------|
| VLQ unsigned short              | Count of last header IDs         |
| byte\[32\]\[lastHeaderIdCount\] | Last header IDs (ID byte arrays) |

## Inv

Code = 55

Transmits one or more inventories of objects known to the transmitting peer.

It can be sent unsolicited to announce new transactions or blocks, or it can be sent in reply to a [Sync Info](#sync-info) message.

| Data type                  | Field name                |
|:---------------------------|:--------------------------|
| unsigned byte              | Type ID                   |
| VLQ unsigned int           | Count of elements         |
| byte\[32\]\[elementCount\] | Elements (ID byte arrays) |

## Request Modifier

Code = 22

Requests one or more modifiers from another node. The objects are requested by an inventory, which the requesting node typically received previously with an [Inv](#inv) message.

This message cannot be used to request arbitrary data, such as historic transactions no longer in the memory pool. Full nodes may not even be able to provide older blocks if they've pruned old transactions from their block database.

For this reason, this message should usually only be used to request data from a node which previously advertised it had that data by sending an [Inv](#inv) message.

| Data type                  | Field name                |
|:---------------------------|:--------------------------|
| unsigned byte              | Modifier type ID          |
| VLQ unsigned int           | Count of elements         |
| byte\[32\]\[elementCount\] | Elements (ID byte arrays) |

## Modifier

Code = 33

Sent in response to Request Modifier.

| Data type                                     | Field name         |
|:----------------------------------------------|:-------------------|
| unsigned byte                                 | Type ID            |
| VLQ unsigned int                              | Count of modifiers |
| [Modifier](#modifier-record)\[modifierCount\] | Modifiers          |

# PRs

- [NiPoPoW powered bootstrapping #1365](https://github.com/ergoplatform/ergo/issues/1365)

# Tests

[Tests for parsing networking messages against test vectors #1264](https://github.com/ergoplatform/ergo/pull/1264)

This PR contains :

- assert() swapped with require() as the latter is non-elidable
- unused parameter nextBlockTimestampOpt is removed from HeadersProcessor.requiredDifficultyAfter()

Different test vectors and simple parsers:

- invalid PoW solution validation test for manually checked test vector
- handshake parsing test for a test vector (bytes got from a 3.3.6 mainnet node)
- SyncInfo networking message parsing test (can be used as a simple spec)
- INV networking message parsing test (can be used as a simple spec)

Demo applications:

- to generate address: [AddressGenerationDemo](https://github.com/ergoplatform/ergo/blob/master/ergo-wallet/src/test/java/org/ergoplatform/wallet/AddressGenerationDemo.java)
- transaction JSON printing: [CreateTransactionDemo](https://github.com/ergoplatform/ergo/blob/master/ergo-wallet/src/test/java/org/ergoplatform/wallet/CreateTransactionDemo.java)