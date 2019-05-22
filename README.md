# Peg Zone Smart Contracts

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![Gitter](https://badges.gitter.im/WingsChat/community.svg)](https://gitter.im/WingsChat/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

**WINGS peg zone** smart contracts implementation, based on PoA (Proof of Authority) government model, supports **ETH and any ERC20 approved token**.

Allowing to move ETH/ERC20 tokens between Ethereum
blockchain and **WINGS blockchain**.

Right now security is reached by PoA validators, that inspected by community, later it should be replaced with Proof of Stake (PoS) algorithm to
reach more decentralization.

## Features

This is work in progress, but still general functional already implemented, like:

* **N/2+1** validators needed to reach consensus
* Allowing to remove/add validators by consensus
* Validator takes fees for their support
* Validator can propose to list any [ERC20 token](https://theethereum.wiki/w/index.php/ERC20_Token_Standard)
* Support minimum exchange amount and capacity of any ETH/ERC20 token
* Allowing to lock ETH/ERC20 tokens
* Allowing to withdraw ETH/ERC20 tokens
* Basic migration implemented
* Basic pause/resume function implemented for safe migration

## Motivation

Main motivation to use multiplay native currencies from
different blockchains inside **WINGS** blockchain, that will provide more liquidity for financial derivatives and financial operations in nutshell, and be not limited by one ecosystem. This idea opens new doors for
many of many usages we can bring to blockchain technology, such as defi, swaps, futures, options.

During our development we are going to make a same
solutions for popular blockchains, not only for Ethereum, like: **Bitcoin, EOS, Tron, Lisk, etc**.

## Structure

This repository contains only smart contract part, it doesnt include relay node part, and Cosmos part (module for Cosmos SDK), described initiatives will be announced later.

For easy explanation let's make a small glossary:
* `Currency`  - Any ETH/ERC20 token
* `Validator` - Validator account involved in PoA consensus
* `Peggy`     - Peg zone allowing to move tokens between chains
* `Consensus` - When any action requires validators N/2+1 approve, where N is total amount of PoA validators
* `WETH`      - Example currency, 1:1 to ETH, but exists on Wings blockchain

So let's look at **ETH to WETH UML** as example

![Wings to WETH UML](/res/eth_wei_flow.png?raw=true "Wings to WETH UML")

WETH to ETH conversion going to work in same way, but lock will start at Wings blockchain, and withdraw will happen at Ethereum blockchain.

Current repository contains smart contracts part like:

* [PoAGovernment.sol](/contracts/PoAGovernment.sol) - Implements validators logic and PoA consensus logic on actions during peg zone life cycle
* [Bridge.sol](/contracts/Bridge.sol)      - Implements bridge for lock/withdraw Ethereum and any listed ERC20 token, when user want to move his ETH/tokens to Wings blockchain
* [BankStorage.sol](/contracts/BankStorage.sol)  - Keeps ETH/tokens and split fees between
validators

## PoA multisignature logic

In PoA implementations we have list of validators, maximum is
11 of them, minimum is 3. Each sensitive call
to Bridge done under validation consensus, means any validator
can initiate a transaction to Bridge contract or to itself contract (in case needs to add new validator, remove it, etc),
then once transaction reaches N/2+1 confirmations from the rest of validators, transaction could be executed.

## Fees

Fees splits between all active validators, and if value can't be divided without reminder smart contract will save reminder for next exchange.

To get accrued fees validator should make a call to BankStorage contract with amount and address of currency he wants to withdraw.

## Installation

Requirements:

    * Node.js
    * [Truffle Framework](https://truffleframework.com)

Installation:

```
yarn install
```

After execution of described commands it should be possible to launch tests,
migrations.

## Migrations

There is few migrations scripts:

* [BankStorageFactory](/migrations/1_bank_storage_factory.js) - Migrations for [BankStorage](/contracts/BankStorage.sol) contract, based on factory
* [PoAFactory migration](/migrations/2_poa_factory.js) - Migrations for [PoAGovernment](/contracts/PoAGovernment.sol) contract, same here based on factory
* [BridgeFactory migration](/migrations/3_bridge_factory.js) - Migration for [Bridge](/contracts/Bridge.sol) contract, based on factory
* [Bridge creation](/migrations/4_new_bridge.js) - Creating new [Bridge](/contracts/Bridge.sol) / [PoA](/contracts/PoAGovernment.sol) / [BankStorage](/contracts/BankStorage.sol) instances, connect them (ownership, etc), based on previously created factories

To launch migration we have to provide correct environment variables.

To deploy [BankStorageFactory](/contracts/factories/BankStorageFactory.sol):

```
CONTRACT=BankStorage ACCOUNT=0x5195.... truffle migrate
```

Where `ACCOUNT` is deployer account address, so replace value with your own.

To deploy [PoAGovernmentFactory](/contracts/factories/PoAGovernment.sol):

```
CONTRACT=PoA ACCOUNT=0x5195.... truffle migrate
```

To deploy [BridgeFactory](/contract/factories/Bridge.sol):

```
CONTRACT=Bridge ACCOUNT=0x5195.... BANK_STORAGE_FACTORY=0x4579... POA_FACTORY=0xc6C11... truffle migrate
```

Where both `BANK_STORAGE_FACTORY` and `POA_FACTORY` values (addresses) could be copied from previous two commands outputs (where we deploy [BankStorageFactory](/contracts/factories/BankStorageFactory.sol) and [PoAGovernmentFactory](/contracts/factories/PoAGovernment.sol)).

To deploy new [Bridge](/contracts/) instance:

```
CONTRACT=NEW_BRIDGE BRIDGE_FACTORY=0x4579... VALIDATORS=0x4579...,0x2f39... ETH_CAPACITY=1000    ETH_MIN_EXCHANGE=1000000000000000000000 ETH_FEE_PERCENTAGE=10 truffle migrate
```

Where:

* `BRIDGE_FACTORY` - Could be copied from previous command (where we deploy [BridgeFactory](/contracts/factories/BridgeFactory.sol))
* `VALIDATORS` - Comma seperated string contains initial validators addresses
* `ETH_CAPACITY` - Maximum capacity for ETH exchange contract
* `ETH_MIN_EXCHANGE` - Minimum ETH amount to exchange
* `ETH_FEE_PERCENTAGE` - Fee percent that validator takes for their work for ETH exchange, minimum is 1, maximum is 9999 (normalized percent value, e.g. 100 is 1%, 1 is 0.01%, 9999 is 99.99%)

It's all, to see how to work with deployed contracts visit our (documentation)(/#docs).

## Tests

    TODO

## Docs

    TODO

## Contribution

Current project is still under development and going to evolve together with other parts of Wings blockchain as
**Relay Layer** and Wings blockchain itself, anyway we have
planned things to:

* More tests coverage
* Allow to stop withdraw/deposit of specific currency
* Allow to do migration without reference on previous contract version
* First refactoring
* PoS government implementation instead of PoA

You are ready to contribute, but please, try to follow
solidity [style guide](https://solidity.readthedocs.io/en/v0.5.3/style-guide.html) and leave comments on new functional.

In case of modification our Javascript code ([migrations](/migrations) and [tests](/test)) follow our [eslint](/.eslintrc) configuration.

This project has the [following contributors](https://github.com/WingsDao/griffin-consensus-poc/graphs/contributors).

## License

Copyright © 2019 Wings Foundation

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the [GNU General Public License](https://github.com/WingsDAO/griffin-consensus-poc/tree/master/LICENSE) along with this program.  If not, see <http://www.gnu.org/licenses/>.
