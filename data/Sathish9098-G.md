
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
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

53: string[] memory urls = new string[](1);

```
[OffchainDNSResolver.sol#L53](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L53)

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

## [G-4] For events use 3 indexed rule to save gas

Need to declare 3 indexed fields for event parameters. If the event parameter is less than 3 should declare all event parameters indexed

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

47: event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
    );
53: event NewPublicSuffixList(address suffixes);

```
[DNSRegistrar.sol#L47-L53](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L47-L53)

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
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }

```
[OffchainDNSResolver.sol#L43-L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

62: previousRegistrar = _previousRegistrar;
63: resolver = _resolver;
64: oracle = _dnssec;
65: suffixes = _suffixes;
67: ens = _ens;
```
[DNSRegistrar.sol#L62-L67](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L62-L67)

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

##

## [G-6] Use nested if and, avoid multiple check combinations

> Instances()

> Approximate Gas Saved:  gas

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

As per Solidity [reports](https://gist.github.com/sathishpic22/fe96671bafb22ceaace7fc05a66bd115) possible to save 9 gas

```solidity
FILE: FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

178: if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

```
[OffchainDNSResolver.sol#L178](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L178)

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
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

86:  if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            ) {

144:  if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {

```
[OffchainDNSResolver.sol#L86-L90](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90)

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
File; 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

modifier onlyOwner() {
        Root root = Root(ens.owner(bytes32(0)));
        address owner = root.owner();
        require(msg.sender == owner);
        _;
    }

```
[DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

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
[]()

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
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

102: if (dnsresolver != address(0)) 
197: if (resolver == address(0)) {

```
[OffchainDNSResolver.sol#L102](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L102)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

115: if (addr != address(0)) {
116: if (resolver == address(0)) {

```
[DNSRegistrar.sol#L115-L116](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L115-L116)

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

## [G-19] Avoid emitting constants

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

##

## [G-20] Modifiers only called once can be inlined to save gas 

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

73: modifier onlyOwner() {

```
[DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

##

## [G-21] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

```solidity
FILE:  2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

166: function enableNode(bytes memory domain) public returns (bytes32 node) {

174: function _enableNode(
        bytes memory domain,
        uint256 offset
    ) internal returns (bytes32 node) {

```
[DNSRegistrar.sol#L166](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L166)





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