# Zombies Attack their Victims

TBD


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

