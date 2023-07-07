# Open Charging Network

Open Charging Network (OCN) is an open and decentralized network for electric vehicle charging and e-mobility services. It uses OCPI protocol, Energy Web Chain (EWC) and OCN Registry to enable secure and authorized peer-to-peer communication between different parties on the network.

## Evaluate OCN and its components

The main components of the OCN are as follows:

- **OCN Node:** The OCN node is a software module that setup a communication layer between OCPI protocol and the OCN Registry. It is based on the OCPI 2.2 and supports various modules such as credentials, tokens, locations, tariffs, sessions, cdrs, and commands.

- **OCN Registry:** OCN Registry is a smart contract deployed on the Energy Web Chain that stores and verifies the OCN Node URLs and public keys of the participants.

- **OCN Tools:** This is a set of tools for testing, monitoring and debugging OCN Nodes and OCPI connections.

- **OCN Bridge:** The OCN bridge enables charging point operators (CPOs) and other open charge point interface (OCPI) parties to connect their existing systems with the OCN protocol.

- **OCN CLI:** This is a command-line interface for interacting with the OCN Registry and managing OCN Nodes.

## How OCN can be used to register users and assets

The diagram shows the steps involved in setting up and using an OCN node.

```mermaid
sequenceDiagram
participant User
participant OCN Node
participant Backoffice System
participant OCN Bridge
participant OCN Registry
participant OCN CLI
participant OCN Tools
participant Other Nodes

User->>OCN Node: Set up OCN Node with role and modules
OCN Node->>Backoffice System: Connect to backoffice system via API
Backoffice System->>OCN Bridge: Use OCN Bridge to implement OCPI interface
OCN Bridge->>OCN Node: Proxy OCPI requests and responses
User->>OCN CLI: Generate registration token for OCN Node
OCN CLI->>OCN Registry: Add node URL and public key to registry
User->>OCN CLI: Initiate OCPI credentials handshake with other nodes
OCN CLI->>Other Nodes: Send and receive tokens and endpoints
User->>OCN Node: Use OCPI modules and interfaces to communicate with other nodes
OCN Node->>Other Nodes: Send and receive OCPI messages
User->>OCN Tools: Test, monitor and debug node and OCPI connections
```

## Identify the benefits and challenges of using OCN

Some of the benefits are:

- Full control over identity and data on the network
- Enhances privacy and security on the network
- Reduced costs and complexity of e-roaming and interoperability

Some of the challenges are:

- Management of Ethereum accounts and private keys
- Funding of Ethereum accounts with Ether
- Latency and uncertainty on the blockchain transactions

**Dependencies:**

- A Java Runtime Environment (JRE at least version 8) to run the OCN node and the OCN tools
- A backoffice system that supports OCPI protocol or a pluggable API to connect to the OCN Bridge
- A funded Ethereum account to register on the OCN Registry and sign OCN Messages
- A stable connection to communicate between OCN node, the OCN Registry and other nodes on the network

**TODO**

The communication part is still not fully explored. For example sending details about:

- Sending location updates
- Receive session updates from other nodes
- Send tariff updates to other nodes
- Receive CDR updates from other nodes

# 🚀 How to run the OCN node locally

This guide will show you how to run the OCN node locally using the command line. You will need to have Java Runtime Environment (at least version 8), Node.js, and curl installed on your machine.

## 📌 Prerequisites

Before you start, you will need to have the following:

