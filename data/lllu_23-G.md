# [G-01] require()`/`revert()` strings longer than 32 bytes cost extra gas
## `require()`/`revert()` strings longer than 32 bytes cost extra gas Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

There are 3 instances of this issue:

`
11:  require(
12:  controllers[msg.sender],
13:  "Controllable: Caller is not a controller"
14:  );
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/root/Controllable.sol#L11-L14

`
42:  require(
43:  addr == msg.sender ||
44:  controllers[msg.sender] ||
45:  ens.isApprovedForAll(addr, msg.sender) ||
46:  ownsContract(addr),
47:  "ReverseRegistrar: Caller is not a controller or authorised by address or the address itself"
48:  );
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/reverseRegistrar/ReverseRegistrar.sol#L42-#L48

`
53:  require(
54:  address(resolver) != address(0),
55:  "ReverseRegistrar: Resolver address must not be 0"
56:  );
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/reverseRegistrar/ReverseRegistrar.sol#L53-#L56

# [G-02]`<x> += <y>` Costs More Gas Than ` <x> = <x> + <y>` For State Variables

## References:
https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8

There are 1 instances of this issue:

`
dest += 32;
src += 32;
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L279-#L280


# [G-03]Use constants instead of type(uintx).max
## `type(uint256).max` uses more gas in the distribution process and also for each transaction than constant usage.

There are 3 instances of this issue:

`398: return type(uint256).max;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L398

`
24:  if (separator == type(uint256).max)
25:  return ("", "", type(uint256).max);
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L24-#L25

` 
33:  if (terminator == type(uint256).max) `
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L33


# [G-04]Use hardcode address instead `address(this)`
## Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the hardcoded`address`. Foundry’s `script.sol` and solmate’s `LibRlp.sol` contracts can help achieve this.

### References:
https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824

There are 2 instances of this issue:

`61:  addr = address(this);`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistryWithFallback.sol#L61

`57:  address(this)`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L57

# [G-05]Use double `require` instead of using `&&`
## Using double require instead of operator && can save more gas. When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

There are 2 instances of this issue:
`
154:  self.length == offset + other.length &&
155:  equals(self, offset, other, 0, other.length);
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L148-#L156

Recommended Mitigation Steps
`
function equals(
  bytes memory self,
  uint256 offset,
  bytes memory other
) internal pure {
  require(
  self.length == offset + other.length,
  "Length of byte arrays do not match"
);
  require(
  equals(self, offset, other, 0, other.length),
  "Bytes are not equal"
  );
}
`

another instance
`
169:  self.length == other.length &&
170:  equals(self, 0, other, 0, self.length);
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L164-#L171


# [G-06]Help the optimizer by Saving a storage variable’s reference instead of repeatedly fetching it
## To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.The effect can be quite significant.

As an example, instead of repeatedly calling `someMap[someIndex]` 
save its reference like this: SomeStruct storage someStruct = `someMap[someIndex]` and use it.

There are 13 instances of this issue:

`20:  address owner = records[node].owner;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L20

`108:  records[node].resolver = resolver;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L108

`121:  records[node].ttl = ttl;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L121

`146:   address addr = records[node].owner;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L146

`162:  return records[node].resolver;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L162

`171:  return records[node].ttl;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L171

`182:  return records[node].owner != address(0x0);`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L182

`199:  records[node].owner = owner;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L199

`
207: if (resolver != records[node].resolver) {
208:  records[node].resolver = resolver;   
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L207-#L208

`
212:  if (ttl != records[node].ttl) {
213:  records[node].ttl = ttl;
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistry.sol#L212-#L213

`25:  if (!recordExists(node))`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistryWithFallback.sol#L25

`38:  if (!recordExists(node))`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistryWithFallback.sol#L38

`51:  if (!recordExists(node))`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENSRegistryWithFallback.sol#L51

# [G-07] Using `private` rather than `public` for constants to saves gas
## If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 3 instances of this issue:

` 
27:  uint16 constant DNSCLASS_IN = 1;
29:  uint16 constant DNSTYPE_DS = 43;
30:  uint16 constant DNSTYPE_DNSKEY = 48;
31:  uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27-#L32

`
72:  uint256 constant RRSIG_TYPE = 0;
73:  uint256 constant RRSIG_ALGORITHM = 2;
74:  uint256 constant RRSIG_LABELS = 3;
75:  uint256 constant RRSIG_TTL = 4;
76:  uint256 constant RRSIG_EXPIRATION = 8;
77:  uint256 constant RRSIG_INCEPTION = 12;
78:  uint256 constant RRSIG_KEY_TAG = 16;
79:  uint256 constant RRSIG_SIGNER_NAME = 18;
`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72-#L79

`
29:  uint16 constant CLASS_INET = 1;
30:  uint16 constant TYPE_TXT = 16;`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol


# [G-08]Optimize names to save gas
## Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because `22 gas` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

See more here.
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92


## Proof Of Concept
All in-scope contracts.

Recommended Mitigation Steps
Find a lower method ID name for the most called functions for example `Call()` vs .`Call1()` is cheaper by 22 gas.

For example, the function IDs in the `Gauge.sol` contract will be the most used; A lower method ID may be given.

# [G-09] Use solidity version 0.8.19 to gain some gas boost
## Upgrade to the latest solidity version 0.8.19 to get additional gas savings.

See latest release for reference:
https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/
