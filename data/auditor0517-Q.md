## [L-01] Lack of length validation in `BytesUtils.equals`

- https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L135-L137

```solidity
        return 
            keccak(self, offset, self.length - offset) ==
            keccak(other, otherOffset, other.length - otherOffset);
```
`BytesUtils.equals` only checks the hash values of two strings. We can add length validation for more safety, and other `equal` method (on line 164) checked the lengths.

## [L-02] Wrong comment about  `base32HexTable`

- https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L320

The comment says `base32HexTable` maps characters from 0x30 to 0x7A, but it actually maps from 0x30 to 0x76.

## [L-03] Wrong validation of character

- https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L343

```solidity
        require(char >= 0x30 && char <= 0x7A);
```

`base32HexTable` only maps characters from 0x30 to 0x76, so 0x7A is not correct here. 0x76 is the correct for current `base32HexTable`. We can also extend `base32HexTable` in case of using 0x7A.

## [L-04] Wrong validation of decoded value

- https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L345

```solidity
        require(decoded <= 0x20);
```

This validation is not correct. The correct validation is `decoded` < `0x20`.

## [L-05] u might be 0 after validation

- https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L42-L43

```solidity
            if (u == 0 || u == m || m == 0) return 0;
            if (u > m) u = u % m;
```

`EllipticCurve.inverseMod` gets a residue after validation. In `EllipticCurve`, m is p or n, and p\*2, n\*2 > 2^256.
So this is safe. But in general context, this is not safe. So it is better to swap these two lines.


## [L-06] EllipticCurve.validateSignature has wrong and needless code blocks

- https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L410-L415

```solidity
        if (P[2] == 0) {
            return false;
        }

        uint256 Px = inverseMod(P[2], p);
        Px = mulmod(P[0], mulmod(Px, Px, p), p);
```

Px = p[0] * (inverse(P[2]))^2 is not correct here. Fortunately, P[2] is always 1 after `addAndReturnProjectivePoint`, so there is no problem. We can safely remove the wrongly implemented blocks.









