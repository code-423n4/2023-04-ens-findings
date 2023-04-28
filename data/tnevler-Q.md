# Report
## Low Risk ##
### [L-1]: Missing checks for address(0x0)
**Context:**

1. ```previousRegistrar = _previousRegistrar;``` [L62](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62) 
1. ```resolver = _resolver;``` [L63](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L63) 

**Recommendation:**

Add non-zero address checks when set address state variables.

### [L-2]: Unsafe casting may overflow
**Context:**

1. ```return uint8(self[idx]);``` [L183](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L183) 
1. ```decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);``` [L344](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L344) 

**Description:**

While Solidity 0.8.x checks for overflows on arithmetic operations, it does not do so for casting.

**Recommendation:**

Use OpenZeppelin’s SafeCast library to prevent unexpected overflows.

### [L-3]: setOwner should be two step process
**Context:**

1. ```import "./Owned.sol";``` [L5](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L5)

**Description:**

Lack of two-step procedure for transfer of ownership makes it error-prone. It’s possible that the owner mistakenly transfers ownership to the uncontrolled account and it will break all functions with owner_only() modifier.

**Recommendation:**

Implement a two step process for transfer of ownership. Owner call transferOwnership()  function where nominates an account. Nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This will confirm that the nominated EOA account is valid and active account.

### [L-4]: The events should include the new value and old value where possible
**Context:**

1. ```emit NewPublicSuffixList(address(suffixes));``` [L82](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82) 
1. ```emit AlgorithmUpdated(id, address(algo));``` [L66](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L66)
1. ```emit DigestUpdated(id, address(digest));``` [L77](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L77)

**Description:**

Events are generally emitted when sensitive changes are made to the contracts. Some events are missing important parameters. 

## Non-Critical Issues ##
### [N-1]: Event is not used
**Context:**

```event Debug(bytes32 x);``` [L4](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4)

### [N-2]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return ("", "", type(uint256).max);``` [L25](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L25) 
1. ```return _enableNode(domain, 0);``` [L171](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L171) 
1. ```return node;``` [L204](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L204) 
1. ```return uint8(self[idx]);``` [L183](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L183) 
1. ```return self.substring(offset, len);``` [L46](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L46) 
1. ```return true;``` [L129](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L129) 
1. ```return false;``` [L131](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L131) 
1. ```return toAffinePoint(x1, y1, z1);``` [L371](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L371) 
1. ```return verifyRRSet(input, block.timestamp);``` [L96](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L96) 
1. ```return (proof, inception);``` [L127](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L127) 
1. ```return rrset;``` [L173](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L173) 
1. ```return (dnsName, node);``` [L50](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L50) 

**Recommendation:**

Choose named return variable or return statement.

### [N-3]: Follow Solidity standard naming conventions
**Context:**

