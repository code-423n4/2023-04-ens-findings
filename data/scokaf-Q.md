# 1: GENERATE PERFECT CODE HEADERS EVERY TIME					

Vulnerability details

## Context:

We recommend using a header for Solidity code layout and readability

> ***Example***


 /*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/


For reference, see https://github.com/transmissions11/headers

## Proof of Concept

All contracts


## Tools Used

Manual Analysis


# 2: WORD & TYPING TYPOS

Vulnerability details

## Context:

Word & typing typos

## Proof of Concept

> ***File: DNSSECImpl.sol*** 

canonicalisation can be changed to canonicalization in the following comment.

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L135


## Tools Used

Manual Analysis


