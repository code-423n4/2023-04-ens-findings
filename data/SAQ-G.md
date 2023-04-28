## Summary

### Gas Optimization

no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] | Use calldata instead of memory for function parameters | 2 | - |
| [G-02] | Using delete statement can save gas | 1 | - |
| [G-03] | It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied | 1 | - |
| [G-04] | internal functions only called once can be inlined to save gas | 11 | - |
| [G-05] | Public Functions To External | 1 | - |
| [G-06] | require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas  | 3 | - |
| [G-07] | ++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 8 | - |
| [G-08] | Using unchecked blocks to save gas | 7 | - |
| [G-09] | CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS | 7 | - |
| [G-10] | USE ASSEMBLY TO CHECK FOR ADDRESS(0)  | 6 | - |
| [G-11] | Duplicated require()/if() checks should be refactored to a modifier or function | 6 | - |
| [G-12] | Use nested if and, avoid multiple check combinations| 2 | - |



## Gas Optimizations  

## [G-1] Use calldata instead of memory for function parameters


```solidty
file: /contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
30   function parseSignature(
        bytes memory data
    ) internal pure returns (uint256[2] memory) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30


```solidty
file: /contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
37   function parseKey(
        bytes memory data
    ) internal pure returns (uint256[2] memory) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37

---------------------------------------------------------------------------------------------------------


## [G-2] Using `delete` statement can save gas


```solidity
file: /contracts/utils/NameEncoder.sol
34    labelLength = 0;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L34

--------------------------------------------------------------------------------------------------------------------------


## [G-3]  It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied


```solidity
file: /contracts/dnssec-oracle/BytesUtils.sol
77    for (uint256 idx = 0; idx < shortest; idx += 32) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77

--------------------------------------------------------------------------------------------------------------------------

## [G-4] `internal` functions only called once can be `inlined` to save gas 

```solidity
file: /contracts/dnsregistrar/RecordParser.sol
14   function readKeyValue(
        bytes memory input,
        uint256 offset,
        uint256 len
    )
        internal
        pure
        returns (bytes memory key, bytes memory value, uint256 nextOffset)
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L14


```solidity
file: /contracts/dnsregistrar/DNSRegistrar.sol
174   function _enableNode(
        bytes memory domain,
        uint256 offset
    ) internal returns (bytes32 node) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L174


```solidity 
file: /contracts/dnssec-oracle/BytesUtils.sol
23   function compare(
        bytes memory self,
        bytes memory other
    ) internal pure returns (int256) {


52   function compare(
        bytes memory self,
        uint256 offset,
        uint256 len,
        bytes memory other,
        uint256 otheroffset,
        uint256 otherlen
    ) internal pure returns (int256) {

111   function equals(
        bytes memory self,
        uint256 offset,
        bytes memory other,
        uint256 otherOffset,
        uint256 len
    ) internal pure returns (bool) {        

129     function equals(
        bytes memory self,
        uint256 offset,
        bytes memory other,
        uint256 otherOffset
    ) internal pure returns (bool) {


148   function equals(
        bytes memory self,
        uint256 offset,
        bytes memory other
    ) internal pure returns (bool) {


164    function equals(
        bytes memory self,
        bytes memory other
    ) internal pure returns (bool) {


```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L32

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L52

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L111

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L129

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L148

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L164


```solidity
file: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

316    function multiplyPowerBase2(
        uint256 x0,
        uint256 y0,
        uint256 exp
    ) internal pure returns (uint256, uint256) {


377    function multipleGeneratorByScalar(
        uint256 scalar
    ) internal pure returns (uint256, uint256) {        


386  function validateSignature(
        bytes32 message,
        uint256[2] memory rs,
        uint256[2] memory Q
    ) internal pure returns (bool) {        

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L316

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L377

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L386


--------------------------------------------------------------------------------------------------------------------------

## [G-5] `Public` Functions To `External`


The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.


```solidity
file:/contracts/dnssec-oracle/DNSSECImpl.sol
107     function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
        public
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L107

--------------------------------------------------------------------------------------------------------------------------


## [G-6] require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas


```solidity
file:/contracts/dnssec-oracle/RRUtils.sol
381   require(data.length <= 8192, "Long keys not permitted");

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381



```solidity
file:/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
40     require(data.length == 68, "Invalid p256 key length");

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L40


```solidity
file:/contracts/dnssec-oracle/digests/SHA1Digest.sol
17     require(hash.length == 20, "Invalid sha1 hash length");

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17

--------------------------------------------------------------------------------------------------------------------------


## [G-7]  ++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP


```solidity
file: /contracts/dnssec-oracle/RRUtils.sol
384    for (uint256 i = 0; i < data.length + 31; i += 32) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384