1. ```uint256 otheroffset,``` [L57](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L57) (Change to otherOffset)
1. ```uint256 otherlen``` [L58](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L58) (Change to otherLen)
1. ```bytes constant base32HexTable =``` [L322](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant a =``` [L21](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant b =``` [L23](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L23) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant gx =``` [L25](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L25) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant gy =``` [L27](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L27) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant p =``` [L29](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L29) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant n =``` [L31](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L31) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 constant lowSmax =``` [L34](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34) (Constant name must be in capitalized SNAKE_CASE)
1. ```uint256 LHS = mulmod(y, y, p); // y^2``` [L142](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L142) (Variable name must be in mixedCase)
1. ```uint256 RHS = mulmod(mulmod(x, x, p), x, p); // x^3``` [L143](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L143) (Variable name must be in mixedCase)
1. ```uint256[2] memory Q``` [L389](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L389) (function param name must be in mixedCase)
1. ```uint256[3] memory P = addAndReturnProjectivePoint(x1, y1, x2, y2);``` [L408](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L408) (Variable name must be in mixedCase)
1. ```uint256 Px = inverseMod(P[2], p);``` [L414](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L414) (Variable name must be in mixedCase)
1. ```bytes memory N,``` [L15](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L15) (Function param name must be in mixedCase)
1. ```bytes memory E,``` [L16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L16) (Function param name must be in mixedCase)
1. ```bytes memory S``` [L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L17) (Function param name must be in mixedCase)

**Description:**

The above codes don't follow [Solidity's standard naming convention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions).  
- Structs should be named using the CapWords style. Examples: MyCoin, Position, PositionXY.
- Events should be named using the CapWords style. Examples: Deposit, Transfer, Approval, BeforeTransfer, AfterTransfer.
- Functions should use mixedCase. Examples: getBalance, transfer, verifyOwner, addMember, changeOwner.
- Function arguments should use mixedCase. Examples: initialSupply, account, recipientAddress, senderAddress, newOwner.
- Local and State Variable should use mixedCase. Examples: totalSupply, remainingSupply, balancesOf, creatorAddress, isPreSale, tokenExchangeRate.
- Constants should be named with all capital letters with underscores separating words. Examples: MAX_BLOCKS, TOKEN_NAME, TOKEN_TICKER, CONTRACT_VERSION.
- Modifier should use mixedCase. Examples: onlyBy, onlyAfter, onlyDuringThePreSale.
- Enums, in the style of simple type declarations, should be named using the CapWords style. Examples: TokenGroup, Frame, HashStyle, CharacterLocation.

### [N-4]: Missing leading underscores
**Context:**

1. ```uint16 constant CLASS_INET = 1;``` [L16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16) 
1. ```uint16 constant TYPE_TXT = 16;``` [L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L17) 
1. ```function parseRR(``` [L136](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L136) 
1. ```function readTXT(``` [L162](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162) 
1. ```function parseAndResolve(``` [L173](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L173) 
1. ```function resolveName(``` [L190](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L190) 
1. ```function textNamehash(``` [L209](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L209) 
1. ```function memcpy(uint256 dest, uint256 src, uint256 len) private pure {``` [L273](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L273) 
1. ```function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {``` [L40](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L40) 
1. ```function toProjectivePoint(``` [L65](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L65) 
1. ```function addAndReturnProjectivePoint(``` [L77](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L77) 
1. ```function toAffinePoint(``` [L92](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L92) 
1. ```function zeroProj()``` [L106](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L106) 
1. ```function zeroAffine() internal pure returns (uint256 x, uint256 y) {``` [L117](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L117) 
1. ```function isZeroCurve(``` [L124](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L124) 
1. ```function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {``` [L137](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L137) 
1. ```function twiceProj(``` [L159](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L159) 
1. ```function addProj(``` [L208](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L208) 
1. ```function addProj2(``` [L247](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L247) 
1. ```function add(``` [L286](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L286) 
1. ```function twice(``` [L302](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L302) 
1. ```function multiplyPowerBase2(``` [L316](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L316) 
1. ```function multiplyScalar(``` [L335](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L335) 
1. ```function multipleGeneratorByScalar(``` [L377](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L377) 
1. ```function validateSignature(``` [L386](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L386) 
1. ```function parseSignature(``` [L30](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30) 
1. ```function parseKey(``` [L37](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37) 
1. ```function validateSignedSet(``` [L140](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L140) 
1. ```function validateRRs(``` [L181](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L181) 
1. ```function verifySignature(``` [L225](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L225) 
1. ```function verifyWithKnownKey(``` [L254](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L254) 
1. ```function verifySignatureWithKey(``` [L285](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L285) 
1. ```function verifyWithDS(``` [L330](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L330) 
1. ```function verifyKeyWithDS(``` [L373](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L373) 
1. ```function verifyDSHash(``` [L415](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L415) 

**Description:**

Internal and private functions, state variables, constants, and immutables should starting with an underscore.

### [N-5]: Mark visibility of variables
**Context:**

