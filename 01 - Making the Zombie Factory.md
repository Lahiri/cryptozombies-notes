# Making the Zombie Factory
1. [Pragma](#pragma)
2. [State variables](#state-variables)
3. [Math operations](#math-operations)
4. [Structs](#structs)
5. [Arrays](#arrays)
6. [Functions](#functions)
7. [Storing data](#storing-data)
8. [Modifiers](#modifiers)
9. [Pseudo-random generation](#pseudo-random-generation)
10. [Typecasting](#typecasting)
11. [Events](#events)


## Pragma

First, we must always declare the Solidity version:

`pragma solidity >=0.6.0 <0.8.11`

Then we declare a contract. Contracts are the core of Solidity.

``` javascript
contract ZombieFactory {
    // contract logic
}
```


## State variables

**State variables** are permanently stored in contract storage. This means they're written to the Ethereum blockchain. Think of them like writing to a DB.

A uint is an unsigned integer, so it can not be negative. The variable can be inititate with or without a value.

``` javascript
contract ZombieFactory {
    uint dnaDigits = 16;
}
```


## Math operations

- *Addition*: x + y
- *Subtraction*: x - y,
- *Multiplication*: x * y
- *Division*: x / y
- *Modulus*: x % y
- *Power*: x ** y


## Structs

**Structs** are data types with multiple properties.

``` javascript
struct Zombie {
    string name;
    uint dna;
}
```

Usually Solidity always uses 256 bits for *uint*s, so it is useless to specify smaller sizes, such as *uint8* or *uint16* (remember that *uint* is an alias for *uint256*).

However, when using *uint*s in structs, it is important to specify the bit size because Solidity can pack the variables together and save storage.

For this reason, inside a struct you'll want to use the smallest integer sub-types you can get away with.

``` javascript
struct NormalStruct {
  uint a;
  uint b;
  uint c;
}

struct MiniMe {
  uint32 a;
  uint32 b;
  uint c;
}
```

## Arrays

**Arrays** can be *fixed* or *dynamic*:

``` javascript
uint[2] fixedArray;

string[5] stringArray;

uint[] dynamicArray;
```

This is how you create an array of structs and add itemps with the *push* method:

``` javascript
contract ZombieFactory {

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function _createZombie(string memory _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
}
```


## Functions

**Functions** can take parameters that by convention start with _ (underscore).

``` javascript
function createZombie(string memory _name, uint _dna) public {

}
```

Functions and state variables can have different visibility:
- *public*: can be called or viewed internally or externally.
- *private*: are only available to the contract where they are created.
- *internal*: are only available to the contract where they are created or from derived contracts.
- *external*: can be called from other contracts or via transaction, but not internally. External functions are sometimes more efficient when they receive large arrays of data.

By convention, private functions start with _ (underscore).

Functions can return values and the function declaration must contain the type of it.

``` javascript
string message = "Hello world";

function printMessage() public returns(string memory) {
    return message;
}
```

Variables can be declared as *public* and a **getter** method is automatically created.

``` javascript
Zombie[] public zombies;
```


## Storing data

The Ethereum Virtual Machine has three areas where it can store items:
- **Storage**: where all the contract state variables reside. Every contract has its own storage and it is persistent between function calls and quite expensive to use.
- **Memory**: is used to hold temporary values. It is erased between (external) function calls and is cheaper to use.
- **Stack**: is used to hold small local variables. It is almost free to use, but can only hold a limited amount of values.

For almost all types, you cannot specify where they should be stored, because they are copied everytime they are used.

State variables are always in storage.

Local variables of struct, array or mapping type reference storage by default.

Local variables of value type (i.e. neither array, nor struct nor mapping) are stored in the stack

What are inefficient practices in other programming languages can be the best practice in Solidity. For example, it may be more efficient to rebuild an array in memory every time a function is called rather than store the array in memory, because we save the storage cost.

This is how you declare an array in memory"

``` javascript
function getArray() external pure returns(uint[] memory) {
  // Instantiate a new array in memory with a length of 3
  uint[] memory values = new uint[](3);

  // Put some values to it
  values[0] = 1;
  values[1] = 2;
  values[2] = 3;

  return values;
}
```

The array will exist only during the function execution.

Note that memory arrays can only be created with a length argument and they cannot be resized like storage arrays can with `array.push()`.


## Modifiers

- *view*: used when the function only reads data from the blockchain. It is cheaper than functions that make changes to the storage.
- *pure*: used when the function does not read or modify any storage data. It is even cheaper than view.

``` javascript
    function _generateRandomDna(string memory _str) private view returns (uint) {

    }

```

You can define custom modifiers to easily add features to your functions.
A very common modifier is onlyOwner():

``` javascript
function isOwner() public view returns(bool) {
    return msg.sender == _owner;
}

modifier onlyOwner() {
    require(isOwner());
    _;
}
```

This makes the modified functions callable only by the contract owner:

``` javascript
function myFunction() public onlyOwner {
    // function logic
}
```

Modifiers can also take arguments.

``` javascript
// A mapping to store a user's age:
mapping (uint => uint) public age;

// Modifier that requires this user to be older than a certain age:
modifier olderThan(uint _age, uint _userId) {
  require(age[_userId] >= _age);
  _;
}

// Must be older than 16 to drive a car (in the US, at least).
// We can call the `olderThan` modifier with arguments like so:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // Some function logic
}
```

## Pseudo-random generation

The *kekkak256* function is a hash function that can be used to create pseudo-random 256-bit hexadecimal numbers.

```
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256(abi.encodePacked("aaaab"));

//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256(abi.encodePacked("aaaac"));
```


## Typecasting

**Typecasting** is used when you want to convert between data types:

``` javascript
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:
uint8 c = a * b;
// we have to typecast b as a uint8 to make it work:
uint8 c = a * uint8(b);
```

*uint* is an alias for *uint256*.


## Events

**Events** are a way for your contract to communicate that something happened on the blockchain to your app front-end, for example using web3.js.

Events are declared with the *event* keyword and fired with the *emit* keyword.

``` javascript
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  emit IntegersAdded(_x, _y, result);
  return result;
}
```
