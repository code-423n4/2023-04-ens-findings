# Gas Savings

## Immutable variables

### Description 

The following variable(s) could be made immutable:

- [`gatewayURL`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L39)

Immutable variables are replaced with literal values when contracts are deployed, which eliminates the gas cost of reading those variables from memory at runtime.

### Recommendation

Make the variable(s) listed above immutable with the `immutable` modifier.

## Unused functions

### Description 

The following functions are defined but unused:

- `multiplyPowerBase2` in [`EllipticCurve.sol`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L316)
- `multipleGeneratorByScalar ` in [`EllipticCurve.sol`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L377)

Unused functions increase the size of the codebase and can cause confusion when reading code.

### Recommendation

Consider removing functions that are not used.