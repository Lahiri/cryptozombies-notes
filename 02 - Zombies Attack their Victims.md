# Zombies Attack their Victims
1. [Mapping addresses](#mapping-addresses)
2. [Global variable: msg.sender](#global-variable-msgsender)
3. [Require](#require)
4. [Inheritance](#inheritance)
5. [Import](#import)
6. [Storage and Memory](#storage-and-memory)
7. [Interacting with other contracts](#interacting-with-other-contracts)
8. [Return values](#return-values)
9. [If statement](#if-statement)
10. [Javascript implementation](#javascript-implementation)


## Mapping addresses

Addresses are declared with the *address* keyword:

```
address
```

In smart contracts it is common to map addresses and their assets, like tokens or NFTs.
To do this in Solidity we can use the *mappings*:

```
mapping (address => uint) public accountBalance;
```


## Global variable: msg.sender

In Solidity, there are certain global variables that are available to all functions.
One that is particularly important is `msg.sender`, which refers to the address that called the current function.

```
mapping (address => uint) favoriteNumber;

function setMyNumber(uint _myNumber) public {
  favoriteNumber[msg.sender] = _myNumber;
}
```


## Require

*Require* is used to add conditions to the execution of a function, that will throw an error if the condition is not met.

```
function createRandomZombie(string memory _name) public {
  require(ownerZombieCount[msg.sender] == 0);
  uint randDna = _generateRandomDna(_name);
  _createZombie(_name, randDna);
}
```


## Inheritance

Often it is more convenient to split the code in multiple contracts to better organize and reuse them.
Solidity deals with this with inheritance.

```
contract Doge {
  function catchphrase() public returns (string memory) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string memory) {
    return "Such Moon BabyDoge";
  }
}
```

In this example. the *BabyDoge* contract inherits from *Doge*, so it has access to the *catchphrase()* function, in addition to the functions definited in *BabyDoge* itself.


## Import

Code can also be split in multiple files by using the *import* keyword.
Importing a file will add make all the contracts in there available.

```
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```


## Storage and Memory

**Storage** refers to variables stored permanently on the blockchain.

**Memory** variables are temporary, and are erased between external function calls to your contract.

State variables (variables declared outside of functions) are by default storage and written permanently to the blockchain, while variables declared inside functions are memory and will disappear when the function call ends.

When dealing with **structs** and **arrays** it may be necessary to explicitly use **storage** or **memory** keywords.

```
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }

  Sandwich[] sandwiches;

  function eatSandwich(uint _index) public {
    Sandwich storage mySandwich = sandwiches[_index];
    mySandwich.status = "Eaten!";
    // ...this will permanently change `sandwiches[_index]` on the blockchain.

    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    anotherSandwich.status = "Eaten!";
    // ...will just modify the temporary variable and have no effect
    // on `sandwiches[_index + 1]`. But you can do this:
    sandwiches[_index + 1] = anotherSandwich;
    // ...if you want to copy the changes back into blockchain storage.
  }
}
```


## Interacting with other contracts

You can read or interact with other smart contracts stored in the blockchain.

First you need to define an **interface**, that contains the functions from the external contract that we wanto to interact with.

Example of external contract:

```
contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
```

Our interface:

```
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

In this case we only need to use the *getNum* function, so we don't have to define the other one in the interface.

Note that interfaces functions **do not** have a body, not even the curly braces.

The interface can be used instantiated in our contract:

```
contract MyContract {
  address NumberInterfaceAddress = 0xab38...
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);

  function myFunction() public {
    uint num = numberContract.getNum(msg.sender);
  }
}
```

The value of *NumberInterfaceAddress* is the address on the blockchain of the smart contract we are referencing.

*numberContract* is an instance of that contract that exposes the functions included in our interface.


## Return values

Functions can return more than one value.

```
function multipleOutputs() internal returns(uint a, uint b, uint c) {
  return (1, 2, 3);
}
```

This is how you do multiple assignments:

```
function receiveMultipleOutputs() external {
  uint a;
  uint b;
  uint c;

  (a, b, c) = multipleOutputs();
}
```

If you only want one of the returned values you can leave the other outputs unassigned:

```
function receiveOnlyOneOutput() external {
  uint c'

  (,,c) = multipleOutputs();
}
```


## If statement

```
function helloWorld(string memory message) public {
  if(message == "Hello world"){
    return message
  }
}
```

Solidity has the following operators:

Operator | Denotation
---------|-----------
equals   | ==
not equals | !=
greater than | >
greater or equal | >=
less or equal | <=


## Javascript implementation

You can interact with the contracts using web3.js.
You need to import the abi code generated by the compiler to interact with the contract methods.

```
var abi = /* abi generated by the compiler */
var MySmartContract = web3.eth.contract(abi)
var contractAddress = /* our contract address on Ethereum after deploying */
var MyContractInstance = ZombieFeedingContract.at(contractAddress)

let message = "Hello world";

$(".myImage").click(function(e) {
  // Call our contract's `feedOnKitty` method
  MyContractInstance.myFunction(message)
})
```