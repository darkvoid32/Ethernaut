# Privacy

```solidity
contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(now);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) public {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }
  ...
}
```
Using what I learnt from vault : 

> await web3.eth.getStorageAt("0x387C8BF8371db968A25cA6Cc2965D4aB0C9d940d", 5)

> 0x75f7a66e5804238b6d1967e75cba073c25dd5c3ea3864d199bfb6bac6c3225c3

That hexadecimal should be the *bytes32[3] data* (I think). Trying the next index gives an empty hexadecimal so this should be right.

Next let's look at the *unlock* function. It takes a 16 byte key and matches it to the 3rd element of the data array, casted to 16 bytes.

*data* is array of size 3 -> take 3rd(last) element of *data* -> typecast it to *bytes16*

## I was wrong

Nvm I was wrong. After reading up on the way storage is handled in smart contracts : The smart contract tries to pack everything into as little slots as possible. A slot here, refers to a 32 byte storage.

```solidity
bool public locked = true;
uint256 public ID = block.timestamp;
uint8 private flattening = 10;
uint8 private denomination = 255;
uint16 private awkwardness = uint16(now);
bytes32[3] private data;
```

*locked* -> 1 byte -> 1/32 1st slot;

*ID* -> 1 slot (256 bits -> 32 bytes) -> 32/32 2nd slot;

*flattening* -> 1 byte -> 1/32 3rd slot;

*denomination* -> 1 byte -> 2/32 3rd slot;

*awkwardness* -> 2 byte -> 4/32 3rd slot;

*data* -> 3 slots -> 32/32 4 & 5 & 6 slot;

I was trying to decode the hexadecimal I got above since I thought the whole thing was the bytes32 array but its just the 3rd element of the array already.

typecasting it to 16 bytes:

> 0x75f7a66e5804238b6d1967e75cba073c25dd5c3ea3864d199bfb6bac6c3225c3 -> 0x75f7a66e5804238b6d1967e75cba073c