
# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1 | Immutable state variables lack zero address checks | Low | 6 |
| 2 | Named return variables not used anywhere in the functions | NC | 4 |
| 3 | Constant redefined elsewhere | NC | 2 |
| 4 | Unsed imports | NC | 1 |

## Findings

### 1- Immutable state variables lack zero address checks :

Constructors should check the values written in an immutable state variables(address) is not the address(0).

#### Risk : Low

#### Proof of Concept
Instances include:

File: DNSRegistrar.sol [Line 62-64](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L64)
```
previousRegistrar = _previousRegistrar;
resolver = _resolver;
oracle = _dnssec;
```

File: DNSRegistrar.sol [Line 67](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L67)
```
ens = _ens;
```

File: OffchainDNSResolver.sol [Line 44-45](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L44-L45)
```
ens = _ens;
oracle = _oracle;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.


### 2- Adding a `return` statement when the function defines a named return variable, is redundant :

#### Risk : Non critical

#### Proof of Concept
Instances include:

File: DNSRegistrar.sol [Line 204](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L204)

File: BytesUtils.sol [Line 183](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L183)

File: DNSSECImpl.sol [Line 127](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L127)

File: DNSSECImpl.sol [Line 173](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L173)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.


### 3- Constant redefined elsewhere :

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. .

#### Risk : Non critical

#### Proof of Concept

Instances include:

File: DNSClaimChecker.sol [Line 16-17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17)

File: OffchainDNSResolver.sol [Line 29-30](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L29-L30)

```
uint16 constant CLASS_INET = 1;
uint16 constant TYPE_TXT = 16;
```

### 4- Unused imports :

Contracts or libraries that are imported and not used should be removed.

#### Risk : Non critical

#### Proof of Concept
Instances include:

File: DNSClaimChecker.sol [Line 4](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L4)
```
import "../dnssec-oracle/DNSSEC.sol";
```