# advanced-evm

## EVM Encoding/Decoding

### abi.encode()

Concatenates given arguments and returns a bytes object.
It returns the bytes with padding exactly how it would look like in the contract binary.

### abi.encodePacked()

Concatenates given arguments and returns a bytes object.
Difference with abi.encode: It compresses the returned bytes (no padding).

To return a string, simply wrap this in a string()

Example: 

```
string(abi.encodePacked(stringA, stringB));
```

Alternative for strings: (^0.8.12)

```
string.concat(stringA, stringB);
```

### abi.decode()

ABI-decodes the given bytes into the specified format.

Example:

```
abi.decode(BYTES_TO_DECODE, (string));
```

Example multi decode:

```
(string memory firstString, string memory secondString) = abi.decode(BYTES_TO_DECODE, (string, string));
```

This won't work on ABI packed encoded bytes (abi.encodePacked()), instead we can use string(abi.encodePacked(string)) to decode it.



## call/staticcall

### call: 

call functions that changes the state of the chain.

Example:

```
(bool success, ) = address.call{value: ...}("")
```

in the brackets {} we can pass value, gasLimit, gasPrice.
in the parathenses () we can send the data, in order to call a specific function. 


### staticcall

How at a low level we do our "pure" or "view" function calls. Read data but does not affect the state of the chain.



## EVM signatures and selectors

In order to call a function using only the data field of call, we need to encode:
1) The function name
2) The paramaters we want to add
Down to the binary level

### Function signature

The function signature is a string that defined the function name and parameters

Example

```
transfer(address,uint256)
```

### Function selector

The function selector is the first 4 bytes of the function signature

Example:

```
0xa9059cbb
```

#### To get the data needed for the call:

Step1: To get the function selector we can use:

```
function getSelector() public pure returns(bytes4 selector){
  selector = bytes4(keccak256(bytes("transfer(address,uint256")));
}
```

Step2: To Encode with the arguments:

```
function getDataToCallTransfer(address someAddress, uint256 someAmount) public pure returns(bytes memory) {
  return abi.encodeWithSelector(getSelector(), someAddress, someAmount);
}
```

Alternatively we can directly use abi.encodeWithSignature which combines step1 and step2:

```
function getDataToCallTransfer(address someAddress, uint256 someAmount) public pure returns(bytes memory) {
    return abi.encodeWithSignature("transfer(address,uint256)", someAddress, someAmount);
}
```
