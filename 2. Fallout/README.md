# Fallout

Got spoiled from watching the youtube walkthrough for this

```solidity
   /* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }
```

Constructors don't have a function name and shouldn't have the function call (I think?) since it should have its own constructor call.

Calling *contract.abi* also reveals the public method for this and calling it will give us ownership.