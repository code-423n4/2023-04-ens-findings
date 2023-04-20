 # LOW FINDINGS

##

## [L-1] Loss of precision due to rounding

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

## [L-2] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256

Using the SafeCast library can help prevent unexpected errors in your Solidity code and make your contracts more secure

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

### Recommended Mitigation Steps:
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256.

##

## [L-3] Vulnerable to cross-chain replay attacks due to static DOMAIN_SEPARATOR/domainSeparator

See this [issue](https://github.com/code-423n4/2021-04-maple-findings/issues/2) from a prior contest for details

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

## [L-4] MIXING AND OUTDATED COMPILER

The pragma version used are: 0.8.0

The minimum required version must be 0.8.17; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

0.8.17
Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements

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

## [L-5] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). "Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead. If all arguments are strings and or bytes, bytes.concat() should be used instead

```solidity

```

```solidity

```

```solidity

```

```solidity

```

##

## [L-6] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks for critical changes. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values

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

## [L-7] Function Calls in Loop Could Lead to Denial of Service

Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions


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

## [L-8] Use .call instead of .transfer to send ether

.transfer will relay 2300 gas and .call will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using .transfer will fail, causing integration issues

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

## [L-9] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

##

## [L-10]  ALLOWS MALLEABLE SECP256K1 SIGNATURES

Here, the ecrecover() method doesn’t check the s range.

Homestead (EIP-2) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.

Since an order can only be confirmed once and its hash is saved, there doesn’t seem to be a serious danger in existing use cases

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149

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

## [L-11] AVOID HARDCODED VALUES

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code

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

## [L-12] Front running attacks by the onlyOwner

owner value is not a constant value and can be changed with transferOwnership() function, before a function using setOwner state variable value in the project, transferOwnership function can be triggered by onlyOwner and operations can be blocked

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

## [L-13]





# NON CRITICAL FINDINGS

##

## [NC-1] immutable should be uppercase




##

## [NC-2] Missing NATSPEC



##

## [NC-3] For functions, follow Solidity standard naming conventions (internal function style rule)

### Description
The above codes don’t follow Solidity’s standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

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

## [NC-4] Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)

While the compiler knows to optimize away the exponentiation, it’s still better coding practice to use idioms that do not require compiler optimization, if they exist

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

## [NC-5] Need Fuzzing test for unchecked



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

## [NC-6] Remove commented out code


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

## [NC-7] Inconsistent spacing in comments

Some lines use // x and some use //x. The instances below point out the usages that don’t follow the majority, within each file



##

## [NC-8] Inconsistent method of specifying a floating pragma

Some files use >=, some use ^. The instances below are examples of the method that has the fewest instances for a specific version. Note that using >= without also specifying <= will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so ^ should be preferred regardless of the instance counts


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

## [NC-9] Numeric values having to do with time should use time units for readability

There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they’re defined, they should be used


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

## [NC-10] NO SAME VALUE INPUT CONTROL


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

## [NC-11] Contract layout and order of functions

The Solidity style guide [recommends](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout)

Declare internal functions bellow the external/public functions


##

## [NC-12] Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated.

A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.



##

## [NC-13] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword


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

## [NC-14] Tokens accidentally sent to the contract cannot be recovered

It can’t be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts

### Recommended Mitigation Steps
Add this code:
```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}
```

##

## [NC-15] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

##

## [NC-16] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns).

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html




##

## [NC-17] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone


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

## [NC-18] Take advantage of Custom Error’s return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly


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

## [NC-19] Use constants instead of using numbers directly without explanations


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







LOW‑1   Use of ecrecover is susceptible to signature malleability       1
LOW‑2   Event is missing parameters     2
LOW‑3   Possible rounding issue 1
LOW‑4   Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug        1
LOW‑5   Minting tokens to the zero address should be avoided    1
LOW‑6   Missing Checks for Address(0x0) 1
LOW‑7   Prevent division by 0   2
LOW‑8   Use safetransfer Instead Of transfer    20
LOW‑9   Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project 7
LOW‑10  TransferOwnership Should Be Two Step    1
LOW‑11  Unbounded loop  2
LOW‑12  Use safeTransferOwnership instead of transferOwnership function 1

NC‑1    Add a timelock to critical functions    1
NC‑2    Avoid Floating Pragmas: The Version Should Be Locked    8
NC‑3    Constants Should Be Defined Rather Than Using Magic Numbers     5
NC‑4    Critical Changes Should Use Two-step Procedure  1
NC‑5    Declare interfaces on separate files    1
NC‑6    Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function     4
NC‑7    Event Is Missing Indexed Fields 3
NC‑8    Function writing that does not comply with the Solidity Style Guide     10
NC‑9    Large or complicated code bases should implement fuzzing tests  1
NC‑10   Imports can be grouped together 11
NC‑11   NatSpec return parameters should be included in contracts       1
NC‑12   Initial value check is missing in Set Functions 1
NC‑13   Lines are too long      1
NC‑14   Implementation contract may not be initialized  6
NC‑15   NatSpec comments should be increased in contracts       1
NC‑16   Non-usage of specific imports   28
NC‑17   Use a more recent version of Solidity   10
NC‑18   Open TODOs      1
NC‑19   Using >/>= without specifying an upper bound is unsafe  2
NC‑20   Public Functions Not Called By The Contract Should Be Declared External Instead 3
NC‑21   Empty blocks should be removed or emit something        1
NC‑22   require() / revert() Statements Should Have Descriptive Reason Strings  10
NC‑23   Large multiples of ten should use scientific notation   6
NC‑24   Use bytes.concat()      1
NC‑25   Use Underscores for Number Literals     6

Updated by: Sathish9098


submissions@code423n4.com
1:14 AM (11 hours ago)
to submissions


https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/Position.sol#L181-L189

https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/Position.sol#L292-L296

https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/Position.sol#L366-L390

https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/MintingHub.sol#L54-L57

https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/MintingHub.sol#L59-L65

https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/MintingHub.sol#L235-L237
+ 15: mapping (address => uint256) public nonces;
FILE: 2023-04-frankencoin/contracts/Frankencoin.sol

118: return minterReserveE6 / 1000000;
166: uint256 usableMint = (_amount * (1000_000 - _feesPPM - _reservePPM)) / 1000_000; // rounding down is fine
205: uint256 theoreticalReserve = _reservePPM * mintedAmount / 1000000;
239: return 1000000 * amountExcludingReserve / (1000000 - adjustedReservePPM); // 41 / (1-18%) = 50


```
[Frankencoin.sol#L118](https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/Frankencoin.sol#L118)

##

## [NC-20] Shorthand way to write if / else statement

The normal if / else statement can be refactored in a shorthand way to write it:

Increases readability
Shortens the overall SLOC

```solidity
FILE: 2023-04-frankencoin/contracts/Position.sol

184: if (time >= exp){
            return 0;
        } else {
            return uint32(mintingFeePPM - mintingFeePPM * (time - start) / (exp - start));
        }

160: if (newPrice > price) {
            restrictMinting(3 days);
        } else {
            checkCollateral(collateralBalance(), newPrice);
        }

121: if (afterFees){
            return totalMint * (1000_000 - reserveContribution - calculateCurrentFee()) / 1000_000;
        } else {
            return totalMint * (1000_000 - reserveContribution) / 1000_000;
        }

250: if (token == address(collateral)){
            withdrawCollateral(target, amount);
        } else {
            IERC20(token).transfer(target, amount);
        }

```
[Position.sol#L184-L188](https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/Position.sol#L184-L188)

```solidity
FILE: 2023-04-frankencoin/contracts/MintingHub.sol

267: if (effectiveBid > fundsNeeded){
            zchf.transfer(owner, effectiveBid - fundsNeeded);
        } else if (effectiveBid < fundsNeeded){
            zchf.notifyLoss(fundsNeeded - effectiveBid); // ensure we have enough to pay everything
        }
```
[MintingHub.sol#L267-L271](https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/MintingHub.sol#L267-L271)

```solidity
FILE: 2023-04-frankencoin/contracts/Frankencoin.sol

141: if (balance <= minReserve){
        return 0;
      } else {
        return balance - minReserve;
      }

207: if (currentReserve < minterReserve()){
         // not enough reserves, owner has to take a loss
         return theoreticalReserve * currentReserve / minterReserve();
      } else {
         return theoreticalReserve;
      }


```
[Frankencoin.sol#L141-L145](https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/Frankencoin.sol#L141-L145)

### Recommended Mitigation

```solidity

time >= exp ? return 0 : return uint32(mintingFeePPM - mintingFeePPM * (time - start) / (exp - start));

```

##

## [NC-21] Return values of approve() not checked

Not all IERC20 implementations revert() when there’s a failure in approve(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

```solidity
FILE: 2023-04-frankencoin/contracts/ERC20.sol

109: _approve(msg.sender, spender, value);
132: _approve(sender, msg.sender, currentAllowance - amount);

```
[ERC20.sol#L109](https://github.com/code-423n4/2023-04-frankencoin/blob/1022cb106919fba963a89205d3b90bf62543f68f/contracts/ERC20.sol#L109)






GAS‑1	abi.encode() is less efficient than abi.encodepacked()	1	100
GAS‑2	<array>.length Should Not Be Looked Up In Every Loop Of A For-loop	2	194
GAS‑3	Use calldata instead of memory for function parameters	6	1800
GAS‑4	Setting the constructor to payable	3	39
GAS‑5	Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function	2	56
GAS‑6	Using delete statement can save gas	10	-
GAS‑7	++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)	5	30
GAS‑8	Functions guaranteed to revert when called by normal users can be marked payable	3	63
GAS‑9	Use hardcoded address instead address(this)	4	-
GAS‑10	It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied	4	-
GAS‑11	internal functions only called once can be inlined to save gas	63	1386
GAS‑12	Optimize names to save gas	9	198
GAS‑13	<x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables	25	-
GAS‑14	Public Functions To External	6	-
GAS‑15	require() Should Be Used Instead Of assert()	3	-
GAS‑16	require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas	3	-
GAS‑17	Save gas with the use of specific import statements	44	-
GAS‑18	Shorten the array rather than copying to a new one	1	-
GAS‑19	Splitting require() Statements That Use && Saves Gas	1	9
GAS‑20	Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It	1	-
GAS‑21	Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead	40	-
GAS‑22	++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops	7	245
GAS‑23	Using unchecked blocks to save gas	4	80
GAS‑24	Use v4.8.1 OpenZeppelin contracts	1	-
GAS‑25	Use solidity version 0.8.19 to gain some gas boost	19	1672
GAS‑26	Use uint256(1)/uint256(2) instead for true and false boolean states	2	10000