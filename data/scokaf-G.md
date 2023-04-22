# 1: USE Keccak256 OVER Sha256 FOR STRING COMPARISON 

Optimization details

## Context:

Use Keccak256 Over Sha256 For String Comparison

For more info, see https://gist.github.com/alexon1234/5e8f4c335899a3398808bb96203bb982

## Proof of Concept

> ***File: SHA256Digest.sol***

        return sha256(data) == hash.readBytes32(0);

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/digests/SHA256Digest.sol#L17

## Tools Used

Manual Analysis

