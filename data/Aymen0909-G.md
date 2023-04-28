# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1 | `storage` variable should be cached into `memory` variables instead of re-reading them  |  1 |
| 2 | Use `unchecked` blocks to save gas | 5 |
| 3 | Use `assembly` to check for `address(0)` | 5 |
| 4 | Reorder line of codes to save gas | 1 |
| 5 | `memory` values should be emitted in events instead of `storage` ones  | 2 |
| 6 | Using `private` rather than `public` for constants, saves gas |4|


## Findings

### 1- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There is 1 instance of this issue :

File: DNSSECImpl.sol [Line 317](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317)
```
if (address(digests[digesttype]) == address(0)) {
    return false;
}
return digests[digesttype].verify(data, digest);
```

In the code linked above the variable `digests[digesttype]` is read two times from storage and because its value won't be changed in those lines of code, `digests[digesttype]` should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

The code should be modified as follow :

```
Digest digest = digests[digesttype];
if (address(digest) == address(0)) {
    return false;
}
return digest.verify(data, digest);
```

### 2- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 5 instances of this issue:

File: DNSClaimChecker.sol [Line 53-60](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L53-L60)
```
idx += 1;

...

idx += len;
```

In the code linked above the increments of `idx` cannot overflow because of the check line [51](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L51), so the operations should be marked as `unchecked` to save gas. 

File: RRUtils.sol [Line 269](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L269)
```
counts--;
```

In the code linked above the decrement of `counts` cannot underflow because of the check line [267](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L267), so the operation should be marked as `unchecked` to save gas. 

File: RRUtils.sol [Line 294](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L294)
```
counts--;
```

In the code linked above the decrement of `counts` cannot underflow because of the check line [291](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L291), so the operation should be marked as `unchecked` to save gas. 

File: RRUtils.sol [Line 300](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L300)
```
othercounts--;
```

In the code linked above the decrement of `othercounts` cannot underflow because of the check line [297](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L297), so the operation should be marked as `unchecked` to save gas. 

File: RRUtils.sol [Line 309](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L309)
```
counts -= 1;
```

In the code linked above the decrement of `counts` cannot underflow because of the check line [304](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304), so the operation should be marked as `unchecked` to save gas. 


### 3- Use `assembly` to check for `address(0)` :

You should assembly when checking if an address is `address(0)` or not, this saves gas compared to the normal checks.

There are 5 instances of this issue:

File: DNSSECImpl.sol [Line 317](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317)

File: DNSSECImpl.sol [Line 420](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420)

File: DNSRegistrar.sol [Line 115-116](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115-L116)

File: OffchainDNSResolver.sol [Line 102](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L102)

File: OffchainDNSResolver.sol [Line 197](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197)


### 4- Reorder lines of code to save gas :

When some lines of code in a function can revert on a certain condition and they can be excuted in the beginning of the function, they should not be placed at the end of the function as this would cost more gas if the transaction reverts. In this case those lines of code should be placed as high as possible in the function.

There is 1 instance of this issue:

File: DNSRegistrar.sol [Line 157-161](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L157-L161)
```
bool found;
(addr, found) = DNSClaimChecker.getOwnerAddress(name, data);
if (!found) {
    revert NoOwnerRecordFound();
}
```

Those lines of code can be placed at the start of the function to save gas :

```
function _claim(
    bytes memory name,
    DNSSEC.RRSetWithSignature[] memory input
) internal returns (bytes32 parentNode, bytes32 labelHash, address addr) {
    (bytes memory data, uint32 inception) = oracle.verifyRRSet(input);

    // @audit placed at the beginning to save gas
    bool found;
    (addr, found) = DNSClaimChecker.getOwnerAddress(name, data);
    if (!found) {
        revert NoOwnerRecordFound();
    }

    ...

    inceptions[node] = inception;

    emit Claim(node, addr, name, inception);
}
```

### 5- `memory` values should be emitted in events instead of `storage` ones :

The values emitted in events shouldn’t be read from storage but the existing memory values should be used instead, this will save **~100 GAS**.

There are 2 instances of this issue :

File: DNSRegistrar.sol [Line 66](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L66)
```
emit NewPublicSuffixList(address(suffixes));
```

It should be replaced by : 

```
emit NewPublicSuffixList(address(_suffixes));
```

File: DNSRegistrar.sol [Line 82](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82)
```
eemit NewPublicSuffixList(address(suffixes));
```
It should be replaced by : 

```
emit NewPublicSuffixList(address(_suffixes));
```

### 6- Using `private` rather than `public` for constants, saves gas :

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 4 instances of this issue:

File: DNSSECImpl.sol [Line 27-32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27-L32)

```
uint16 constant DNSCLASS_IN = 1;
uint16 constant DNSTYPE_DS = 43;
uint16 constant DNSTYPE_DNSKEY = 48;
uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
```