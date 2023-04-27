|     |                                                Issues                                                | Instances |
| :-- | :-------------------------------------------------------------------------------------------------- | --------: |
| 01  |                                 Use Custom Errors instead of String.                                 |         5 |
| 02  |                                  Event is missing `indexed` fields.                                  |         2 |
| 03  |                      Missing License (e.g, `// SPDX-License-Identifier: MIT`).                       |        11 |
| 04  |                         Inconsistent method of specifying a floating pragma.                         |         2 |
| 05  |                               One line `if` statements. Best Practice                                |        48 |
| 06  | According to the syntax rules, use => `mapping (` instead of => `mapping(` using spaces as keyword.  |         3 |
| 07  |                             Shorthand way to write if / else statement.                              |         3 |
| 08  |                                        NatSpec is incomplete.                                        |        24 |
| 09  |                                       Missing NatSpec in file.                                       |         5 |
| 10  |                              Some lines use `// x` and some use `//x`.                               |         2 |
| 11  |                          `pragma experimental ABIEncoderV2` is deprecated.                           |         1 |
| 12  |             Not using the named return variables anywhere in the function is confusing.              |         7 |
| 13  |      Adding a return statement when the function defines a named return variable, is redundant.      |         3 |
| 14  | Missing checks for `address(0x0)` (zero-address) when assigning values to `address` state variables. |         2 |
| 15  |                          Don't use of `Block.Timestamp` can be manipulated.                          |         1 |

## [01] Use Custom Errors instead of String.

_Instead of using strings for error messages (e.g., `require(msg.sender == owner, “unauthorized”)`), you can use custom errors to reduce both deployment and runtime gas costs. In addition, they are very convenient as you can easily pass dynamic information to them._

There are 5 instances:

