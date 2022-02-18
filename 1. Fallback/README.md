# Fallback

Had to get some help to figure out the syntax for everything.

```solidity
  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }
...
  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
```
These 2 methods are the important ones here. We can see contributing a minimum amount of ether will increase your contributions, and if you send a transaction to the contract, *receive()* will get triggered and the owner will be equals to you now.