- The OCN node jar file (ocn-node-1.1.2.jar) downloaded from the [OCN GitHub repository](https://github.com/energywebfoundation/ocn-node/releases/tag/v1.1.2).
- The OCN registry CLI tool (ocn-registry) installed globally using `npm install -g ocn-registry`.
- A Volta wallet address and private key for signing transactions on the Volta network. You can use [MetaMask](https://metamask.io/) or any other Ethereum-compatible wallet to create one. You can also use [Vanit-ETH](https://vanity-eth.tk/) to create Ethereum vanity address.
- After downloading, extract the contents of the archive and change directory:

```bash
tar zxvf ocn-node-1.1.2.tar.gz
cd ocn-node-1.1-2
```

## 🛠️ Running the node

To run the node, you will need to create a configuration file named `application.local.properties` in the same folder as the jar file. This file should contain the following properties:

```properties
# spring JPA
spring.jpa.open-in-view = false

# http request handling
server.error.include-stacktrace = never

# logging
logging.level.web = DEBUG

# OCN Node
ocn.node.dev = true
ocn.node.signatures = false
ocn.node.url = http://localhost:8080
ocn.node.privateKey = <Replace with your Ethereum address private key>
ocn.node.apikey = randomkey
ocn.node.web3.provider = https://volta-rpc.energyweb.org/
ocn.node.web3.contracts.registry = 0xd57595D5FA1F94725C426739C449b15D92758D55
ocn.node.web3.contracts.permissions = 0x520896B666fCcDb6458D4eC5C1FdD0D6d9EB97A3
```

You can also modify other properties according to your preferences. For more details, see the [OCN node documentation](https://bitbucket.org/shareandcharge/ocn-node/src/396e4b54541981a5c9b8c0b5a56db2eff50a27ab/CONFIGURATION.md).

Once you have created the configuration file, you can run the node using the following command:

```bash
java -jar -Dspring.config.location=application.local.properties ocn-node-1.2.0-rc1.jar
```

This will start the node on port 8080 with HTTPS enabled. You should see some logs indicating that the node is running.

## 📝 Listing the node in the OCN registry

To make your node visible to other parties on the OCN, you will need to list it in the OCN registry. The OCN registry is a smart contract on the Volta network that stores the URLs and public keys of all nodes on the network.

To list your node in the OCN registry, you will need to use the ocn-registry CLI tool. You can use the following command:

```bash
npx ocn-registry set-node https://localhost:8080 -n volta -s <REPLACE WITH YOUR ETHEREUM ADDRESS PRIVATE KEY>
```

This will send a transaction to the OCN registry contract with your node URL and public key. The `-n volta` option specifies that you are using the Volta network, and the `-s` option provides your private key for signing the transaction.

You should see a confirmation message with the transaction hash and a link to view it on [Volta Explorer](https://volta-explorer.energyweb.org/).

## ⚙️ Setting OCN party

To identify yourself as a party on the OCN, you will need to set your OCPI credentials and roles in the OCN registry. This will allow other parties to discover and communicate with you using OCPI.

To set your OCN party, you will need to use the ocn-registry CLI tool again. You can use the following command:

```bash
npx ocn-registry set-party --credentials DE OLI --roles EMSP --operator <REPLACE WITH YOUR ETHEREUM ADDRESS> --signer <REPLACE WITH YOUR ETHEREUM ADDRESS PRIVATE KEY> -n volta
```

This will send another transaction to the OCN registry contract with your OCPI credentials and roles. The `--credentials` option provides your country code and party ID, which should match your OCPI credentials object. The `--roles` option specifies which roles you are playing on the network, such as EMSP or CPO. The `--operator` option provides your Volta wallet address, which should match the one you used to run the node. The `--signer` option provides your private key for signing the transaction. The `-n volta` option specifies that you are using the Volta network.

You should see another confirmation message with the transaction hash and a link to view it on Volta Explorer.

## 🔒 Requesting a Token_A

You can request a registration token using the following command:

```bash
curl -XPOST http://localhost:8080/admin/generate-registration-token -H 'Authorization: Token randomkey' -H 'Content-Type: application/json' -d '[{"country_code": "DE", "party_id": "OLI"}]' | jq .
```

You should see a response like this:

```json
{
  "token": "dfcba4c4-ad4f-4f7e-aa57-698d168cdf37",
  "versions": "http://localhost:8080/ocpi/versions"
}
```

You can also request your node's version details using the following command:

```bash
curl http://localhost:8080/ocpi/versions -H 'Authorization: Token dfcba4c4-ad4f-4f7e-aa57-698d168cdf37'
```

Congratulations, you have successfully run the OCN node locally! 🎉