[contracts/dnssec-oracle/RRUtils.sol#L381](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381)

```js
require(data.length <= 8192, 'Long keys not permitted');
```

[contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L33](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L33)

```js
require(data.length == 64, 'Invalid p256 signature length');
```

[contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L40](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L40)

```js
require(data.length == 68, 'Invalid p256 key length');
```

[contracts/dnssec-oracle/digests/SHA1Digest.sol#L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17)

```js
require(hash.length == 20, 'Invalid sha1 hash length');
```

[contracts/dnssec-oracle/digests/SHA256Digest.sol#L16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L16)

```js
require(hash.length == 32, 'Invalid sha256 hash length');
```

### Recommended Mitigation Steps:

-   Use custom errors, example:

```java
error Unauthorized(address caller);

function add(uint256 _amount) public {
    if (msg.sender != owner)
        revert Unauthorized(msg.sender);

    total += _amount;
}
```

## [02] Event is missing `indexed` fields.

_Each `event` should use three `indexed` fields if there are three or more fields._

_Index event fields make the field more quickly accessible to off-chain tools that parse events._

Note: Using the `indexed` keyword for value types such as `uint`, `bool`, and `address` saves gas costs. However, this is only the case for value types, whereas indexing `bytes` and `strings` are more expensive than their unindexed version.

There are 2 instances:

[contracts/dnsregistrar/DNSRegistrar.sol#L47-L52](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L47-L52)

```java
event Claim(
      bytes32 indexed node,
      address indexed owner,
      bytes dnsname,
      uint32 inception
  );
```

[contracts/dnsregistrar/DNSRegistrar.sol#53](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L53)

```java
event NewPublicSuffixList(address suffixes);
```

### Recommended Mitigation Steps:

-   Add `indexed` to the missing fields

## [03] Missing License (e.g, `// SPDX-License-Identifier: MIT`).

There are 11 instances:

[contracts/dnssec-oracle/BytesUtils.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L1)

[contracts/dnssec-oracle/RRUtils.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L1)

[contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1)

[contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1)

[contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1)

[contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1)

[contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1)

[contracts/dnssec-oracle/digests/SHA1Digest.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L1)

[contracts/dnssec-oracle/digests/SHA256Digest.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L1)

[contracts/dnssec-oracle/SHA1.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L1)

### Recommended Mitigation Steps:

-   Add in the first line: `// SPDX-License-Identifier: MIT`

## [04] Inconsistent method of specifying a floating pragma.

_Some files use `>=`, some use `^`._

_Note that using `>=` without also specifying `<=` will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so `^` should be preferred regardless of the instance counts._

There are 2 instances:

[contracts/wrapper/BytesUtils.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/wrapper/BytesUtils.sol#L2)

```java
pragma solidity ~0.8.17;
```

[contracts/dnssec-oracle/SHA1.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L1)

```java
pragma solidity >=0.8.4;
```

### Recommended Mitigation Steps:

-   Use `^`

## [05] One line `if` statements. Best Practice

_It is possible to write the code that should be run `if` the condition evaluates to true in the same line of the `if` statement (instead of inside the `if` block)._

1. Increases readability
2. Shortens the overall SLOC

There are 48 instances:

[contracts/dnsregistrar/DNSClaimChecker.sol#L38-L40](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L38-L40)

```java
if (found) {
    return (addr, true);
  }
```

e.g, change to:

```java
if (found) return (addr, true);
```

[contracts/dnsregistrar/OffchainDNSResolver.sol#L144-L146](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L144-L146)

```java
if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {
            return (address(0), "");
        }
```

[contracts/dnsregistrar/OffchainDNSResolver.sol#L183-L185](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L183-L185)

```java
if (valid) {
    return ret;
  }
```

[contracts/dnsregistrar/OffchainDNSResolver.sol#L197-L199](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197-L199)

```java
if (resolver == address(0)) {
    return address(0);
  }
```

[contracts/dnsregistrar/RecordParser.sol#L24-L26](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L24-L26)

```java
if (separator == type(uint256).max) {
      return ("", "", type(uint256).max);
  }
```

[contracts/dnsregistrar/RecordParser.sol#L33-L35](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L33-L35)

```java
if (terminator == type(uint256).max) {
            terminator = input.length;
        }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L111-L113](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L111-L113)

```java
 if (msg.sender != owner) {
      revert PermissionDenied(msg.sender, owner);
    }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L116-L118](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L116-L118)

```java
if (resolver == address(0)) {
    revert PreconditionNotMet();
 }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L159-L161](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L159-L161)

```java
if (!found) {
     revert NoOwnerRecordFound();
  }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L152-L154](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152-L154)

```java
if (!RRUtils.serialNumberGte(inception, inceptions[node])) {
      revert StaleProof();
    }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L168-L170](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L168-L170)

```java
if (!suffixes.isPublicSuffix(domain)) {
    revert InvalidPublicSuffix(domain);
  }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L179-L181](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L179-L181)

```java
if (len == 0) {
    return bytes32(0);
  }
```

[contracts/dnsregistrar/DNSRegistrar.sol#L201-L203](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L201-L203)

```java
} else if (owner != address(this)) {
          revert PreconditionNotMet();
}
```

[contracts/dnssec-oracle/BytesUtils.sol#L60-L62](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L60-L62)

```java
if (offset + len > self.length) {
      revert OffsetOutOfBoundsError(offset + len, self.length);
   }
```

[contracts/dnssec-oracle/BytesUtils.sol#L63-L65](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L63-L65)

```java
if (otheroffset + otherlen > other.length) {
     revert OffsetOutOfBoundsError(otheroffset + otherlen, other.length);
  }
```

[contracts/dnssec-oracle/BytesUtils.sol#L346-L348](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L346-L348)

```java
if (i == len - 1) {
        break;
  }
```

[contracts/dnssec-oracle/BytesUtils.sol#L372-L374](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L372-L374)

```java
} else {
     revert();
    }
```

[contracts/dnssec-oracle/BytesUtils.sol#L394-L396](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L394-L396)

```java
if (self[idx] == needle) {
               return idx;
           }
```

[contracts/dnssec-oracle/RRUtils.sol#L28-L30](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L28-L30)

```java
if (labelLen == 0) {
            break;
        }
```

[contracts/dnssec-oracle/RRUtils.sol#L64-L66](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L64-L66)

```java
if (labelLen == 0) {
                break;
            }
```

[contracts/dnssec-oracle/RRUtils.sol#L160-L162](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L160-L162)

```java
if (iter.offset >= iter.data.length) {
            return;
        }
```

[contracts/dnssec-oracle/RRUtils.sol#L279-L281](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L279-L281)

```java
if (self.equals(other)) {
            return 0;
        }
```

[contracts/dnssec-oracle/RRUtils.sol#L312-L317](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L312-L317)

```java
if (off == 0) {
            return -1;
        }
if (otheroff == 0) {
            return 1;
        }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128-L130](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128-L130)

```java
if (x0 == 0 && y0 == 0) {
            return true;
        }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L138-L140](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L138-L140)

```java
if (0 == x || x == p || 0 == y || y == p) {
            return false;
        }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L145-L150](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L145-L150)

```java
if (a != 0) {
            RHS = addmod(RHS, mulmod(x, a, p), p); // x^3 + a*x
        }
if (b != 0) {
            RHS = addmod(RHS, b, p); // x^3 + a*x + b
        }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L169-L171](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L169-L171)

```java
if (isZeroCurve(x0, y0)) {
            return zeroProj();
        }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L233-L239](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L233-L239)

```java
if (u0 == u1) {
      if (t0 == t1) {
          return twiceProj(x0, y0, z0);
       } else {
          return zeroProj();
            }
      }
```

change to:

```java
if (u0 == u1) {
      if (t0 == t1) return twiceProj(x0, y0, z0);
      return zeroProj();
      }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L392-L398](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L392-L398)

```java
if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
            // || rs[1] > lowSmax)
        return false;
}
if (!isOnCurve(Q[0], Q[1])) {
        return false;
}
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L410-L413](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L410-L413)

```java
if (P[2] == 0) {
      return false;
   }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L192-L194](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L192-L194)

```java
if (iter.class != DNSCLASS_IN) {
            revert InvalidClass(iter.class);
    }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L243-L245](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L243-L245)

```java
} else {
    revert InvalidProofType(proofRR.dnstype);
 }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L271-L273](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L271-L273)

```java
if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
                return;
            }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L294-L296](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L294-L296)

```java
if (dnskey.protocol != 3) {
            return false;
        }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L301-L303](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L301-L303)

```java
if (dnskey.algorithm != rrset.algorithm) {
            return false;
        }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L305-L307](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L305-L307)

```java
if (computedkeytag != rrset.keytag) {
            return false;
        }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L312-L314](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L312-L314)

```java
if (dnskey.flags & DNSKEY_FLAG_ZONEKEY == 0) {
            return false;
        }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L317-L319](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317-L319)

```java
if (address(algorithm) == address(0)) {
            return false;
        }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L382-L384](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L382-L384)

```java
if (!proofName.equals(keyname)) {
                revert ProofNameMismatch(keyname, proofName);
            }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L390-L395](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L390-L395)

```java
if (ds.keytag != keytag) {
                continue;
            }
if (ds.algorithm != dnskey.algorithm) {
                continue;
            }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L401-L403](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L401-L403)

```java
if (verifyDSHash(ds.digestType, buf.buf, ds.digest)) {
                return true;
            }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L420-L422](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420-L422)

```java
if (address(digests[digesttype]) == address(0)) {
            return false;
        }
```

[contracts/utils/NameEncoder.sol#L39-L41](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L39-L41)

```java
if (i == 0) {
        break;
  }
```

[contracts/utils/HexUtils.sol#L19-L21](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L19-L21)

```java
if gt(lastIdx, mload(str)) {
                revert(0, 0)
            }
```

### Recommended Mitigation Steps:

-   This is accomplished with a if statement on a single line, without opening a block with curly brackets.

## [06] According to the syntax rules, use => `mapping (` instead of => `mapping(` using spaces as keyword.

There are 3 instances:

[contracts/dnsregistrar/DNSRegistrar.sol#L32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L32)

```java
mapping(bytes32 => uint32) public inceptions;
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46)

```java
mapping(uint8 => Algorithm) public algorithms;
mapping(uint8 => Digest) public digests;
```

### Recommended Mitigation Steps:

-   Change to:

```ts
mapping ( bytes32 => uint32 ) public inceptions;

mapping ( uint8 => Algorithm ) public algorithms;
mapping ( uint8 => Digest ) public digests;
```

## [07] Shorthand way to write if / else statement.

_The normal `if` / `else` statement can be refactored in a shorthand way to write it:_

1. Increases readability
2. Shortens the overall SLOC

There are 3 instances:

[contracts/dnsregistrar/OffchainDNSResolver.sol#L123-L127](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L123-L127)

```java
        if (ok) {
            return ret;
        } else {
            revert CouldNotResolve(name);
        }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238)

```java
        if (t0 == t1) {
            return twiceProj(x0, y0, z0);
        } else {
            return zeroProj();
        }
```

[contracts/dnssec-oracle/BytesUtils.sol#L86-L91](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L86-L91)

```java
        uint256 mask;
        if (shortest - idx >= 32) {
            mask = type(uint256).max;
        } else {
            mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
        }
```

### Recommended Mitigation Steps:

-   Change to:

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238)

```java
 ok ? return ret; : revert CouldNotResolve(name);
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238)

```ts
 return t0 == t1 ? twiceProj(x0, y0, z0); : zeroProj();
```

[contracts/dnssec-oracle/BytesUtils.sol#L86-L91](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L86-L91)

```java
uint256 mask = shortest - idx >= 32 ? type(uint256).max; : ~(2 ** (8 * (idx + 32 - shortest)) - 1);
```

## [08] NatSpec is incomplete.

There are 24 instances:

[contracts/dnsregistrar/OffchainDNSResolver.sol#L203#L213](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L203#L213)

[contracts/dnsregistrar/RecordParser.sol#L9-L22](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L9-L22)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L37-L40](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L37-L40)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L62-L68](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L62-L68)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L74-L82](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L74-L82)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L89-L96](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L89-L96)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L121-L127](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L121-L127)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L134-L137](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L134-L137)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L155-L163](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L155-L163)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L204-L215](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L204-L215)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L244-L253](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L244-L253)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L283-L291](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L283-L291)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L299-L305](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L299-L305)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L313-L320](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L313-L320)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L332-L339](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L332-L339)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L374-L379](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L374-L379)

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L383-L390](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L383-L390)

[contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L4-L11](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L4-L11)

[contracts/dnssec-oracle/DNSSECImpl.sol#L130-L144](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L130-L144)

[contracts/dnssec-oracle/DNSSECImpl.sol#L176-L184](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L176-L184)

[contracts/dnssec-oracle/DNSSECImpl.sol#L216-L229](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L216-L229)

[contracts/dnssec-oracle/DNSSECImpl.sol#L278-L290](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L278-L290)

[contracts/utils/HexUtils.sol#L5-L15](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L5-L15)

[contracts/utils/HexUtils.sol#L62-L72](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L62-L72)

### Recommended Mitigation Steps:

-   Add `@param`/`@return` missing

## [09] Missing NatSpec in file.

There are 5 instances:

[contracts/dnsregistrar/DNSClaimChecker.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol)
[contracts/dnsregistrar/OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)
[contracts/dnsregistrar/DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)
[contracts/dnssec-oracle/RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)
[contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol)
[contracts/dnssec-oracle/SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)
[contracts/utils/NameEncoder.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol)

### Recommended Mitigation Steps:

-   Add NatSpec

## [10] Some lines use `// x` and some use `//x`.

_The instances below point out the usages that don’t follow the majority, within each file:_

There are 2 instances:

[contracts/dnsregistrar/DNSClaimChecker.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L1)

[contracts/dnsregistrar/DNSRegistrar.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L1)

## [11] `pragma experimental ABIEncoderV2` is deprecated.

See [`Silent Changes of the Semantics`](https://github.com/ethereum/solidity/blob/69411436139acf5dbcfc5828446f18b9fcfee32c/docs/080-breaking-changes.rst#silent-changes-of-the-semantics).

There an instance:

[contracts/dnssec-oracle/DNSSECImpl.sol#L3](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L3)

```js
pragma experimental ABIEncoderV2;
```

[]()

### Recommended Mitigation Steps:

-   Use `pragma abicoder v2` instead

## [12] Not using the named return variables anywhere in the function is confusing.

The are 7 instances:

[contracts/dnsregistrar/DNSRegistrar.sol#L166-L172](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L166-L172)

```java
 function enableNode(bytes memory domain) public returns (bytes32 node) {
        // Name must be in the public suffix list.
        if (!suffixes.isPublicSuffix(domain)) {
            revert InvalidPublicSuffix(domain);
        }
        return _enableNode(domain, 0);
    }
```

[contracts/dnssec-oracle/RRUtils.sol#L41-L47](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L41-L47)

```java
    function readName(
        bytes memory self,
        uint256 offset
    ) internal pure returns (bytes memory ret) {
        uint256 len = nameLength(self, offset);
        return self.substring(offset, len);
    }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L106-L112](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L106-L112)

```java
    function zeroProj()
        internal
        pure
        returns (uint256 x, uint256 y, uint256 z)
    {
        return (0, 1, 0);
    }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L117-L119](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L117-L119)

```java
    function zeroAffine() internal pure returns (uint256 x, uint256 y) {
        return (0, 0);
    }
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L124-L132](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L124-L132)

```java
    function isZeroCurve(
        uint256 x0,
        uint256 y0
    ) internal pure returns (bool isZero) {
        if (x0 == 0 && y0 == 0) {
            return true;
        }
        return false;
    }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L87-L97](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L87-L97)

```java
    function verifyRRSet(
        RRSetWithSignature[] memory input
    )
        external
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)
    {
        return verifyRRSet(input, block.timestamp);
    }
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L107-L128](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L107-L128)

```java
    function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
        public
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)
    {
        bytes memory proof = anchors;
        for (uint256 i = 0; i < input.length; i++) {
            RRUtils.SignedSet memory rrset = validateSignedSet(
                input[i],
                proof,
                now
            );
            proof = rrset.data;
            inception = rrset.inception;
        }
        return (proof, inception);
    }
```

### Recommended Mitigation Steps:

-   Consider changing the variable to be an unnamed one

## [13] Adding a return statement when the function defines a named return variable, is redundant.

The are 3 instances:

[contracts/dnsregistrar/DNSRegistrar.sol#L174-L206](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L174-L206)

```java
177      ) internal returns (bytes32 node) {
185      node = keccak256(abi.encodePacked(parentNode, label));
204      return node; // line 185
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L140-L174](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L140-L174)

```java
144       ) internal view returns (RRUtils.SignedSet memory rrset) {
145       rrset = input.rrset.readSignedSet();
152       rrset.name = name;
173       return rrset; // line 145, 152
```

[contracts/utils/NameEncoder.sol#L9-L51](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L9-L51)

```java
11        ) internal pure returns (bytes memory dnsName, bytes32 node) {
45        node = keccak256( // correct
46          abi.encodePacked(node, bytesName.keccak(0, labelLength))
47        );
49        dnsName[0] = bytes1(labelLength);
50        return (dnsName, node); // line 45, 49
```

### Recommended Mitigation Steps:

-   See the comments

## [14] Missing checks for `address(0x0)` (zero-address) when assigning values to `address` state variables.

_Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly._

There are 2 instances of this issue:

[contracts/dnsregistrar/DNSRegistrar.sol#L62-L63](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L63)

```java
 previousRegistrar = _previousRegistrar;
 resolver = _resolver;
```

### Recommended Mitigation Steps:

-   Consider adding zero-address checks: e.g,
    `require(newAddr != address(0));`

## [15] Don't use of `Block.Timestamp` can be manipulated.

[Docs](https://solidity-by-example.org/hacks/block-timestamp-manipulation/)
_Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts._

There an instance:

[contracts/dnssec-oracle/DNSSECImpl.sol#L96](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L96)

```java
return verifyRRSet(input, block.timestamp);
```

### Recommended Mitigation Steps:

-   Block timestamps should not be used for entropy or generating random numbers —i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
-   Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.