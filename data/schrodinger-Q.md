### Low Risk Issues
| Count | Explanation | Instances |
|:--:|:-------|:--:|
| [L-01] | missing sanity checks for the following functions | 2 |
| [L-02] | missing zero address checks while assigning variables | 2 |
| Total Low Risk Issues | 4 |
|:--:|:--:|

### [L-01]  missing sanity checks for the following functions  
The following functions dosent perform a sanity checks on the params which might lead to a silent revert
```solidity
function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
        // Copy word-length chunks while possible
        for (; len >= 32; len -= 32) {
            assembly {
                mstore(dest, mload(src))
            }
            dest += 32;
            src += 32;
        }

        // Copy remaining bytes
        unchecked {
            uint256 mask = (256 ** (32 - len)) - 1;
            assembly {
                let srcpart := and(mload(src), not(mask))
                let destpart := and(mload(dest), mask)
                mstore(dest, or(destpart, srcpart))
            }
        }
    }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L273
mitigation: use a if/require command to perform the check `len<src.length && src.length>0`
```solidity 
        function find(
        bytes memory self,
        uint256 off,
        uint256 len,
        bytes1 needle
    ) internal pure returns (uint256) {
        for (uint256 idx = off; idx < off + len; idx++) {   
            if (self[idx] == needle) {
                return idx;
            }
        }
        return type(uint256).max;
    }
}
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L387
mitigation: use a if/require command to perform the check `off<self.length`

### [L-02]  missing zero address checks while assigning variables 
```solidity
    constructor(
        address _previousRegistrar,
        address _resolver,
        DNSSEC _dnssec,
        PublicSuffixList _suffixes,
        ENS _ens
    ) {
        previousRegistrar = _previousRegistrar;
        resolver = _resolver;
        oracle = _dnssec;
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
        ens = _ens;
    }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L56
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L57