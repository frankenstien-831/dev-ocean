# Preparation for the Ocean Protocol Mainnet Security Audits

Table of Contents
=================



   * [Preparation for the Ocean Protocol Mainnet Security Audits](#preparation-for-the-ocean-protocol-mainnet-security-audits)
   * [Table of Contents](#table-of-contents)
      * [Code security review](#code-security-review)
         * [Goals of the security review](#goals-of-the-security-review)
         * [Network Governance](#network-governance)
            * [Governance Framework](#governance-framework)
            * [Authorities/Network governance](#authoritiesnetwork-governance)
         * [Technical Components](#technical-components)
            * [Keeper Contracts](#keeper-contracts)
            * [MultiSig Wallet](#multisig-wallet)
               * [Deployment](#deployment)
            * [Token Bridge](#token-bridge)
               * [Bridge Contracts](#bridge-contracts)
               * [Token Bridge](#token-bridge-1)
            * [Deployment](#deployment-1)
            * [Secret Store](#secret-store)
      * [Penetration Test](#penetration-test)
         * [Aquarius](#aquarius)
         * [Brizo](#brizo)


The security audit prior to the network launch require the following steps:

* Code review of different technical components
* Penetration tests of the main micro-services

## Code security review

### Goals of the security review

We need to have information about:
The main objective of the different security audits is to obtain information about:

* Is the Ocean Token secure?
* Are the user transactions facilitated using the Service Execution Agreements secure?
* Is the governance model for deployment, upgrades and roles inside the contracts secure?
* The Governance of the authorities building the network is on-chain. Are the Smart Contracts used to govern the network secure?
* Can we move tokens between the Security ERC20 token (Ethereum mainnet) and the Utility ERC20 token (POA Network) using the token bridge securely?
* What's the overall level of security?
* Are we okay for the network launch?


### Network Governance

In the scope of the Ocean Protocol network we need to validate the governance approach including the network (authorities) and the software upgrades.

Here you can find the complete [Ocean Protocol Governance framework](https://github.com/oceanprotocol/governance).

#### Governance Framework

```
Url: https://github.com/oceanprotocol/governance-contracts/
Topic: POA Network Governance
Version: 0.x
Branch: develop
Already audited?: no
Need to be Audited?: yes, top priority
```


#### Authorities/Network governance

Ocean Protocol production environment network is based in Proof of Authority (POA). 
What authorities belong to the network and how are added/removed is managed on-chain using Smart Contracts.

Current Options:

* [poa-network-consensus-contracts](https://github.com/poanetwork/poa-network-consensus-contracts), already [audited](https://github.com/poanetwork/poa-network-consensus-contracts/tree/master/audit)
* [Validator Sets](https://wiki.parity.io/Validator-Set) + [PeerManager](https://wiki.parity.io/Permissioning#how-it-works)

It's pending to define the requirements and the implementation on-chain of the governance.


### Technical Components

In order to release the Ocean Protocol network, we need to validate different components (internally or externally implemented) in different ways.
The list of projects to evaluate is:

#### Keeper Contracts

```
Url: https://github.com/oceanprotocol/keeper-contracts/
Topic: Solidity Smart Contracts
Version: 0.9.x
Branch: develop
Already audited?: yes, by Trail of Bits (Feb/2019)
Need to be Audited?: yes, top priority
```

It provides the core business logic of Ocean Protocol including the following modules:

* Service Execution Agreements (SEA) - Generic framework to facilitate data services
* Ocean Token - Ocean Protocol ERC20 Token Contract
* Dispenser - Ocean Tokens Dispenser
* DIDRegistry - Registration of Decentralized Identifiers (W3C DID)

In addition to this, the project provides the tools to deploy/upgrade the Smart Contracts in the different Ocean Protocol networks.
The documentation about the [Release Process](https://github.com/oceanprotocol/keeper-contracts/blob/develop/RELEASE_PROCESS.md) 
and how to [upgrade the Smart Contracts](https://github.com/oceanprotocol/keeper-contracts/blob/develop/doc/upgrades.md) can be found in the repository.

High-Level Architecture:
* [Service Execution Agreements](https://blog.oceanprotocol.com/exploring-the-sea-service-execution-agreements-65f7523d85e2)
* [Secure On-Chain Access Control for Ocean Protocol](https://blog.oceanprotocol.com/secure-on-chain-access-control-for-ocean-protocol-38dca0af820c)
* [OEP11: On-Chain Access Control using Service Agreements](https://github.com/oceanprotocol/OEPs/tree/master/11)
* [OEP7: Decentralized Identifiers](https://github.com/oceanprotocol/OEPs/tree/master/7)


Some information about SEA:

* [Modular Service Execution Agreements](https://github.com/oceanprotocol/OEPs/issues/125)
* [Service Conditions](https://github.com/oceanprotocol/OEPs/issues/119)
* [HashLock Condition](https://github.com/oceanprotocol/OEPs/issues/120)
* [Sign Condition](https://github.com/oceanprotocol/OEPs/issues/121)
* [LockReward Condition](https://github.com/oceanprotocol/OEPs/issues/122)
* [SEA Template Store](https://github.com/oceanprotocol/OEPs/issues/132)


Automated reports generated by Travis (when merging to `develop` branch) can be found in the [Keeper Contracts Security Reports repo](https://github.com/oceanprotocol/keeper-contracts-security-reports).

Additional Details:

* Travis is used as Continuous Integration pipeline: https://travis-ci.com/oceanprotocol/keeper-contracts
* Security reports (mythril + securify) are generated automatically when merging to `develop` branch. They will be stored [here](https://github.com/oceanprotocol/keeper-contracts-security-reports)
* Project integrates ZeppelinOS 2.2 for upgradability
* Roles are managed using standard openzeppelin-eth project libraries
* All contracts are Ownable to transfer ownership and leverage the `onlyOwner` modifier.
* Project is using solidity v0.5.6

#### MultiSig Wallet

```
Url: https://github.com/oceanprotocol/MultiSigWallet
Forked from: https://github.com/gnosis/MultiSigWallet
Topic: MultiSigWallet 
Branch: master
Already audited?: yes, by Trail of Bits (Feb/2019)
Need to be Audited?: yes, Nope
```
Cleaned and modularized for of the Gnosis MultiSigWallet v1.4.0

Can be used in conjunction with: https://wallet.gnosis.pm

##### Deployment

Two instances of the MultiSigWallet are required. They are set up automatically by the deploment script. They can be used by importing the addresses in https://wallet.gnosis.pm

To deploy the contracts use:

`npm run deploy` to deploy all the contracts.
`npm run deploy OceanToken` to only deploy the OceanToken contract.

This will deploy the contracts to a node located at localhost:8545. Most likely `ganache-cli`.

To upgrade a contract use:

`npm run upgrade OceanToken2:OceanToken` to swap out the implementation of `OceanToken` with the implementation of the `OceanToken2` contract.

This will issue an `upgrade request` against the MultiSigWallet that has to be confirmed by the owners of the wallet. Once this is done the implementations are swapped out.

#### Token Bridge

Token Bridge is used to transfer ERC20 tokens between Ocean POA network and Ethereum mainnet. It is based on token bridge developed by POA.network.

We are using the following components forked (no modifications made) from the POA.network repositories:

This components [was already audited](https://github.com/poanetwork/token-bridge/blob/master/audit/peppersec/POA-Network-Token-bridge-security-assessment-report.pdf) by PepperSec, 
it's not necessary to audit again.


##### Bridge Contracts
```
Url: https://github.com/oceanprotocol/poa-bridge-contracts
Forked from: https://github.com/poanetwork/poa-bridge-contracts
Topic: Solidity Smart Contracts
Branch: master
Already audited?: yes
Need to be Audited?: nope
```

##### Token Bridge
```
Url: https://github.com/oceanprotocol/token-bridge
Forked from: https://github.com/poanetwork/token-bridge
Topic: NodeJS Bridge Oracle
Branch: master
```

The initial investigation & Proof of Concept can be found in the following repository: https://github.com/oceanprotocol/poc-token-bridge

#### Deployment

Instructions can be found on: https://github.com/oceanprotocol/dev-ocean/tree/master/doc/architecture/token-bridge#how-to-deploy


#### Secret Store
```
Url: https://github.com/oceanprotocol/parity-ethereum
Forked from: https://github.com/paritytech/parity-ethereum
Topic: 
Branch: master
Already audited?: no
Need to be Audited?: nice to have
```

Parity Secret Store is a feature included as part of the Parity Ethereum client that allows users to store a fragmented ECDSA key on the blockchain, such that retrievals are controlled by a permissioned Smart Contract.

The Secret Store implements a threshold retrieval system, so individual Secret Store nodes are unable to reconstruct the keys to decrypt documents by themselves. A Secret Store node only saves a portion of the ECDSA key. The decryption key can only be generated if a consensus is reached by an amount of Secret Store nodes bigger than the threshold that the publisher of the secret chooses.

We use the Parity Secret Store for encrypting URLs to data. Those URLs only can be decrypted by the consumer when the SEA conditions are fulfilled (typically after payment). In the decryption flow, the Secret Store call the `checkPermissions` function of the AccessConditions contract to check if a specific user (consumer `address`) is entitled to decrypt the URLs of a specific Asset (`did`). 


https://github.com/oceanprotocol/dev-ocean/blob/master/doc/architecture/secret-store.md


## Penetration Test

A black box penetration test of the Ocean Protocol micro-services is necessary to assess its risk posture and identify security 
issues that could negatively affect Ocean Protocol's data, systems, or reputation. 

The scope of the assessment must cover Aquarius and Brizo API. 


### Aquarius

```
Url: https://github.com/oceanprotocol/aquarius/
Topic: Metadata API micro-service
Version: 0.2.x
Branch: develop
```

More information about hte API can be found [here](https://docs.oceanprotocol.com/references/aquarius/)


### Brizo

```
Url: https://github.com/oceanprotocol/brizo/
Topic: Publishers Gateway micro-service
Version: 0.3.x
Branch: develop
```

More information about hte API can be found [here](https://docs.oceanprotocol.com/references/brizo/)




