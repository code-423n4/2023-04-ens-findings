### Gas Optimizations

| Number | Issue                                         | Instances |
| :----: | :-------------------------------------------- | :-------: |
| [G-01] | Named return values                           |     3     |
| [G-02] | Use `!= 0` instead of `> 0`                   |     1     |
| [G-03] | Strict inequalities                           |    10     |
| [G-04] | Avoid storage variable assignment if possible |     2     |

#### [G-01] Named return values

When using named return values for a function, e.g. `returns (address[] memory tokens, uint256[] memory amounts)`, do not use an explicit return statement, e.g. `return (tokens, amounts);`. Removing redundant code will save gas on deployment and execution and improve code clarity.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

204:    return node;
```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

173:    return rrset;
```

```solidity
File: contracts/utils/NameEncoder.sol

50:     return (dnsName, node);
```

#### [G-02] Use != 0 instead of > 0

For unsigned integers, it uses less gas and achieves the same effect to use `!= 0` rather than `> 0`.

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

304:    while (counts > 0 && !self.equals(off, other, otheroff)) {
```

#### [G-03] Strict inequalities

Non-strict inequalities are cheaper than strict ones because, in the EVM, there is no opcode for non-strict inequalities (`>=`, `<=`). When non-strict inequalities are used in Solidity, two operations are performed (e.g., `>` and `=`.) Consider replacing the non-strict inequalities like `>=` with their strict counterpart `>` if doing so does not incur any extra gas usage (e.g., additional arithmetic).

For example:

```solidity
// Before
if (shortest - idx >= 32) {

// After
if (shortest - idx > 31) {
```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

87:    if (shortest - idx >= 32) {

265:   require(len <= 32);

275:   for (; len >= 32; len -= 32) {

337:   require(len <= 52);

343:   require(char >= 0x30 && char <= 0x7A);

345:   require(decoded <= 0x20);
```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

337:    return int32(i1) - int32(i2) >= 0;

381:    require(data.length <= 8192, "Long keys not permitted");
```

```solidity
File: contracts/utils/NameEncoder.sol

25:     for (uint256 i = length - 1; i >= 0; i--) {
```

#### [G-04] Avoid storage variable assignment if possible

In Solidity, it costs more to write a storage variable than to read it. Writing to a storage variable results in a state change, which incurs gas costs for executing the transaction. On the other hand, reading a storage variable does not change the state, and it only requires a small amount of gas to access the variable value.

For “setter” functions that are exposed so that the value of a storage variable can be set, the new value should first be compared against the current value. If the new value is the same as the current value, the assignment of the storage variable can be skipped and therefore gas can be saved.

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

64:    function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
65:        algorithms[id] = algo;
66:        emit AlgorithmUpdated(id, address(algo));
67:    }

75:    function setDigest(uint8 id, Digest digest) public owner_only {
76:        digests[id] = digest;
77:        emit DigestUpdated(id, address(digest));
78:    }
```

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

80:    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
81:        suffixes = _suffixes;
82:        emit NewPublicSuffixList(address(suffixes));
83:    }
```
