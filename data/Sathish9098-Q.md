 # GAS OPTIMIZATION

##

## [G-1] State variables should be cached in stack variables rather than re-reading them from storage

> Instances()

> Approximate gas saved: 

Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read.
Less obvious fixes/optimizations include having local storage variables of mappings within state variable mappings or mappings within state variable structs, having local storage variables of structs within mappings, having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.


```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```


##

## [G-2] Using storage instead of memory for structs/arrays saves gas

> Instances()

> Approximate gas saved: 

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```solidity
```

```solidity
```

```solidity
```

```solidity
```

```solidity
```

## [G-3] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

> Instances()
> Instances()

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key’s keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```



##

## [G-5] Lack of input value checks cause a redeployment if any human/accidental errors

> Instances()

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables.

If any human/accidental errors happen need to redeploy the contract so this create the huge gas lose

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-6] Use nested if and, avoid multiple check combinations

> Instances()

> Approximate Gas Saved:  gas

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

As per Solidity [reports](https://gist.github.com/sathishpic22/fe96671bafb22ceaace7fc05a66bd115) possible to save 9 gas

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```


##

## [G-7] No need to evaluate all expressions to know if one of them is true

> Instances()

When we have a code expressionA || expressionB if expressionA is true then expressionB will not be evaluated and gas saved

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-8] The result of function calls should be cached rather than re-calling the function

> Instances()

In Solidity, caching repeated function calls can be an effective way to optimize gas usage, especially when the function is called frequently with the same arguments

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-9] Amounts should be checked for 0 before calling a transfer

> Instances()

Checking non-zero transfer values can avoid an expensive external call and save gas.
While this is done at some places, it’s not consistently done in the solution.
I suggest adding a non-zero-value check here

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity


```

```solidity

```

```solidity

```
##

## [G-10] Don't declare the variable inside the loops

> Instances()

In every iterations the new variables instance created this will consumes more gas . So just declare variables outside the loop and only use inside to save gas

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-11] Empty blocks should be removed to save deployment cost

> Instances()

```solidity

```

```solidity

```

```solidity

```

```solidity

```


##

## [G-12] Functions should be used instead of modifiers to save gas

> Instances()

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-13] Avoid contract existence checks by using low level calls

> Instances()

> Approximate gas saved:  gas

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-14] Sort Solidity operations using short-circuit mode

> Instances(3)

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```

//f(x) is a low gas cost operation
//g(y) is a high gas cost operation
//Sort operations with different gas costs as follows
f(x) || g(y)
f(x) && g(y)

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-15] Use assembly to check for address(0)

> Instances()

Saves 6 gas per instance

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-16] Shorthand way to write if / else statement can reduce the deployment cost

> Instances()

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-17] The Less gas consuming condition checks should be on top

> Instances()

When writing conditional statements in smart contracts, it is generally best practice to order the conditions so that the less gas-consuming checks are performed first. This can help to optimize the gas usage of the contract and improve its overall efficiency

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [G-18] internal functions not called by the contract should be removed to save deployment gas

> Instances()

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```solidity

```

```solidity

```

```solidity

```


```solidity

```


```solidity

```


```solidity

```


##

## [G-20] Avoid emitting constants

> Instances()

One way to optimize your smart contract and reduce the gas cost is to avoid emitting constants. When you declare a constant in your contract, it is stored on the blockchain and takes up space. This can increase the cost of deploying your contract and make it more expensive to execute.

To avoid emitting constants, you can use inline assembly to perform arithmetic operations or bitwise operations. You can also use local variables instead of constants, and calculate the values you need at runtime

```solidity

```

```solidity

```

```solidity

```

```solidity

```





LOW‑1	Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug	2
LOW‑2	Contracts are not using their OZ Upgradeable counterparts	2
LOW‑3	Pragma Experimental ABIEncoderV2 is Deprecated	1
LOW‑4	Remove unused code	2
LOW‑5	require() should be used instead of assert()	3
LOW‑6	Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project	1
LOW‑7	Upgrade OpenZeppelin Contract Dependency	1

NC‑1	Add a timelock to critical functions	3
NC‑2	Avoid Floating Pragmas: The Version Should Be Locked	17
NC‑3	Variable Names That Consist Of All Capital Letters Should Be Reserved For Const/immutable Variables	2
NC‑4	Constants in comparisons should appear on the left side	9
NC‑5	Critical Changes Should Use Two-step Procedure	3
NC‑6	Declare interfaces on separate files	1
NC‑7	Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function	2
NC‑8	Function writing that does not comply with the Solidity Style Guide	19
NC‑9	Use delete to Clear Variables	3
NC‑10	Imports can be grouped together	44
NC‑11	NatSpec return parameters should be included in contracts	1
NC‑12	No need to initialize uints to zero	7
NC‑13	Initial value check is missing in Set Functions	3
NC‑14	Contracts should have full test coverage	1
NC‑15	Implementation contract may not be initialized	3
NC‑16	NatSpec comments should be increased in contracts	1
NC‑17	Non-usage of specific imports	44
NC‑18	Use a more recent version of Solidity	19
NC‑19	Omissions in Events	1
NC‑20	Open TODOs	3
NC‑21	Using >/>= without specifying an upper bound is unsafe	1
NC‑22	Public Functions Not Called By The Contract Should Be Declared External Instead	4
NC‑23	require() / revert() Statements Should Have Descriptive Reason Strings	13
NC‑24	Use bytes.concat()	8