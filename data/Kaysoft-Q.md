## [LC-01] AVOID THE USE OF SOLIDITY KEYWORD `now`

The `now` Solidity keyword for block.timestamp in the previous version of Solidity is used as input parameter to function. It is best practice to avoid naming variables with Solidity keywords.

Files: 
- [dnssec-oracle/DNSSEC.sol#L22](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSEC.sol#L22)
- [DNSSECImpl.sol#L35](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L35)
- [DNSSECImpl.sol#L36](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L36)
- [DNSSECImpl.sol#L122](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L122)
```
function verifyRRSet(
        RRSetWithSignature[] memory input,
@>        uint256 now //@audit use of keyword
    ) public view virtual returns (bytes memory rrs, uint32 inception);
```

## [NC-1] USE SPECIFIC NAMED NAMED IMPORTS
USE `import {Digest} "./Digest.sol";` instead of global imports like `import "./Digest.sol";`.

File: All files

## [NC-2] USE LATEST SOLIDITY STABLE VERSION
Consider using atleast Solidity version 0.8.17 as latest software versions have security updates and improvements.

Files: All files.
