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
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

222: keccak256(
                abi.encodePacked(parentNode, name.keccak(idx, separator - idx))
            );

```
[OffchainDNSResolver.sol#L222-L224](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L222-L224)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

119: bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
151: bytes32 node = keccak256(abi.encodePacked(parentNode, labelHash));

```
[DNSRegistrar.sol#L119](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L119)

```solidity

```

```solidity

```

##

## [L-6] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks for critical changes. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }

```
[OffchainDNSResolver.sol#L43-L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47)

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

## [L-10] ALLOWS MALLEABLE SECP256K1 SIGNATURES

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


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

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

# NON CRITICAL FINDINGS

##

## [NC-1] immutable should be uppercase

```solidity
FILE : 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

37: ENS public immutable ens;
38: DNSSEC public immutable oracle;

```
[OffchainDNSResolver.sol#L37-L38](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L38)

### Recommended Mitigation

```solidity
- 38: DNSSEC public immutable oracle;
+ 38: DNSSEC public immutable ORACLE;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L26-L27

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L29-L30


##

## [NC-2] Missing NATSPEC

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L22-L27

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L49-L52

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L65-L69






##

## [NC-3] For functions, follow Solidity standard naming conventions (internal function style rule)

### Description
The above codes don’t follow Solidity’s standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

```solidity
File: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

136: function parseRR(
        bytes memory data,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (address, bytes memory) {

162: function readTXT(
        bytes memory data,
        uint256 startIdx,
        uint256 lastIdx
    ) internal pure returns (bytes memory) {

173: function parseAndResolve(
        bytes memory nameOrAddress,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (address) {

190: function resolveName(
        bytes memory name,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (address) {

209: function textNamehash(
        bytes memory name,
        uint256 idx,
        uint256 lastIdx
    ) internal view returns (bytes32) {

```
[OffchainDNSResolver.sol#L136-L140](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L136-L140)

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
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)



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
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

144: if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {
149: uint256 lastTxtIdx = txt.find(5, txt.length - 5, " ");
151: address dnsResolver = parseAndResolve(txt, 5, txt.length);
154: address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);

```
[OffchainDNSResolver.sol#L144](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L144)

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

## [NC-20] Shorthand way to write if / else statement

The normal if / else statement can be refactored in a shorthand way to write it:

Increases readability
Shortens the overall SLOC


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

if (separator < lastIdx) {
            parentNode = textNamehash(name, separator + 1, lastIdx);
        } else {
            separator = lastIdx;
        }

```
[OffchainDNSResolver.sol#L216-L220](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220)


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



### Recommended Mitigation

```solidity

time >= exp ? return 0 : return uint32(mintingFeePPM - mintingFeePPM * (time - start) / (exp - start));

```

##

## [NC-21] Return values of approve() not checked

Not all IERC20 implementations revert() when there’s a failure in approve(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything


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

## [NC-22] For critical changes should emit both old and new values 

Emitting both old and new values for critical changes is a good practice in Solidity, as it provides a way for external parties (such as other smart contracts or off-chain applications) to track and verify the changes made to a smart contract state.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

##

## [NC-23] Don't use named return variables its confusing

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

113: function _claim(
        bytes memory name,
        DNSSEC.RRSetWithSignature[] memory input
    ) internal returns (bytes32 parentNode, bytes32 labelHash, address addr) {

166: function enableNode(bytes memory domain) public returns (bytes32 node) {

174: function _enableNode(
        bytes memory domain,
        uint256 offset
    ) internal returns (bytes32 node) {


```
[DNSRegistrar.sol#L133-L136](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L133-L136)





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