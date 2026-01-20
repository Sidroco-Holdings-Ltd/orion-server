# Orion - a blockchain database
Orion is a **key-value/document database** with certain blockchain properties such as

  - **Tamper Evident**: Data cannot be tampered with, without it going unnoticed. At any point in time, a user can request the database to provide proof for an existance of a transaction or data, and verify the same to ensure data integrity.
  - **Non-Repudiation**: A user who submitted a transaction to make changes to data cannot deny submitting the transaction later.
  - **Crypto-based Authentication**: A user that submitted a query or transaction is always authenticated using digital signature.
  - **Confidentiality and Access Control**: Each data item can have an access control list (ACL) to dictate which users can read from it and which users can write to it. Each user needs to authenticate themselves by providing their digital signature to read or write to data. Depending on the access rule defined for data, sometimes more than one users need to authenticate themselves together to read or write to data.
  - **Serialization Isolation Level**: It ensures a safe and consistent transaction execution.
  - **Provenance Queries**: All historical changes to the data are maintained separately in a persisted graph data structure so that a user can execute query on those historical changes to understand the lineage of each data item.

Orion **DOES NOT** have the following two blockchain properties:

  - **Smart-Contracts**: A set of functions that manage data on the blockchain ledger. Transactions are invocations of one or more smart contract's functions.
  - **Decentralization of Trust**: A permissioned setup of known but untrusted organizations each operating their own independent database nodes but connected together to form a blockchain network. As one node cannot trust the execution results of another node, ordering transaction must be done with a BFT protocol
  and all transactions need to be independently executed on all nodes.
  
## Documentation 
Please visit our [online documentation](http://labs.hyperledger.org/orion-server/) for information on [getting started](http://labs.hyperledger.org/orion-server/docs/introduction/).

In addition, the [Go SDK repository](https://github.com/hyperledger-labs/orion-sdk-go) contains examples and tutorials.

## REST API Reference

### Common Headers
- `UserID`: User identifier (required for most endpoints)
- `Signature`: Cryptographic signature of the query payload (required for most endpoints)
- `TxTimeout`: Transaction timeout (for transaction submissions)

### User Management (`/user/`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/user/{userid}` | Get user information |
| POST | `/user/tx` | Submit user management transaction |

### Data Operations (`/data/`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/data/{dbname}/{key}` | Get value for a specific key (key in base64 URL encoding) |
| GET | `/data/{dbname}?startkey=&endkey=&limit=` | Get range of key-value pairs |
| POST | `/data/{dbname}/jsonquery` | Execute JSON query on database |
| POST | `/data/tx` | Submit data transaction (create/update/delete) |

### Database Management (`/db/`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/db/{dbname}` | Get database status (check if DB exists) |
| GET | `/db/index/{dbname}` | Get database index definition |
| POST | `/db/tx` | Submit database management transaction |

### Configuration (`/config/`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/config/tx` | Get cluster configuration |
| GET | `/config/cluster` | Get cluster status (leader, active nodes) |
| GET | `/config/node/{nodeId}` | Get single node configuration |
| GET | `/config/block/last` | Get last configuration block |
| POST | `/config/tx` | Submit configuration transaction |

### Ledger (`/ledger/`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/ledger/block/{blockId}` | Get block header by block number |
| GET | `/ledger/block/{blockId}?augmented=true` | Get augmented block header |
| GET | `/ledger/block/last` | Get last block header |
| GET | `/ledger/path?start={start}&end={end}` | Get ledger path between blocks |
| GET | `/ledger/proof/tx/{blockId}?idx={txIdx}` | Get transaction proof |
| GET | `/ledger/proof/data/{dbname}/{key}?block={blockNum}` | Get data existence proof (key in base64) |
| GET | `/ledger/proof/data/{dbname}/{key}?block={blockNum}&deleted=true` | Get data deletion proof |
| GET | `/ledger/tx/receipt/{txId}` | Get transaction receipt |
| GET | `/ledger/tx/content/{blockId}?idx={txIdx}` | Get transaction content |

### Provenance (`/provenance/`)

#### Data Provenance
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/provenance/data/history/{dbname}/{key}` | Get all historical values for a key (key in base64) |
| GET | `/provenance/data/history/{dbname}/{key}?onlydeletes=true` | Get deleted values only |
| GET | `/provenance/data/history/{dbname}/{key}?blocknumber={n}&transactionnumber={m}` | Get value at specific version |
| GET | `/provenance/data/history/{dbname}/{key}?blocknumber={n}&transactionnumber={m}&mostrecent=true` | Get most recent value at or below version |
| GET | `/provenance/data/history/{dbname}/{key}?blocknumber={n}&transactionnumber={m}&direction=previous` | Get previous values from version |
| GET | `/provenance/data/history/{dbname}/{key}?blocknumber={n}&transactionnumber={m}&direction=next` | Get next values from version |
| GET | `/provenance/data/readers/{dbname}/{key}` | Get all users who read a key (key in base64) |
| GET | `/provenance/data/writers/{dbname}/{key}` | Get all users who wrote a key (key in base64) |

#### User Activity Provenance
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/provenance/data/read/{userId}` | Get all data read by a user |
| GET | `/provenance/data/written/{userId}` | Get all data written by a user |
| GET | `/provenance/data/deleted/{userId}` | Get all data deleted by a user |
| GET | `/provenance/data/tx/{userId}` | Get all transaction IDs submitted by a user |

#### Historical State
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/provenance/user/{userId}?blocknumber={n}&transactionnumber={m}` | Get most recent user info at version |
| GET | `/provenance/node/{nodeId}?blocknumber={n}&transactionnumber={m}` | Get most recent node config at version |

### Notes
- **Keys in URLs**: Must be encoded in base64 URL encoding without padding for data operations
- **Authentication**: Most endpoints require `UserID` and `Signature` headers
- **Query Signatures**: Each query type has a specific payload structure that must be signed
- **Transaction Endpoints**: POST endpoints for submitting transactions (`/user/tx`, `/data/tx`, `/db/tx`, `/config/tx`)

## How to Contribute
We will be happy to receive help! In order to become an Orion contributor, please contact the project [maintainers](MAINTAINERS.md).
