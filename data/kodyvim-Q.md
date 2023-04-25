# QA report
## No setter method for `gatewayURL`.
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L46
In `offchainDNSResolver` if the `gatewayURL` is down there is no way to provide an alternative url since the parameter is set on the constructor and their is no setter method for the variable.
The variable is not immutable which shows there was a possibility of changing/resetting the variable but was not implemented.
Recommedation:
Add a setter method for `gatewayURL` with proper access control.

## using same name for multiple contracts 
The following files have the same contract name.
```
contracts/dnssec-oracle/BytesUtils.sol BytesUtils
contracts/wrapper/BytesUtils.sol BytesUtils
```
The compilation artifacts will not contain one of the contracts with the duplicate name. Therefore, only one of the two contracts will generate artifacts.

## proveAndClaim does not return the value of the subnode.
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L90
the `proveAndClaim` function makes a subcall to `ens.setSubnodeOnwer` which returns the value of the subnode but does not return the value in `proveAndClaim`, return value of such function calls may be useful to smart contracts and automated tools.

# Gas Optimisations
## Multiple mappings related to the same data can be combined into a single mapping using a struct:
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.
Instance:
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L45
```solidity
mapping(uint8 => Algorithm) public algorithms;
mapping(uint8 => Digest) public digests;
```
can be refactored into:
```solidity
struct Params {
	Algorithm algo;
	Digest digest;
}
mapping (uint8 => Params) public params;
```