1. ```uint16 constant CLASS_INET = 1;``` [L16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16) 
1. ```uint16 constant TYPE_TXT = 16;``` [L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L17) 
1. ```bytes constant base32HexTable =``` [L322](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322) 
1. ```uint256 constant RRSIG_TYPE = 0;``` [L72](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72) 
1. ```uint256 constant RRSIG_ALGORITHM = 2;``` [L73](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L73) 
1. ```uint256 constant RRSIG_LABELS = 3;``` [L74](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L74) 
1. ```uint256 constant RRSIG_TTL = 4;``` [L75](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L75) 
1. ```uint256 constant RRSIG_EXPIRATION = 8;``` [L76](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L76) 
1. ```uint256 constant RRSIG_INCEPTION = 12;``` [L77](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L77) 
1. ```uint256 constant RRSIG_KEY_TAG = 16;``` [L78](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L78) 
1. ```uint256 constant RRSIG_SIGNER_NAME = 18;``` [L79](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L79) 
1. ```uint256 constant DNSKEY_FLAGS = 0;``` [L210](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L210) 
1. ```uint256 constant DNSKEY_PROTOCOL = 2;``` [L211](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L211) 
1. ```uint256 constant DNSKEY_ALGORITHM = 3;``` [L212](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L212) 
1. ```uint256 constant DNSKEY_PUBKEY = 4;``` [L213](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L213) 
1. ```uint256 constant DS_KEY_TAG = 0;``` [L236](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L236) 
1. ```uint256 constant DS_ALGORITHM = 2;``` [L237](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L237) 
1. ```uint256 constant DS_DIGEST_TYPE = 3;``` [L238](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L238) 
1. ```uint256 constant DS_DIGEST = 4;``` [L239](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L239) 
1. ```uint256 constant a =``` [L21](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21) 
1. ```uint256 constant b =``` [L23](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L23) 
1. ```uint256 constant gx =``` [L25](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L25) 
1. ```uint256 constant gy =``` [L27](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L27) 
1. ```uint256 constant p =``` [L29](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L29) 
1. ```uint256 constant n =``` [L31](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L31) 
1. ```uint256 constant lowSmax =``` [L34](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34) 
1. ```uint16 constant DNSCLASS_IN = 1;``` [L27](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27) 
1. ```uint16 constant DNSTYPE_DS = 43;``` [L29](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L29) 
1. ```uint16 constant DNSTYPE_DNSKEY = 48;``` [L30](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L30) 
1. ```uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;``` [L32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L32) 

### [N-6]: Wrong order of Layout
**Context:**

1. ```bytes constant base32HexTable =``` [L322](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322) (constant can not go after internal function)
1. ```uint256 constant RRSIG_TYPE = 0;``` [L72](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72) (state variable can not go after internal function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

### [N-7]: NatSpec is missing
**Context:**

1. [DNSClaimChecker.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol)
1. [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol) (6 functions are missing NatSpec)
1. [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol) (5 functions are missing NatSpec)
1. ```function memcpy(uint256 dest, uint256 src, uint256 len) private pure {``` [L273](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L273) 
1. ```function readSignedSet(``` [L94](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L94) 
1. [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol) (7 functions are missing NatSpec)
1. [RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol)
1. ```function parseSignature(``` [L30](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30) 
1. ```function parseKey(``` [L37](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37) 
1. [RSASHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol)
1. [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol)
1. [SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol)
1. [SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)
1. [NameEncoder.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol)

### [N-8]: Typos
**Context:**

1. ```* @dev Compares a range of 'self' to all of 'other' and returns True iff``` [L141](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L141) (Change **iff** to **if**)
1. ```* @return True iff the signature is valid.``` [L15](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L15) (Change **iff** to **if**)

### [N-9]: Natspec is incomplete
**Context:**

1. ```* @dev Compares two serial numbers using RFC1982 serial number math.``` [L330](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L330) (param and return tags are missing)
1. `[EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol) (param and return tags are missing in all functions)
1. ```* @dev Computes (base ^ exponent) % modulus over big numbers.``` [L5](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L5) (param and return tags are missing)
1. ```RRUtils.SignedSet memory rrset,``` [L227](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L227) (param rrset is missing)
1. ```bytes memory keyrdata,``` [L287](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L287) (param keyrdata is missing)
