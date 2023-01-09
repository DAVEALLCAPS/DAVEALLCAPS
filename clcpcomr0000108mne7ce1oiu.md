# Hello Ethernaut | OpenZeppelin Ethernaut Level 0 Walk-through

This is the first in the Ethernaut write-up series by CAPS LOCK DAVE.  
[LEVEL 0 CAN BE FOUND HERE](https://ethernaut.openzeppelin.com/level/0)

![](https://cdn-images-1.medium.com/max/1100/0*p7x4pfx3k8Lr7Bk-.png align="left")

This level serves as the tutorial and guides us through the setup needed to participate in the CTF.

We need:

* MetaMask Wallet with Testnet Ether
    
* Web Browser Console (Dev Tools)
    

Connecting our MetaMask wallet will allow us to track our progress and create a new instance of the challenge contracts, which are actually deployed to the Goerli testnet.

Opening up the console we can see a welcome message and some addresses, as well as a help() command that can give us more info on commands that may be useful.

![](https://cdn-images-1.medium.com/max/1100/0*NP-YHnvtaXJjVK5x.png align="left")

Following some of the tutorial commands, we can see that player returns my wallet address, getBalance(player) returns my wallet balance, and the help() command returns a list of methods that we can use.

![](https://cdn-images-1.medium.com/max/1100/0*gtiY_M07xjmJ8QW4.png align="left")

(Note: I’m using Chrome Dev Tools so I use await before most methods to directly return the value, some other browsers might not have this need.)

Next we need to request an instance of the level so that we can complete it.

![](https://cdn-images-1.medium.com/max/1100/0*QOLbT8eJuMHFGDF-.png align="left")

Clicking Get new instance should make a MetaMask transaction popup for you to sign.

![](https://cdn-images-1.medium.com/max/1100/0*5pCLS5jQq6zuotXR.png align="left")

After signing this transaction, wait for it to be mined, and your instance will be created.

![](https://cdn-images-1.medium.com/max/1100/0*Z8Y2Aq0NoyLPcngW.png align="left")

Now we can start inspecting the contract using the [contract.info](http://contract.info)() method.

![](https://cdn-images-1.medium.com/max/1100/0*TRmyfQWhW8uadU7Y.png align="left")

After following some tricky methods we have been lead to this last hint, ‘If you know the password, submit it to authenticate().’

I think we have figured out how to solve the level, now we need the password.

Let’s do some digging into the abi with contract.abi.

![](https://cdn-images-1.medium.com/max/1100/0*syjn5-vVDuDcu5Cs.png align="left")

In the abi we can see a method named password, let’s try that and see if we can authenticate.

![](https://cdn-images-1.medium.com/max/1100/0*4CzgLYlsfxH7XOm4.png align="left")

Once we send the password using the authenticate method a transaction should popup for you to sign.

Once the transaction has been mined we are ready to submit our instance.

![](https://cdn-images-1.medium.com/max/1100/0*vTsbLJkD7jWZwz1s.png align="left")

Ethernaut gives us this colorful congratulations message, we’ve completed this level.

The contract code is revealed to us to show what we have been interacting with from the console.

![](https://cdn-images-1.medium.com/max/1100/0*rcbfz2aXX51IzFpV.png align="left")

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Instance {

  string public password;
  uint8 public infoNum = 42;
  string public theMethodName = 'The method name is method7123949.';
  bool private cleared = false;

  // constructor
  constructor(string memory _password) {
    password = _password;
  }

  function info() public pure returns (string memory) {
    return 'You will find what you need in info1().';
  }

  function info1() public pure returns (string memory) {
    return 'Try info2(), but with "hello" as a parameter.';
  }

  function info2(string memory param) public pure returns (string memory) {
    if(keccak256(abi.encodePacked(param)) == keccak256(abi.encodePacked('hello'))) {
      return 'The property infoNum holds the number of the next info method to call.';
    }
    return 'Wrong parameter.';
  }

  function info42() public pure returns (string memory) {
    return 'theMethodName is the name of the next method.';
  }

  function method7123949() public pure returns (string memory) {
    return 'If you know the password, submit it to authenticate().';
  }

  function authenticate(string memory passkey) public {
    if(keccak256(abi.encodePacked(passkey)) == keccak256(abi.encodePacked(password))) {
      cleared = true;
    }
  }

  function getCleared() public view returns (bool) {
    return cleared;
  }
}
```

That’s it for level 0, time to move on to the first real challenge.

**DAVE**

*Originally published at* [*https://securingweb3.xyz*](https://securingweb3.xyz)*.*