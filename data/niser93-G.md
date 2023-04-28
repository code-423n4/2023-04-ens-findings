# GAS REPORT

| GAS_N°| Title  | Total gas saving|
|:--------:|:------:|:---------------:|
| [GAS-01](#gas-01---use-alternative-formulation-in-order-to-avoid-not-using-and-instead-of-or-or-vice-versa)  | Use alternative formulation in order to avoid NOT using AND instead of OR or vice versa      | 4019               |
| [GAS-02](#gas-02---using--instead-of--and--instead-of)  | Using >= instead of > and <= instead of <      | 4996               |
| [GAS-03](#gas-03---avoid-to-define-variable-which-are-used-once)  | Avoid to define variable which are used once      | 31222               |
| [GAS-04](#gas-04---avoid-to-modify-variable-which-are-used-once)  | Avoid to modify variable which are used once      | 222               |
| [GAS-05](#gas-05---return-result-of-boolean-formula-without-using-ifelse)  | Return result of boolean formula without using if/else      | 3882               |
| [GAS-06](#gas-06---i-costs-less-gas-than-i---ii---too)  | ++i Costs Less Gas Than i++ (--i/i-- too)      | 2198               |
| [GAS-07](#gas-07---using-do-while-loop-and-avoid-break-condition-and-whiletrue)  | Using do-while loop and avoid break condition and while(true)      | 11779               |
|||
|-| All optimizations applied|56641

## Optimization details
```
Contract             Method                     Min     Max     Avg
-------------------  -------------------------  ------  ------  ------
DNSRegistrar         proveAndClaim              -746    -819    -765
DNSRegistrar         proveAndClaimWithResolver  -758    -758    -758
DNSRegistrar                                    -18765  -18765  -18765
DNSSECImpl                                      -11450  -11450  -11450
OffchainDNSResolver                             -11186  -11186  -11186
OwnedResolver                                   0       0       -828
P256SHA256Algorithm                             0       0       -4314
PublicResolver                                  -840    -840    -840
SHA1Digest                                      0       0       -2136
TestBytesUtils                                  0       0       -228
TestHexUtils                                    0       0       -216
TestRRUtils                                     0       0       -5155

Total                                           -43745  -43818  -56641
```

## GAS-01 - Use alternative formulation in order to avoid NOT using AND instead of OR or vice versa

### Founded in [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

* [OffchainDNSResolver.sol#L86-L92](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L92)

```
if (
    !rrname.equals(name) ||
    iter.class != CLASS_INET ||
    iter.dnstype != TYPE_TXT
) {
    continue;
}
```

* [OffchainDNSResolver.sol#L143-L146](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L143-L146)

```
// Must start with the magic word
if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {
    return (address(0), "");
}
```

### Founded in [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

* [RRUtils.sol#L304](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304)

```
while (counts > 0 && !self.equals(off, other, otheroff)) {
```

### Founded in [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)

* [DNSSECImpl.sol#L201-L206](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L201-L206)

```
if (
    name.length != iter.data.nameLength(iter.offset) ||
    !name.equals(0, iter.data, iter.offset, name.length)
) {
    revert InvalidRRSet();
}
```

### Description
It's possible to change boolean formula in order to use AND instead of OR.
This should save gas.

#### [OffchainDNSResolver.sol#L86-L92](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L92)
```diff
if (
-    !rrname.equals(name) ||
-    iter.class != CLASS_INET ||
-    iter.dnstype != TYPE_TXT
+    !(rrname.equals(name) &&
+    iter.class == CLASS_INET &&
+    iter.dnstype == TYPE_TXT)
) {
    continue;
}
```

#### [OffchainDNSResolver.sol#L143-L146](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L143-L146)

```diff
// Must start with the magic word
- if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {
+ if (!(txt.length >= 5 && txt.equals(0, "ENS1 ", 0, 5))) {
    return (address(0), "");
}
```

#### [RRUtils.sol#L304](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304)

```diff
- while (counts > 0 && !self.equals(off, other, otheroff)) {
+ while (!(counts == 0 || self.equals(off, other, otheroff))) {
```

We can do that because counts is a uint, so counts > 0 is same of counts != 0.

#### [DNSSECImpl.sol#L201-L206](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L201-L206)

```diff
if (
-   name.length != iter.data.nameLength(iter.offset) ||
-   !name.equals(0, iter.data, iter.offset, name.length)
+   !(name.length == iter.data.nameLength(iter.offset) &&
+    name.equals(0, iter.data, iter.offset, name.length))
) {
    revert InvalidRRSet();
}
```

### Proof of concept

In general

#### Two variables

```
(a || b) = !(!(a || b)) = !(!a && !b)
```

| a  | b  | (a or b)  | !a and !b  | !(!a and !b)|
|:--:|:--:|:---------:|:----------:|:------------:|
| 0  | 0  | 0         | 1          | 0            |
| 0  | 1  | 1         | 0          | 1            |
| 1  | 0  | 1         | 0          | 1            |
| 1  | 1  | 1         | 0          | 1            |


#### Three variables

```
(a || b || c) = !(!(a || b || c)) = !(!a && !b && !c)
```

| a  | b  | c | (a or b or c)  | !a and !b and !c  | !(!a and !b and !c)|
|:--:|:--:|:-:|:--------------:|:-----------------:|:------------------:|
| 0  | 0  | 0 | 0              | 1                 | 0                  |
| 0  | 0  | 1 | 1              | 0                 | 1                  |
| 0  | 1  | 0 | 1              | 0                 | 1                  |
| 0  | 1  | 1 | 1              | 0                 | 1                  |
| 1  | 0  | 0 | 1              | 0                 | 1                  |
| 1  | 0  | 1 | 1              | 0                 | 1                  |
| 1  | 1  | 0 | 1              | 0                 | 1                  |
| 1  | 1  | 1 | 1              | 0                 | 1                  |



### Test with customized contracts

Using Remix IDE with optimization at 10k runs.

Original
```
pragma solidity ^0.8.0;

contract Attempt{

    function tryGas(
        address idx,
        address class,
        address dnstype
    ) public returns(uint256){
        if (
            !(idx == address(0x0)) ||
            class != address(0x0) ||
            dnstype != address(0x0)
        ) {
            return 0;
        }

        return 1;
    }
}
```

New
```
pragma solidity ^0.8.0;

contract Attempt{
    function tryGas(
        address idx,
        address class,
        address dnstype
    ) public returns(uint256){
        if (
            !(idx == address(0x0) && class == address(0x0) && dnstype == address(0x0))
        ) {
            return 0;
        }

        return 1;
    }
}
```


| idx  | class  | dnstype |original return | new return  | original execution cost|new execution cost|
|:----:|:------:|:-------:|:--------------:|:-----------:|:----------------------:|:--------------------:|
| 0x0  | 0x0    | 0x0     | 1              | 1           | 618        | 612         |
| 0x0  | 0x0    | !0x0    | 0              | 0           | 628        | 622         |
| 0x0  | !0x0   | 0x0     | 0              | 0           | 611        | 608         |
| 0x0  | !0x0   | !0x0    | 0              | 0           | 611        | 608         |
| !0x0 | 0x0    | 0x0     | 0              | 0           | 594        | 594         |
| !0x0 | 0x0    | !0x0    | 0              | 0           | 594        | 594         |
| !0x0 | !0x0   | 0x0     | 0              | 0           | 594        | 594         |
| !0x0 | !0x0   | !0x0    | 0              | 0           | 594        | 594         |


### Yarn profiles

#### [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| OffchainDNSResolver |        | 1459188 | 1459200 | 1459197 | 4.9 %      |           |
+| OffchainDNSResolver |        | 1458528 | 1458540 | 1458537 | 4.9 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -660 |    -660 |    -660 |            |           |

The average saving gas is 660.
```

#### [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)

```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSRegistrar        |        | 1923407 | 1923659 | 1923519 | 6.4 %      |           |
+| DNSRegistrar        |        | 1922123 | 1922375 | 1922235 | 6.4 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |   -1284 |   -1284 |   -1284 |            |           |

The average saving gas is 1284.
```

```diff
 | Contract        | Method        | Min     | Max     | AVG     | # calls | eur (avg) |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
-| DNSRegistrar    |proveAndClaim  |  198110 |  308834 |  269704 |   7     |           |
+| DNSRegistrar    |proveAndClaim  |  198096 |  308820 |  269690 |   7     |           |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
 |                 |               |     -14 |     -14 |     -14 |         |           |

The average saving gas is 14.
```

```diff
 | Contract    | Method                  | Min    | Max    | AVG    |# calls| eur (avg)|
 |-------------|-------------------------|--------|--------|--------|-------|----------|
-| DNSRegistrar|proveAndClaimWithResolver| 301464 | 339399 | 320432 |  2    |          |
+| DNSRegistrar|proveAndClaimWithResolver| 301450 | 339385 | 320418 |  2    |          |
 |-------------|-------------------------|--------|--------|--------|-------|----------|
 |             |                         |    -14 |    -14 |    -14 |       |          |

The average saving gas is 14.
```

#### [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSSECImpl          |        | 1832341 | 1990780 | 1910159 | 6.4 %      |           │
+| DNSSECImpl          |        | 1831693 | 1990132 | 1909511 | 6.4 %      |           │
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -648 |    -648 |    -648 |            |           |

The average saving gas is 6031.
```

#### TestRRUtils
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| TestRRUtils         |        |    -    |    -    | 1902077 |   6.3 %    |           │
+| TestRRUtils         |        |    -    |    -    | 1900678 |   6.3 %    |           │
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -    |    -    |   -1399 |            |           |

The average saving gas is 1399.
```


## GAS-02 - Using >= instead of > and <= instead of <

### Founded in [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)


* [OffchainDNSResolver.sol#L150-L159](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150-L159)

```
if (lastTxtIdx > txt.length) {
    address dnsResolver = parseAndResolve(txt, 5, txt.length);
    return (dnsResolver, "");
} else {
    address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);
    return (
        dnsResolver,
        txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)
    );
}
```

* [OffchainDNSResolver.sol#L216-L220](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220)

```
if (separator < lastIdx) {
    parentNode = textNamehash(name, separator + 1, lastIdx);
} else {
    separator = lastIdx;
}
```

### Founded in [BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol)

* [BytesUtils.sol#L67-L68](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L67-L68)

```
uint256 shortest = len;
if (otherlen < len) shortest = otherlen;
```

### Founded in [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

* [RRUtils.sol#L384-L399](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384-L399)

```
for (uint256 i = 0; i < data.length + 31; i += 32) {
    uint256 word;
    assembly {
        word := mload(add(add(data, 32), i))
    }
    if (i + 32 > data.length) {
        uint256 unused = 256 - (data.length - i) * 8;
        word = (word >> unused) << unused;
    }
    ac1 +=
        (word &
            0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>
        8;
    ac2 += (word &
        0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
}
```

### Founded in [EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol)

* [EllipticCurve.sol#L42-L43](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L42-L43)

```
if (u == 0 || u == m || m == 0) return 0;
if (u > m) u = u % m;
```

### Founded in [HexUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol)

* [HexUtils.sol#L68-L76](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L68-L76)

```
function hexToAddress(
    bytes memory str,
    uint256 idx,
    uint256 lastIdx
) internal pure returns (address, bool) {
    if (lastIdx - idx < 40) return (address(0x0), false);
    (bytes32 r, bool valid) = hexStringToBytes32(str, idx, lastIdx);
    return (address(uint160(uint256(r))), valid);
}
```


### Description
Using > or < with = saves gas.
In some cases, you could remember that

```
a > b + m implies
a >= b + m + 1

if a, b and m are integer.
```

and

```diff
- if(a > b){
-     action1();
- }
- else{
-     action2();
- }
+ if(a <= b){
+     action2();
+ }
+ else{
+     action1();
+ }
```

#### [OffchainDNSResolver.sol#L150-L159](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150-L159)

```diff
-if (lastTxtIdx > txt.length) {
-    address dnsResolver = parseAndResolve(txt, 5, txt.length);
-    return (dnsResolver, "");
-} else {
-    address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);
-    return (
-        dnsResolver,
-        txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)
-    );
-}
+if (lastTxtIdx <= txt.length) {
+    address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);
+    return (
+        dnsResolver,
+        txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)
+    );
+} else {
+    address dnsResolver = parseAndResolve(txt, 5, txt.length);
+    return (dnsResolver, "");
+}
```

#### [OffchainDNSResolver.sol#L216-L220](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220)

```diff
-if (separator < lastIdx) {
-    parentNode = textNamehash(name, separator + 1, lastIdx);
-} else {
-    separator = lastIdx;
-}
+if (separator >= lastIdx) {
+    separator = lastIdx;
+} else {
+    parentNode = textNamehash(name, separator + 1, lastIdx);
+}
```

#### [BytesUtils.sol#L67-L68](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L67-L68)

```diff
- uint256 shortest = len;
- if (otherlen < len) shortest = otherlen;
+ uint256 shortest = otherlen;
+ if (otherlen >= len) shortest = len;
```

#### [RRUtils.sol#L384-L399](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384-L399)

```diff
- for (uint256 i = 0; i < data.length + 31; i += 32) {
+ for (uint256 i = 0; i <= data.length + 30; i += 32) {
    uint256 word;
    assembly {
        word := mload(add(add(data, 32), i))
    }
-    if (i + 32 > data.length) {
+    if (i + 31 >= data.length) {
        uint256 unused = 256 - (data.length - i) * 8;
        word = (word >> unused) << unused;
    }
    ac1 +=
        (word &
            0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>
        8;
    ac2 += (word &
        0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
}
```

* [EllipticCurve.sol#L42-L43](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L42-L43)

```diff
if (u == 0 || u == m || m == 0) return 0;
- if (u > m) u = u % m;
+ if (u >= m) u = u % m;
```
We can do that because before we check that "u==m".
So the equality check inside if is useless, but >= saves gas with respect >.

#### [HexUtils.sol#L68-L76](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L68-L76)

```diff
function hexToAddress(
    bytes memory str,
    uint256 idx,
    uint256 lastIdx
) internal pure returns (address, bool) {
-   if (lastIdx - idx < 40) return (address(0x0), false);
+   if (lastIdx - idx <= 39) return (address(0x0), false);
    (bytes32 r, bool valid) = hexStringToBytes32(str, idx, lastIdx);
    return (address(uint160(uint256(r))), valid);
}
```

### Test with customized contracts

Using Remix IDE with optimization at 10k runs.

Original
```
pragma solidity ^0.8.0;

contract Attempt{

    function tryGas(uint256 input) public returns(uint256){
        if (
            input > 10
        ) {
            return 0;
        }

        return 1;
    }
}
```

New
```
pragma solidity ^0.8.0;

contract Attempt{

    function tryGas(uint256 input) public returns(uint256){
        if (
            input <= 10
        ) {
            return 1;
        }

        return 0;
    }
}
```


| input |original return | new return  | original execution cost|new execution cost|
|:----: |:--------------:|:-----------:|:----------------------:|:----------------:|
| 5     |  1             | 1           | 286                    | 282              |
| 12    |  0             | 0           | 285                    | 283              |


### Yarn profiles

#### [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| OffchainDNSResolver |        | 1459188 | 1459200 | 1459197 | 4.9 %      |           |
+| OffchainDNSResolver |        | 1458564 | 1458576 | 1458573 | 4.9 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -624 |    -624 |    -624 |            |           |

 The average saving gas is 624.
```

#### [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSRegistrar        |        | 1923407 | 1923659 | 1923519 | 6.4 %      |           |
+| DNSRegistrar        |        | 1922927 | 1923179 | 1923039 | 6.4 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -480 |    -480 |    -480 |            |           |

The average saving gas is 480.
```

```diff
 | Contract        | Method        | Min     | Max     | AVG     | # calls | eur (avg) |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
-| DNSRegistrar    |proveAndClaim  |  198110 |  308834 |  269704 |   7     |           |
+| DNSRegistrar    |proveAndClaim  |  197890 |  308614 |  269484 |   7     |           |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
 |                 |               |    -220 |    -220 |    -220 |         |           |

The average saving gas is 220.
```

```diff
 | Contract    | Method                  | Min    | Max    | AVG    |# calls| eur (avg)|
 |-------------|-------------------------|--------|--------|--------|-------|----------|
-| DNSRegistrar|proveAndClaimWithResolver| 301464 | 339399 | 320432 |  2    |          |
+| DNSRegistrar|proveAndClaimWithResolver| 301244 | 339179 | 320212 |  2    |          |
 |-------------|-------------------------|--------|--------|--------|-------|----------|
 |             |                         |   -220 |   -220 |   -220 |       |          |

The average saving gas is 220.
```

#### [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)
```diff
 | Contract       | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |----------------|--------|---------|---------|---------|------------|-----------|
-| DNSSECImpl     |        | 1832341 | 1990780 | 1910159 | 6.4 %      |           │
+| DNSSECImpl     |        | 1831050 | 1989489 | 1908868 | 6.4 %      |           │
 |----------------|--------|---------|---------|---------|------------|-----------|
 |                |        |   -1291 |   -1291 |   -1291 |            |           |

The average saving gas is 1291.
```

#### [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol)
```diff
 | Contract           | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |--------------------|--------|---------|---------|---------|------------|-----------|
-| P256SHA256Algorithm|        |    -    |    -    |  895974 |   3 %      |           │
+| P256SHA256Algorithm|        |    -    |    -    |  895752 |   3 %      |           │
 |--------------------|--------|---------|---------|---------|------------|-----------|
 |                    |        |         |         |    -222 |            |           |

The average saving gas is 222.
```

#### TestBytesUtils
```diff
 | Contract       | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |--------------- |--------|---------|---------|---------|------------|-----------|
-| TestBytesUtils |        |    -    |    -    | 2254402 |   7.5 %    |           │
+| TestBytesUtils |        |    -    |    -    | 2254186 |   7.5 %    |           │
 |----------------|--------|---------|---------|---------|------------|-----------|
 |                |        |    -    |    -    |    -216 |            |           |

The average saving gas is 216.
```

#### TestHexUtils
```diff
 | Contract       | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |--------------- |--------|---------|---------|---------|------------|-----------|
-| TestHexUtils   |        |    -    |    -    |  234290 |   0.8 %    |           │
+| TestHexUtils   |        |    -    |    -    |  234074 |   0.8 %    |           │
 |----------------|--------|---------|---------|---------|------------|-----------|
 |                |        |    -    |    -    |    -216 |            |           |

The average saving gas is 216.
```

#### TestRRUtils
```diff
 | Contract       | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |----------------|--------|---------|---------|---------|------------|-----------|
-| TestRRUtils    |        |    -    |    -    | 1902077 |   6.3 %    |           │
+| TestRRUtils    |        |    -    |    -    | 1900570 |   6.3 %    |           │
 |----------------|--------|---------|---------|---------|------------|-----------|
 |                |        |    -    |    -    |   -1507 |            |           |

The average saving gas is 1507.
```

## GAS-03 - Avoid to define variable which are used once

### Founded in [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

* [OffchainDNSResolver.sol#L85-L92](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L85-L92)

```
bytes memory rrname = RRUtils.readName(iter.data, iter.offset);
if (
    !rrname.equals(name) ||
    iter.class != CLASS_INET ||
    iter.dnstype != TYPE_TXT
) {
    continue;
}
```

* [OffchainDNSResolver.sol#L150-L159](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150-L159)

```
if (lastTxtIdx > txt.length) {
    address dnsResolver = parseAndResolve(txt, 5, txt.length);
    return (dnsResolver, "");
} else {
    address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);
    return (
        dnsResolver,
        txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)
    );
}
```

### Founded in [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)

* [DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

```
modifier onlyOwner() {
    Root root = Root(ens.owner(bytes32(0)));
    address owner = root.owner();
    require(msg.sender == owner);
    _;
}
```

* [DNSRegistrar.sol#L143-L149](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L143-L149)

```
bytes memory parentName = name.substring(
    labelLen + 1,
    name.length - labelLen - 1
);

// Make sure the parent name is enabled
parentNode = enableNode(parentName);
```

* [DNSRegistrar.sol#L188-L192](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L188-L192)

```
if (parentNode == bytes32(0)) {
    Root root = Root(ens.owner(bytes32(0)));
    root.setSubnodeOwner(label, address(this));
    ens.setResolver(node, resolver);
} else {
```


### Founded in [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

* [RRUtils.sol#L41-L47](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L41-L47)

```
function readName(
    bytes memory self,
    uint256 offset
) internal pure returns (bytes memory ret) {
    uint256 len = nameLength(self, offset);
    return self.substring(offset, len);
}
```

### Founded in [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol)

* [SHA1Digest.sol#L13-L21](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L13-L21)

```
function verify(
    bytes calldata data,
    bytes calldata hash
) external pure override returns (bool) {
    require(hash.length == 20, "Invalid sha1 hash length");
    bytes32 expected = hash.readBytes20(0);
    bytes20 computed = SHA1.sha1(data);
    return expected == computed;
}
```

### Founded in [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)

* [DNSSECImpl.sol#L304-L307](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L304-L307)

```
uint16 computedkeytag = keyrdata.computeKeytag();
if (computedkeytag != rrset.keytag) {
    return false;
}
```


### Description

#### [OffchainDNSResolver.sol#L85-L92](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L85-L92)

```diff
- bytes memory rrname = RRUtils.readName(iter.data, iter.offset);
if (
-     !rrname.equals(name) ||
+     !RRUtils.readName(iter.data, iter.offset).equals(name) ||
    iter.class != CLASS_INET ||
    iter.dnstype != TYPE_TXT
) {
    continue;
}
```

#### [OffchainDNSResolver.sol#L150-L159](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150-L159)

```diff
if (lastTxtIdx > txt.length) {
-    address dnsResolver = parseAndResolve(txt, 5, txt.length);
-    return (dnsResolver, "");
+    return (parseAndResolve(txt, 5, txt.length), "");
} else {
-    address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);
-    return (
-        dnsResolver,
-        txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)
-    );
+    return (
+        parseAndResolve(txt, 5, lastTxtIdx),
+        txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)
+    );
}
```

#### [DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

```diff
modifier onlyOwner() {
-   Root root = Root(ens.owner(bytes32(0)));
-   address owner = root.owner();
+   address owner = Root(ens.owner(bytes32(0))).owner();
    require(msg.sender == owner);
    _;
}
```

#### [DNSRegistrar.sol#L143-L149](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L143-L149)

```diff
-bytes memory parentName = name.substring(
-    labelLen + 1,
-    name.length - labelLen - 1
-);

// Make sure the parent name is enabled
-parentNode = enableNode(parentName);
+parentNode = enableNode(
+    name.substring(
+        labelLen + 1,
+        name.length - labelLen - 1
+    )
+);
```

#### [DNSRegistrar.sol#L188-L192](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L188-L192)

```diff
if (parentNode == bytes32(0)) {
-   Root root = Root(ens.owner(bytes32(0)));
-   root.setSubnodeOwner(label, address(this));
+   Root(ens.owner(bytes32(0))).setSubnodeOwner(label, address(this));
    ens.setResolver(node, resolver);
} else {
```

#### [RRUtils.sol#L41-L47](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L41-L47)

```diff
function readName(
    bytes memory self,
    uint256 offset
) internal pure returns (bytes memory ret) {
-    uint256 len = nameLength(self, offset);
-    return self.substring(offset, len);
+    return self.substring(offset, nameLength(self, offset));
}
```

#### [SHA1Digest.sol#L13-L21](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L13-L21)

```diff
function verify(
    bytes calldata data,
    bytes calldata hash
) external pure override returns (bool) {
    require(hash.length == 20, "Invalid sha1 hash length");
-   bytes32 expected = hash.readBytes20(0);
-   bytes20 computed = SHA1.sha1(data);
-   return expected == computed;
+   return hash.readBytes20(0) == SHA1.sha1(data); 
}
```

#### [DNSSECImpl.sol#L304-L307](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L304-L307)

```diff
- uint16 computedkeytag = keyrdata.computeKeytag();
- if (computedkeytag != rrset.keytag) {
+ if (keyrdata.computeKeytag() != rrset.keytag) {
    return false;
}
```

### Yarn profiles

#### [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| OffchainDNSResolver |        | 1459188 | 1459200 | 1459197 | 4.9 %      |           |
+| OffchainDNSResolver |        | 1451693 | 1451705 | 1451702 | 4.9 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |   -7495 |   -7495 |   -7495 |            |           |

Total average saving gas is 7495.
```


#### [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSRegistrar        |        | 1923407 | 1923659 | 1923519 | 6.4 %      |           |
+| DNSRegistrar        |        | 1908129 | 1908381 | 1908241 | 6.4 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |  -15278 |  -15278 |  -15278 |            |           |

The average saving gas is 15278.
```


```diff
 | Contract        | Method        | Min     | Max     | AVG     | # calls | eur (avg) |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
-| DNSRegistrar    |proveAndClaim  |  198110 |  308834 |  269704 |   7     |           |
+| DNSRegistrar    |proveAndClaim  |  197979 |  308703 |  269565 |   7     |           |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
 |                 |               |    -131 |    -131 |    -139 |         |           |

The average saving gas is 139.
```

```diff
 | Contract    | Method                  | Min    | Max    | AVG    |# calls| eur (avg)|
 |-------------|-------------------------|--------|--------|--------|-------|----------|
-| DNSRegistrar|proveAndClaimWithResolver| 301464 | 339399 | 320432 |  2    |          |
+| DNSRegistrar|proveAndClaimWithResolver| 301321 | 339256 | 320289 |  2    |          |
 |-------------|-------------------------|--------|--------|--------|-------|----------|
 |             |                         |   -143 |   -143 |   -143 |       |          |

The average saving gas is 143.
```

#### [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| SHA1Digest          |        |    -    |    -    |  459326 |   1.5 %    |           │
+| SHA1Digest          |        |    -    |    -    |  457190 |   1.5 %    |           │
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -    |    -    |   -2136 |            |           |

The average saving gas is 2136.
```

#### [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSSECImpl          |        | 1832341 | 1990780 | 1910159 | 6.4 %      |           │
+| DNSSECImpl          |        | 1826310 | 1984749 | 1904128 | 6.3 %      |           │
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |   -6031 |   -6031 |   -6031 |            |           |

The average saving gas is 6031.
```


## GAS-04 - Avoid to modify variable which are used once

### Founded in [EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol)

* [EllipticCurve.sol#L414-L417](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L414-L417)

```
bytes memory rrname = RRUtils.readName(iter.data, iter.offset);
uint256 Px = inverseMod(P[2], p);
Px = mulmod(P[0], mulmod(Px, Px, p), p);

return Px % n == rs[0];
```

### Description

#### [EllipticCurve.sol#L414-L417](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L414-L417)

```diff
bytes memory rrname = RRUtils.readName(iter.data, iter.offset);
uint256 Px = inverseMod(P[2], p);
- Px = mulmod(P[0], mulmod(Px, Px, p), p);

- return Px % n == rs[0];
+ return mulmod(P[0], mulmod(Px, Px, p), p) % n == rs[0];
```

### Yarn profiles

#### [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol)
```diff
 | Contract           | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |--------------------|--------|---------|---------|---------|------------|-----------|
-| P256SHA256Algorithm|        |    -    |    -    |  895974 |   3 %      |           │
+| P256SHA256Algorithm|        |    -    |    -    |  895752 |   3 %      |           │
 |--------------------|--------|---------|---------|---------|------------|-----------|
 |                    |        |         |         |    -222 |            |           |

The average saving gas is 222.
```


## GAS-05 - Return result of boolean formula without using if/else

### Founded in [EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol)

* [EllipticCurve.sol#L124-L132](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L124-L132)

```
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

### Description

```
if(condition){
    return true;
}
else{
    return false;
}
```
becames
```
return condition;
```


#### [EllipticCurve.sol#L124-L132](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L124-L132)

```diff
function isZeroCurve(
    uint256 x0,
    uint256 y0
) internal pure returns (bool isZero) {
-    if (x0 == 0 && y0 == 0) {
-        return true;
-    }
-    return false;
+    return x0 == 0 && y0 == 0
}
```

### Yarn profiles

#### [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol)
```diff
 | Contract           | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |--------------------|--------|---------|---------|---------|------------|-----------|
-| P256SHA256Algorithm|        |    -    |    -    |  895974 |   3 %      |           │
+| P256SHA256Algorithm|        |    -    |    -    |  892092 |   3 %      |           │
 |--------------------|--------|---------|---------|---------|------------|-----------|
 |                    |        |         |         |   -3882 |            |           |

The average saving gas is 3882.
```


## GAS-06 - ++i Costs Less Gas Than i++ (--i/i-- too)

This report was already in [Automated Findings - GAS-7](https://gist.github.com/YinjiDawn/8442dfeff30af72ac83cd8ac657ef954#NC%E2%80%9121).
We just found other instances.

### Founded in [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

* [RRUtils.sol#L267-L270](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L267-L270)

```
while (counts > othercounts) {
    off = progress(self, off);
    counts--;
}
```

* [RRUtils.sol#L291-L295](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L291-L295)

```
while (counts > othercounts) {
    prevoff = off;
    off = progress(self, off);
    counts--;
}
```

* [RRUtils.sol#L297-L301](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L297-L301)

```
while (othercounts > counts) {
    otherprevoff = otheroff;
    otheroff = progress(other, otheroff);
    othercounts--;
}
```

### Description

#### [RRUtils.sol#L267-L270](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L267-L270)

```diff
while (counts > othercounts) {
    off = progress(self, off);
-   counts--;
+   --counts;
}
```

#### [RRUtils.sol#L291-L295](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L291-L295)

```diff
while (counts > othercounts) {
    prevoff = off;
    off = progress(self, off);
-   counts--;
+   --counts;
}
```

#### [RRUtils.sol#L297-L301](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L297-L301)

```diff
while (othercounts > counts) {
    otherprevoff = otheroff;
    otheroff = progress(other, otheroff);
-   othercounts--;
+   --othercounts;
}
```

### Yarn profiles

#### [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| OffchainDNSResolver |        | 1459188 | 1459200 | 1459197 | 4.9 %      |           |
+| OffchainDNSResolver |        | 1459212 | 1459224 | 1459221 | 4.9 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |      24 |      24 |      24 |            |           |

Total average **lost** gas is 24.
```


#### [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSRegistrar        |        | 1923407 | 1923659 | 1923519 | 6.4 %      |           |
+| DNSRegistrar        |        | 1922531 | 1922783 | 1922643 | 6.4 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -876 |    -876 |    -876 |            |           |

The average saving gas is 876.
```


```diff
 | Contract        | Method        | Min     | Max     | AVG     | # calls | eur (avg) |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
-| DNSRegistrar    |proveAndClaim  |  198110 |  308834 |  269704 |   7     |           |
+| DNSRegistrar    |proveAndClaim  |  198095 |  308814 |  269688 |   7     |           |
 |-----------------|---------------|---------|---------|---------|---------|-----------|
 |                 |               |     -15 |     -20 |     -16 |         |           |

The average saving gas is 16.
```

```diff
 | Contract    | Method                  | Min    | Max    | AVG    |# calls| eur (avg)|
 |-------------|-------------------------|--------|--------|--------|-------|----------|
-| DNSRegistrar|proveAndClaimWithResolver| 301464 | 339399 | 320432 |  2    |          |
+| DNSRegistrar|proveAndClaimWithResolver| 301449 | 339384 | 320417 |  2    |          |
 |-------------|-------------------------|--------|--------|--------|-------|----------|
 |             |                         |    -15 |    -15 |    -15 |       |          |

The average saving gas is 15.
```


#### [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)
```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| DNSSECImpl          |        | 1832341 | 1990780 | 1910159 | 6.4 %      |           │
+| DNSSECImpl          |        | 1831885 | 1990324 | 1909703 | 6.4 %      |           │
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |    -456 |    -456 |    -456 |            |           |

The average saving gas is 456.
```

#### TestRRUtils
```diff
 | Contract       | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |----------------|--------|---------|---------|---------|------------|-----------|
-| TestRRUtils    |        |    -    |    -    | 1902077 |   6.3 %    |           │
+| TestRRUtils    |        |    -    |    -    | 1901218 |   6.3 %    |           │
 |----------------|--------|---------|---------|---------|------------|-----------|
 |                |        |    -    |    -    |    -859 |            |           |

The average saving gas is 859.
```




## GAS-07 - Using do-while loop and avoid break condition and while(true)

### Founded in [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

* [RRUtils.sol#L19-L33](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L19-L33)

```
function nameLength(
    bytes memory self,
    uint256 offset
) internal pure returns (uint256) {
    uint256 idx = offset;
    while (true) {
        assert(idx < self.length);
        uint256 labelLen = self.readUint8(idx);
        idx += labelLen + 1;
        if (labelLen == 0) {
            break;
        }
    }
    return idx - offset;
}
```

### Description

#### [RRUtils.sol#L19-L33](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L19-L33)

```diff
function nameLength(
    bytes memory self,
    uint256 offset
) internal pure returns (uint256) {
    uint256 idx = offset;
-   while (true) {
-       assert(idx < self.length);
-       uint256 labelLen = self.readUint8(idx);
-       idx += labelLen + 1;
-       if (labelLen == 0) {
-           break;
-       }
-   }
+   uint256 labelLen;
+   do {
+       assert(idx < self.length);
+       labelLen = self.readUint8(idx);
+       idx += labelLen + 1;
+   } while (labelLen != 0);
    return idx - offset;
}
```

### Yarn profiles

#### [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)

```diff
 | Contract            | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------------|--------|---------|---------|---------|------------|-----------|
-| OffchainDNSResolver |        | 1459188 | 1459200 | 1459197 | 4.9 %      |           |
+| OffchainDNSResolver |        | 1456409 | 1456421 | 1456418 | 4.9 %      |           |
 |---------------------|--------|---------|---------|---------|------------|-----------|
 |                     |        |   -2779 |   -2779 |   -2779 |            |           |

The average saving gas is 2779.
```


#### [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)

```diff
 | Contract     | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |--------------|--------|---------|---------|---------|------------|-----------|
-| DNSRegistrar |        | 1923407 | 1923659 | 1923519 | 6.4 %      |           |
+| DNSRegistrar |        | 1922555 | 1922807 | 1922667 | 6.4 %      |           |
 |--------------|--------|---------|---------|---------|------------|-----------|
 |              |        |    -852 |    -852 |    -852 |            |           |

The average saving gas is 852.
```

```diff
 | Contract            | Method      | Min     | Max    | AVG     | % of limit | eur (avg) |
 |---------------------|-------------|---------|--------|---------|------------|-----------|
-| DNSRegistrar        |proveAndClaim|  198110 | 308834 |  269704 | 7          |           |
+| DNSRegistrar        |proveAndClaim|  197582 | 308238 |  269166 | 7          |           |
 |---------------------|-------------|---------|--------|---------|------------|-----------|
 |                     |             |   -528  |   -596 |    -538 |            |           |

The average saving gas is 538.
```

```diff
 | Contract    | Method                  | Min    | Max   | AVG    | % of limit | eur (avg) |
 |-------------|-------------------------|--------|-------|--------|------------|-----------|
-| DNSRegistrar|proveAndClaimWithResolver| 301464 | 339399| 320432 | 2          |           |
+| DNSRegistrar|proveAndClaimWithResolver| 300936 | 338871| 319904 | 2          |           |
 |-------------|-------------------------|--------|-------|--------|------------|-----------|
 |             |                         |  -528  |  -528 |   -528 |            |           |

The average saving gas is 528.
```


#### [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)
```diff
 | Contract   | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |------------|--------|---------|---------|---------|------------|-----------|
-| DNSSECImpl |        | 1832341 | 1990780 | 1910159 | 6.4 %      |           |
+| DNSSECImpl |        | 1829514 | 1987953 | 1907332 | 6.4 %      |           |
 |------------|--------|---------|---------|---------|------------|-----------|
 |            |        |   -2827 |   -2827 |   -2827 |            |           |

The average saving gas is 2827.
```

#### [OwnedResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/resolvers/OwnedResolver.sol)
```diff
 | Contract      | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |---------------|--------|---------|---------|---------|------------|-----------|
-| OwnedResolver |        |    -    |    -    | 2349096 | 7.8 %      |           |
+| OwnedResolver |        |    -    |    -    | 2348268 | 7.8 %      |           |
 |---------------|--------|---------|---------|---------|------------|-----------|
 |               |        |      0  |       0 |    -828 |            |           |

The average saving gas is 828.
```

#### [PublicResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/resolvers/PublicResolver.sol)
```diff
 | Contract       | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |----------------|--------|---------|---------|---------|------------|-----------|
-| PublicResolver |        | 2762394 | 2762634 | 2762625 | 9.2 %      |           |
+| PublicResolver |        | 2761554 | 2761794 | 2761785 | 9.2 %      |           |
 |----------------|--------|---------|---------|---------|------------|-----------|
 |                |        |    -840 |    -840 |    -840 |            |           |

The average saving gas is 840.
```

#### TestRRUtils
```diff
 | Contract    | Method | Min     | Max     | AVG     | % of limit | eur (avg) |
 |-------------|--------|---------|---------|---------|------------|-----------|
-| TestRRUtils |        |    -    |    -    | 1902077 | 6.3 %      |           |
+| TestRRUtils |        |    -    |    -    | 1899490 | 6.3 %      |           |
 |-------------|--------|---------|---------|---------|------------|-----------|
 |             |        |      0  |      0  |   -2587 |            |           |

The average saving gas is 2587.
```