```solidity
file: /contracts/dnsregistrar/DNSClaimChecker.sol
51     while (idx < endIdx) {
            uint256 len = rdata.readUint8(idx);
            idx += 1;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L51


```solidity
file: /contracts/dnssec-oracle/RRUtils.sol
24    while (true) {
            assert(idx < self.length);
            uint256 labelLen = self.readUint8(idx);
            idx += labelLen + 1;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L24



```solidity
file: /contracts/dnssec-oracle/RRUtils.sol
60      while (true) {
            assert(offset < self.length);
            uint256 labelLen = self.readUint8(offset);
            offset += labelLen + 1;
            if (labelLen == 0) {
                break;
            }
            count += 1;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L60


```solidity
file: /contracts/dnssec-oracle/RRUtils.sol
267   while (counts > othercounts) {
            off = progress(self, off);
            counts--;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L267


```solidity
file:/contracts/dnssec-oracle/RRUtils.sol
291    while (counts > othercounts) {
            prevoff = off;
            off = progress(self, off);
            counts--;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L291



```solidity
file:/contracts/dnssec-oracle/RRUtils.sol
297     while (othercounts > counts) {
            otherprevoff = otheroff;
            otheroff = progress(other, otheroff);
            othercounts--;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L297



```solidity
file:/contracts/dnssec-oracle/RRUtils.sol
304      while (counts > 0 && !self.equals(off, other, otheroff)) {
            prevoff = off;
            off = progress(self, off);
            otherprevoff = otheroff;
            otheroff = progress(other, otheroff);
            counts -= 1;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304

--------------------------------------------------------------------------------------------------------------------------


## [G-8] Using unchecked blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isnâ€™t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

```solidity 
file: /contracts/dnsregistrar/OffchainDNSResolver.sol
178 if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178



```solidity 
file: /contracts/dnsregistrar/DNSRegistrar.sol
128    return
            interfaceID == type(IERC165).interfaceId ||
            interfaceID == type(IDNSRegistrar).interfaceId;


187      if (owner == address(0) || owner == previousRegistrar) {           

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L128-L129-L130

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187


```solidity 
file:/contracts/dnssec-oracle/BytesUtils.sol
366       ret = (ret << 4) | (decoded >> 1);

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L366


```solidity 
file:/contracts/dnssec-oracle/algorithms/EllipticCurve.sol
392     if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L392


```solidity 
file:/contracts/dnssec-oracle/algorithms/EllipticCurve.sol
138      if (0 == x || x == p || 0 == y || y == p) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L138


```solidity 
file: /contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol
43     return ok && sha256(data) == result.readBytes32(result.length - 32);

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L43

--------------------------------------------------------------------------------------------------------------------------


## [G-9] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

Consider making the stack variables before the loop which gonna save gas


```solidity 
file: /contracts/dnsregistrar/DNSClaimChecker.sol
29   for (
            RRUtils.RRIterator memory iter = data.iterateRRs(0);
               !iter.done();
            iter.next()
        )

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L29


```solidity 
file: /contracts/dnssec-oracle/BytesUtils.sol
77     for (uint256 idx = 0; idx < shortest; idx += 32) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77


```solidity 
file: /contracts/dnssec-oracle/BytesUtils.sol
341      for (uint256 i = 0; i < len; i++) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L341


```solidity 
file: /contracts/dnssec-oracle/RRUtils.sol
384     for (uint256 i = 0; i < data.length + 31; i += 32) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384


```solidity 
file: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
325       for (uint256 i = 0; i < exp; i++) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L325



```solidity 
file: /contracts/dnssec-oracle/DNSSECImpl.sol
118      for (uint256 i = 0; i < input.length; i++) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118

```solidity 
file: /contracts/utils/NameEncoder.sol
25      for (uint256 i = length - 1; i >= 0; i--) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L25

--------------------------------------------------------------------------------------------------------------------------


## [G-10]  USE ASSEMBLY TO CHECK FOR ADDRESS(0) 



```solidity 
file: /contracts/dnsregistrar/OffchainDNSResolver.sol
102    if (dnsresolver != address(0)) {


197       if (resolver == address(0)) {    

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L102

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197


```solidity 
file: /contracts/dnsregistrar/DNSRegistrar.sol
115      if (addr != address(0)) {

187     if (owner == address(0) || owner == previousRegistrar) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187


```solidity 
file: /contracts/dnssec-oracle/DNSSECImpl.sol
317      if (address(algorithm) == address(0)) {

420      if (address(digests[digesttype]) == address(0)) {    

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420

--------------------------------------------------------------------------------------------------------------------------


## [G-11] Duplicated require()/if() checks should be refactored to a modifier or function


```solidity 
file: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
169     if (isZeroCurve(x0, y0)) {


221     if (isZeroCurve(x0, y0)) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L169

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L221


```solidity 
file: /contracts/dnssec-oracle/DNSSECImpl.sol
271      if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {

350      if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L271

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L350



```solidity 
file: /contracts/dnssec-oracle/BytesUtils.sol
18      require(offset + len <= self.length);

305     require(offset + len <= self.length);

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L18

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L305

--------------------------------------------------------------------------------------------------------------------------


## [G-12] Use nested if and, avoid multiple check combinations

Using nested if cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.


```solidity 
file: /contracts/dnsregistrar/OffchainDNSResolver.sol
178      if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178


```solidity 
file: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
128     if (x0 == 0 && y0 == 0) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128

--------------------------------------------------------------------------------------------------------------------------