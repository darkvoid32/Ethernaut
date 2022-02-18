# Telephone

Have to remember that address should be sent as a string not a hexadecimal.

```solidity
function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }
```
*balances[msg.sender] - _value >= 0* has a flaw that you can underflow it. Since balances is a mapping for address to uint we can send 21 tokens to some random address that I pulled online and we have so many tokens now.

I think another way to do it is to just send tokens from an empty account to yourself of Remix IDE.
