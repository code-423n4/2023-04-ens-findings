|      |                             Issues                             | Instances |
| :--- | :------------------------------------------------------------: | --------: |
| G-01 | Using `private` rather than `public` for constants, saves gas. |        32 |
| G-02 |     Use nested if and, avoid multiple check combinations.      |         1 |

## [G-01] Using `private` rather than `public` for constants, saves gas.

_If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table._

There are 32 instances:

[contracts/dnsregistrar/OffchainDNSResolver.sol#L29-L30](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L29-L30)

```java
uint16 constant CLASS_INET = 1;
uint16 constant TYPE_TXT = 16;
```

[contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17)

```java
uint16 constant CLASS_INET = 1;
uint16 constant TYPE_TXT = 16;
```

[contracts/dnssec-oracle/BytesUtils.sol#L322](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322)

```java
bytes constant base32HexTable =
        hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";
```

[contracts/dnssec-oracle/RRUtils.sol#L72-L79](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72-L79)

```java
uint256 constant RRSIG_TYPE = 0;
uint256 constant RRSIG_ALGORITHM = 2;
uint256 constant RRSIG_LABELS = 3;
uint256 constant RRSIG_TTL = 4;
uint256 constant RRSIG_EXPIRATION = 8;
uint256 constant RRSIG_INCEPTION = 12;
uint256 constant RRSIG_KEY_TAG = 16;
uint256 constant RRSIG_SIGNER_NAME = 18;
```

[contracts/dnssec-oracle/RRUtils.sol#L210-L213](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L210-L213)

```java
uint256 constant DNSKEY_FLAGS = 0;
uint256 constant DNSKEY_PROTOCOL = 2;
uint256 constant DNSKEY_ALGORITHM = 3;
uint256 constant DNSKEY_PUBKEY = 4;
```

[contracts/dnssec-oracle/RRUtils.sol#L236-L239](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L236-L239)

```java
uint256 constant DS_KEY_TAG = 0;
uint256 constant DS_ALGORITHM = 2;
uint256 constant DS_DIGEST_TYPE = 3;
uint256 constant DS_DIGEST = 4;
```

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35)

```java
uint256 constant a =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
uint256 constant b =
        0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
uint256 constant gx =
        0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
uint256 constant gy =
        0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
uint256 constant p =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
uint256 constant n =
        0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;
uint256 constant lowSmax =
        0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;
```

[contracts/dnssec-oracle/DNSSECImpl.sol#L27-L32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27-L32)

```java
uint16 constant DNSCLASS_IN = 1;
uint16 constant DNSTYPE_DS = 43;
uint16 constant DNSTYPE_DNSKEY = 48;
uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
```

### Recommended Mitigation Steps:

-   Add `private` to constants

## [G-02] Use nested if and, avoid multiple check combinations.

_Using nested is cheaper than using `&&` multiple check combinations. _

There is an instance:

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128-L130](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128-L130)

```java
if (x0 == 0 && y0 == 0) {
            return true;
        }
```
### Recommended Mitigation Steps:
change to:

```java
if (x0 == 0) {
  if(y0 == 0) return true;
}
```