### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] |Gas optimization can be achieved by updating the logic in the ``equals()`` function, which is used extensively in the project| 1 |
| [G-02] |Unused ``OwnerRecord`` struct can be removed| 1 |
| [G-03] |Avoid using ``state variable`` in emit |2 |
| [G-04] |Missing `zero-address` check in `constructor` | 2 |
| [G-05] |Use nested if and, avoid multiple check combinations |2|
| [G-06] |Use ``assembly`` to write _address storage values_  | 2 |
| [G-07] |Ternary operation is cheaper than if-else statement | 1 |

Total 7 issues


### [G-01] Gas optimization can be achieved by updating the logic in the ``equals()`` function, which is used extensively in the project

BytesUtils.sol file has equals() function and this function is compare to 2 difference bytes for equals.

The logic of the function is based on the following code, and the code simultaneously checks the length of 2 different bytes and checks whether their characters are the same, that is, the 2 logics are the control main logic at the same time.

```solidity
self.length == other.length &&
            equals(self, 0, other, 0, self.length);

```

If we separate these two logics and check the length first, a high gas optimization will be achieved. Considering that this function is used extensively in the project, it will be a serious gas gain for the project.


```solidity
contracts\dnssec-oracle\BytesUtils.sol:
  163       */
  164:     function equals(
  165:         bytes memory self,
  166:         bytes memory other
  167:     ) internal pure returns (bool) {
  168:         return
  169:             self.length == other.length &&
  170:             equals(self, 0, other, 0, self.length);
  171:     }
  172: 

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L164-LL171



1- x.length == y.length  && x.details == y.details

2- if (x.length =! y.length) revert
       else x.details == y.details


In terms of gas optimization, option 2 is be more efficient because it avoids unnecessary comparisons when the lengths of x and y are not equal.

In option 1, the function compares the lengths of x and y using the length property, which requires iterating over the entire length of each byte array. This expensive in terms of gas when the byte arrays are large.

In option 2, the function first checks whether the lengths of x and y are equal using the != operator. If the lengths are not equal, the function immediately reverts, which is a low-cost operation in terms of gas. If the lengths are equal, the function then compares the contents of x and y. This approach avoids the expensive length comparison when the byte arrays are not equal in length.


### [G-02] Unused ``OwnerRecord`` struct can be removed

When in-scope and out-of-scope contracts are examined (including test files), it has been noticed that the ``OwnerRecord`` struct is not used.

Removing this struct will reduce the cost of deploy, so gas savings will be provided.


```solidity
contracts\dnsregistrar\DNSRegistrar.sol:

  40:     struct OwnerRecord {
  41:         bytes name;
  42:         address owner;
  43:         address resolver;
  44:         uint64 ttl;
  45:     }

```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L40-L45


### [G-03] Avoid using ``state variable`` in emit

Reading the values published below from memory instead of storage saves gas.

2 results - 1 file:
```solidity
contracts\dnsregistrar\DNSRegistrar.sol:

  55:     constructor(
  56:         address _previousRegistrar,
  57:         address _resolver,
  58:         DNSSEC _dnssec,
  59:         PublicSuffixList _suffixes,
  60:         ENS _ens
  61:     ) {
  62:         previousRegistrar = _previousRegistrar;
  63:         resolver = _resolver;
  64:         oracle = _dnssec;
  65:         suffixes = _suffixes;
  66:         emit NewPublicSuffixList(address(suffixes));
  67:         ens = _ens;
  68:     }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L66


```diff
contracts\dnsregistrar\DNSRegistrar.sol:

  55:     constructor(
  56:         address _previousRegistrar,
  57:         address _resolver,
  58:         DNSSEC _dnssec,
  59:         PublicSuffixList _suffixes,
  60:         ENS _ens
  61:     ) {
  62:         previousRegistrar = _previousRegistrar;
  63:         resolver = _resolver;
  64:         oracle = _dnssec;
  65:         suffixes = _suffixes;
- 66:         emit NewPublicSuffixList(address(suffixes));
+ 66:         emit NewPublicSuffixList(address(_suffixes));
  67:         ens = _ens;
  68:     }

```

