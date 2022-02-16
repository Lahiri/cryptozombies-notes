﻿#  Testing Smart Contracts with Truffle

TBD


## Truffle basics

Install with:

```
npm install -g truffle
```

Folder structure of a Truffle projec:

```
├── build
  ├── contracts
      ├── Migrations.json
      ├── MyContract.json
├── contracts
  ├── Migrations.sol
  ├── MyContract.sol
├── migrations
└── test
. package-lock.json
. truffle-config.js
. truffle.js
```

Every time you compile a smart contract, the Solidity compiler generates a JSON file (referred to as build artifacts) which contains the binary representation of that contract and saves it in the `build/contracts` folder.


## The contract() function

Truffle uses **Mocha** to test your contracts.

The first thing you'll need to do every time you start writing a new test suite is to load the build artifacts of the contract you want to interact with. This way, **Truffle** will know how to format our function calls in a way the contract will understand.

You can perform group tests by calling the *contract()* function, which is an extended version of Mocha's *describe()*, where we can provide a list of accounts to use for the tests.

Example of a simple test:

``` javascript
// Contract abstraction
const myAwesomeContract = artifacts.require(“myAwesomeContract”);

// Test
contract("MyAwesomeContract", (accounts) => {
  it("should be able to receive Ethers", () => {
  })
})
```


## Ganache

With **Ganache** you can setup a local Ethereum test node, that you can use to test your contracts locally.

When you fire up a new local blockchain, Ganache creates 10 test accounts with 100 ETH, to make testing easier.

You can easily access these accounts from **Truffle** with the `accounts` array. To make the code more readable you can give the accounts placeholder names:

``` javascript
let [alice, bob] = accounts;
```


## Test structure

Within the *contract()* function you can define tests with the *it()* function:

``` javascript
const CryptoZombies = artifacts.require("CryptoZombies");

contract("CryptoZombies", (accounts) => {
    let [alice, bob] = accounts;

    it("should be able to create a new zombie", async () => {
      // create a contract instance for this test
      const contractInstance = await CryptoZombies.new();

      // test body
    })
})
```

Tests usually have 3 phases: *set up*, *act* and *assert*.

The *set up* phase consists in declaring the contract abstraction, defining variables and creating a contract instance. You want to create a new contract instance inside each *it()* function.

In the *act* phase we must test the contract calling, for example, one its functions. By doing so we have to define **who** is calling the function.

One of the features of Truffle is that it wraps the original Solidity implementation and lets us specify the address that makes the function call by passing that address as an argument.

This is how we can set *alice* as the `msg.sender` for our test:

``` javascript
const result = await contractInstance.createRandomZombie(zombieNames[0], {from: alice});
```


## Logs and Events

Truffle automatically provides the logs generated by our smart contract.

The `result` variable defined in the previous example includes the logs and many other important information.

``` javascript
// Getting arguments from the function
result.logs[0].args.name

// Transaction hash
result.tx

// Transaction receipt (true/false = success/failure)
result.receipt.status
```

Note that logs are a cheap way to store data, but they can not be accessed from within the smart contract.


## Assert

When the *act* phase is completed we want to assert if the result is what we expect, usually with functions like `equal()` or `deepEqual()`.

These functions throw if the assertion fails.

``` javascript
const CryptoZombies = artifacts.require("CryptoZombies");
const zombieNames = ["Zombie 1", "Zombie 2"];
contract("CryptoZombies", (accounts) => {
    let [alice, bob] = accounts;

    it("should be able to create a new zombie", async () => {
        const contractInstance = await CryptoZombies.new();
        const result = await contractInstance.createRandomZombie(zombieNames[0], {from: alice});

        // Assert statements
        assert.equal(result.receipt.status, true);
        assert.equal(result.logs[0].args.name,zombieNames[0]);
    })
})

```