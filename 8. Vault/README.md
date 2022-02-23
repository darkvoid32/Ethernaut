# Vault

Spent a super long time trying to figure this. I thought it was reading the etherscan input data to see the password parameter in the constructor but I gave up after a while

*selfdesctruct(<payable> _address)* makes the contract go boom and sends all the ether to the *_address*. This can be used in forcing ether into other contracts and in this case what we want.

```javascript
password = web3.eth.getStorageAt(contract.address, 1)
```

Using *web3* we can see the storage of the contract, and index 1 is the password(since index 0 is the locked variable)