```solidity
contracts\dnsregistrar\DNSRegistrar.sol:
  79  
  80:     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
  81:         suffixes = _suffixes;
  82:         emit NewPublicSuffixList(address(suffixes));
  83:     }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82

```diff
contracts\dnsregistrar\DNSRegistrar.sol:
  79  
  80:     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
  81:         suffixes = _suffixes;
- 82:         emit NewPublicSuffixList(address(suffixes));
+ 82:         emit NewPublicSuffixList(address(_suffixes));
  83:     }

```

### [G-04] Missing `zero-address` check in `constructor`

While immutable addresses are assigned in the constructor, there is no zero address control. If these immutable state variables are initialized with a possible value of "0", it means that the contract must be redistributed. This possibility means gas consumption. 

Because of the EVM design, zero value control is the most error-prone value control, as zero value is assigned in case of no value input.

Also, since the constant value will be changed once, adding a zero value control will not cause high gas consumption.

2 reults 2 files:
```solidity
contracts\dnsregistrar\DNSRegistrar.sol:
 
  55:     constructor(
  56:         address _previousRegistrar,
  57:         address _resolver,
  58:         DNSSEC _dnssec,
  59:         PublicSuffixList _suffixes,
  60:         ENS _ens
  61:     ) {
  62:         previousRegistrar = _previousRegistrar;
  63:         resolver = _resolver;
  64:         oracle = _dnssec;
  65:         suffixes = _suffixes;
  66:         emit NewPublicSuffixList(address(suffixes));
  67:         ens = _ens;
  68:     }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L55-L68


```solidity
contracts\dnsregistrar\OffchainDNSResolver.sol:
  
  43:     constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
  44:         ens = _ens;
  45:         oracle = _oracle;
  46:         gatewayURL = _gatewayURL;
  47:     }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47


**Recommendation step:**
It is recommended to perform a zero value check for critical value assignments.

Add zero check for immutable values when assigning values in critical constructor.


### [G-05] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

2 results - 2 files:

```solidity
contracts\dnsregistrar\OffchainDNSResolver.sol:

  178:         if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178


```solidity
contracts\dnssec-oracle\DNSSECImpl.sol:

  312:         if (dnskey.flags & DNSKEY_FLAG_ZONEKEY == 0) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L312


```solidity
contracts\dnssec-oracle\algorithms\EllipticCurve.sol:

  128:         if (x0 == 0 && y0 == 0) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128


**Recomendation Code:**
```diff
contracts\dnssec-oracle\algorithms\EllipticCurve.sol:

- 128:         if (x0 == 0 && y0 == 0) {
+              if (x0 == 0) {
+                  if (y0 == 0) {
+                  }                                   
+              }
                                 
```

### [G-06] Use ``assembly`` to write _address storage values_ 

2 results 2 files:
```solidity
contracts\dnsregistrar\DNSRegistrar.sol:
 
  55:     constructor(
  65:         suffixes = _suffixes;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L65


```solidity
contracts\dnsregistrar\DNSRegistrar.sol:
 
  80:     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
  81:         suffixes = _suffixes;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L81


```diff
contracts\dnsregistrar\DNSRegistrar.sol:
 
  80:     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
- 81:         suffixes = _suffixes;
+                  assembly {                      
+                      sstore(suffixes.slot, _suffixes)
+                  }       

```

### [G-07] Ternary operation is cheaper than if-else statement

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

1 result - 1 file:
```solidity
contracts\dnssec-oracle\algorithms\EllipticCurve.sol:
  232  
  233:         if (u0 == u1) {
  234:             if (t0 == t1) {
  235:                 return twiceProj(x0, y0, z0);
  236:             } else {
  237:                 return zeroProj();
  238:             }
  239:         }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L233-L239

```diff
contracts\dnssec-oracle\algorithms\EllipticCurve.sol:
  232  
  233:   if (u0 == u1) {
- 234:     if (t0 == t1) {
- 235:         return twiceProj(x0, y0, z0);
- 236:     } else {
- 237:         return zeroProj();
- 238:     }
+          return (t0 == t1) ? twiceProj(x0, y0, z0) : zeroProj();
  239:   }

```