# Summary
|ID     | Finding|  Gas saved | Instances|
|:----: | :---           |  :----:         |:----:         |
|G-01      |Static array is cheaper than dynamic array | 180 |  1 |
|G-02      |Modifier onlyOwner can be more gas efficiënt | 8700 |  1 |
|G-03      |Use shift left instead of multiplying| 28 |  1 |
|G-04      |Only append once to buffer| 10 |  2|
|G-05      |`next()` can be made more gas efficiënt| 532 |  1 |
# Details
## [G-01] Static array is cheaper than dynamic array
When the size of an array is known before hand it can be made static to save gas. In the [`resolve()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L49-L63) function, the array has a length of 1. Around 180 gas can be saved with this optimization.

[OffchainDNSResolver](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol)
```diff
L14:
error OffchainLookup(
    address sender,
-   string[] urls,
+   string[1] urls,
    bytes callData,
    bytes4 callbackFunction,
    bytes extraData
);

L49
    function resolve(
        bytes calldata name,
        bytes calldata data
    ) external view returns (bytes memory) {
-       string[] memory urls = new string[](1);
-       urls[0] = gatewayURL;
+       string[1] memory urls = [gatewayURL];

        revert OffchainLookup(
            address(this),
            urls,
            abi.encodeCall(IDNSGateway.resolve, (name, TYPE_TXT)),
            OffchainDNSResolver.resolveCallback.selector,
            abi.encode(name, data)
        );
    }
```
## [G-02] Modifier onlyOwner can be more gas efficiënt
The onlyOwner modifier in [DNSRegistrar](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78) always checks the owner of ens and root in the modifier itself. Assuming the owner rarely changes it's better to set the owner in the constructor and have an additional setter function for when it changes. 

[DNSRegistrar](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol): 8700 gas can be saved for the functions that uses onlyOwner
```diff
L31:
+    address private owner;

L67: in constructor
+    Root root = Root(ens.owner(bytes32(0)));
+    owner = root.owner();

L73:
    modifier onlyOwner() {
-       Root root = Root(ens.owner(bytes32(0)));
-       address owner = root.owner();
        require(msg.sender == owner);
        _;
    }

Additional setter function
    function setOwner(){
        Root root = Root(ens.owner(bytes32(0)));
        owner = root.owner();
    }
```

## [G-03] Use shift left instead of multiplying
You can use the bitwise left shift operator instead of multiplying when the number is a power of 2. 

1. Multiplying by 2: x << 1
1. Multiplying by 4: x << 2
1. Multiplying by 8: x << 3
And so on

This optimization can be used in the [`computeKeytag()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L390) function, instead of multiplying by 8 you can use shift left by 3. This will around 28 gas for the functions `proveAndClaim()` and `proveAndClaimWithResolver()`.
```diff
-   uint256 unused = 256 - (data.length - i) * 8;
+   uint256 unused = 256 - ((data.length - i) << 3);
```

## [G-04] Only append once to buffer
The buffer in [DNSClaimChecker](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L24-L27) is appended twice. However if you use abi.encodepacked you can append once with the same result. Around 10 gas saved for the functions `proveAndClaim()` and `proveAndClaimWithResolver()`

```diff
        Buffer.buffer memory buf;
        buf.init(name.length + 5);
-       buf.append("\x04_ens");
-       buf.append(name);
+       buf.append(abi.encodePacked("\x04_ens", name));
```

Same optimization can be made at: [DNSSECImpl.sol#L399-L400](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L399-L400)
## [G-05] `next()` can be made more gas efficiënt
The `next()` function can be more gas optimized if you optimize the offsett. Gas will be saved on every iteration of a for loop that uses the `next()` function. For example in the tests the average gas saved is:
- proveAndClaim(): 532
- proveAndClaimWithResolver(): 532
[RRUtils.sol#L158-L180](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L158-L180):
```diff
    function next(RRIterator memory iter) internal pure {
        iter.offset = iter.nextOffset;
        if (iter.offset >= iter.data.length) {
            return;
        }

        // Skip the name
        uint256 off = iter.offset + nameLength(iter.data, iter.offset);

        // Read type, class, and ttl
        iter.dnstype = iter.data.readUint16(off);
-       off += 2;
-       iter.class = iter.data.readUint16(off);
+       iter.class = iter.data.readUint16(off + 2);
-       off += 2;
-       iter.ttl = iter.data.readUint32(off);
+       iter.ttl = iter.data.readUint32(off + 4);
-       off += 4;

        // Read the rdata
-       uint256 rdataLength = iter.data.readUint16(off);
+       uint256 rdataLength = iter.data.readUint16(off + 8);
-       off += 2;
+       off += 10;
        iter.rdataOffset = off;
        iter.nextOffset = off + rdataLength;
    }
```