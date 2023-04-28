### Prevent array out of bounds
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L179-L185

    function readUint8(
        bytes memory self,
        uint256 idx
    ) internal pure returns (uint8 ret) {
        require(idx < self.length);
        return uint8(self[idx]);
    }
```
### Ensure that the object being modified is correct
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64-#L67

    function setAlgorithm(uint8 id, Algorithm newAlgo, Algorithm oldAlgo) public owner_only {
        require(newAlgo != oldAlgo);
        require(algorithms[id] == oldAlgo);
        algorithms[id] = newAlgo;
        emit AlgorithmUpdated(id, address(newAlgo));
    }

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75-#L78

    function setDigest(uint8 id, Digest newDigest, Digest oldDigest) public owner_only {
        require(newDigest != oldDigest);
        require(digests[id] == oldDigest);
        digests[id] = newDigest;
        emit DigestUpdated(id, address(newDigest));
    }
```