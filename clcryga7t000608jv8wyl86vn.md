# How to Solve EVM Puzzles

EVM Puzzles is a [GitHub repo](https://github.com/fvictorio/evm-puzzles) from [fvictorio](https://github.com/fvictorio) containing a collection of puzzles to teach how the EVM operates.  
For each challenge we are supplied with the bytecode of the contract, the objective is to send a successful transaction.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-0-1024x451.png align="left")

GitHub Readme

> In some puzzles you only need to provide the value that will be sent to the contract, in others the calldata, and in others both values.
> 
> [<cite>Main Docs Page</cite>](https://github.com/fvictorio/evm-puzzles#how-to-play)

### Let's Get Started

We need to start by cloning the repository.

```solidity
git clone https://github.com/fvictorio/evm-puzzles.git
```

Change directory.

```solidity
cd evm-puzzles
```

Install dependencies.

```solidity
npm install
```

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-3.png align="left")

git clone, cd, npm install

Once everything has installed we are ready to start the game.

```solidity
npx hardhat play
```

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-4.png align="left")

npx hardhat play

Puzzle 1 has given us our first list of opcodes and asks us for a value to send.

### [evm.codes](http://evm.codes) EVM Opcodes & Playground

Looking back at the docs from evm-puzzles, it suggested [evm.codes](http://evm.codes)' opcodes reference and playground to solve the puzzles.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-1-1024x860.png align="left")

[evm.codes](http://evm.codes) opcodes reference

Using the menu at the top we can navigate to the [evm.codes](http://evm.codes) [playground](https://evm.codes/playground).

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-5-1024x410.png align="left")

EVM Playground

Once we are on the playground we can change the contract to bytecode with this drop down menu.  
Erasing the sample contract and then entering in the opcodes from our puzzle will give us an overview of our contract.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-6.png align="left")

[evm.codes](http://evm.codes) playground containing puzzle 1 bytecode

Remember the goal of each puzzle is to send a transaction that **does not revert**.  
Inspecting some of the Opcodes we can get an idea for how the contract works.

### Opcodes

<table><tbody><tr><td colspan="1" rowspan="1"><p><strong>INDEX</strong></p></td><td colspan="1" rowspan="1"><p><strong>OPCODE</strong></p></td><td colspan="1" rowspan="1"><p><strong>NAME</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>[00]</p></td><td colspan="1" rowspan="1"><p>34</p></td><td colspan="1" rowspan="1"><p>CALLVALUE</p></td></tr><tr><td colspan="1" rowspan="1"><p>[01]</p></td><td colspan="1" rowspan="1"><p>56</p></td><td colspan="1" rowspan="1"><p>JUMP</p></td></tr><tr><td colspan="1" rowspan="1"><p>[02]</p></td><td colspan="1" rowspan="1"><p>FD</p></td><td colspan="1" rowspan="1"><p>REVERT</p></td></tr><tr><td colspan="1" rowspan="1"><p>[03]</p></td><td colspan="1" rowspan="1"><p>FD</p></td><td colspan="1" rowspan="1"><p>REVERT</p></td></tr><tr><td colspan="1" rowspan="1"><p>[04]</p></td><td colspan="1" rowspan="1"><p>FD</p></td><td colspan="1" rowspan="1"><p>REVERT</p></td></tr><tr><td colspan="1" rowspan="1"><p>[05]</p></td><td colspan="1" rowspan="1"><p>FD</p></td><td colspan="1" rowspan="1"><p>REVERT</p></td></tr><tr><td colspan="1" rowspan="1"><p>[06]</p></td><td colspan="1" rowspan="1"><p>FD</p></td><td colspan="1" rowspan="1"><p>REVERT</p></td></tr><tr><td colspan="1" rowspan="1"><p>[07]</p></td><td colspan="1" rowspan="1"><p>FD</p></td><td colspan="1" rowspan="1"><p>REVERT</p></td></tr><tr><td colspan="1" rowspan="1"><p>[08]</p></td><td colspan="1" rowspan="1"><p>5B</p></td><td colspan="1" rowspan="1"><p>JUMPDEST</p></td></tr><tr><td colspan="1" rowspan="1"><p>[09]</p></td><td colspan="1" rowspan="1"><p>00</p></td><td colspan="1" rowspan="1"><p>STOP</p></td></tr></tbody></table>

Puzzle 1 Opcodes

There are 5 different Opcodes here, let's define what they do starting at the top of the stack as described by the opcodes reference chart.

* **34 CALLVALUE** "Get deposited value by the instruction/transaction responsible for this execution"
    
* **56 JUMP** "Alter the program counter"
    
* **FD REVERT** "Halt execution reverting state changes but returning data and remaining gas"
    
* **5B JUMPDEST** "Mark a valid destination for jumps"
    
* **00 STOP** "Halts execution"
    

What instantly jumps out at me is the 6x **FD REVERT** back to back, with **56 JUMP** on one side and **5B JUMPDEST** on the other end.

### Program Counter

Since we don't want our transaction to halt execution and revert, I believe we should inspect the JUMP and JUMPDEST codes a bit closer.

From the notes in **56 JUMP** we learn about an important part of how the deployed code actually executes.  
The program counter is a byte offset in the deployed code to indicate which instruction will execute next.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-7-1024x523.png align="left")

56 JUMP

In the simplest example given, when an **01 ADD** is executed the program counter is incremented by 1 since the instruction is 1 byte.  
The **JUMP** instruction can alter the program counter and break the linear path of execution to a different point in the stack.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-8-1024x375.png align="left")

5B JUMPDEST

**JUMPDEST** is simply the valid destination for us to **JUMP** to.  
Knowing this I believe we can solve Puzzle 1.

### Puzzle 1 Solution

Puzzle 1's first instruction is **CALLVALUE (34)**

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-9-1024x318.png align="left")

CALLVALUE

This takes the value of the call in wei and puts it atop the stack.  
Knowing this, we must pass the correct value to this **CALLVALUE** instruction, leading to the **JUMP** instruction correctly ending at the **JUMPDEST**.

Doing some testing in the playground, let's send a transaction with a value of 1 wei.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-10-1024x439.png align="left")

Enter 1 wei, click run, step through the execution.

If we follow the execution of the stack, when we get to the **JUMP** we run into an **\[Error\] invalid JUMP**.  
So we are on the right track but we need to rethink what value we are sending with the transaction.

Looking back at the **JUMP** documentation, the stack input takes *"counter: byte offset in the deployed code where execution will continue from. Must be a JUMPDEST instruction."*

Knowing this the byte offset of our **JUMPDEST** is 8.

Let's retry the transaction with a value of 8 wei.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-11-1024x313.png align="left")

Transaction with a value of 8 wei is successful

Upon reaching the **JUMP** instruction, we no longer run into an error, we **JUMP** down to byte offset 8 AKA our **JUMPDEST**.  
Let's go back to our original terminal and submit our value of 8.

![](https://SecuringWeb3.xyz/wp-content/uploads/2023/01/EVM-12.png align="left")

Puzzle 1 solved!

### Success!

We have solved the first EVM puzzle! Way to go.  
I hope this post can serve as a guide to getting started with EVM puzzles.

Be on the lookout for more puzzle level walk-throughs in the days to come.

**DAVE**