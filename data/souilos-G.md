## Use != 0 instead of > 0 for unsigned integer comparison 

------------------------------------------------------------------------ 

### Proof of concept 

Found in line 304 at 2023-04-ens/contracts/dnssec-oracle/RRUtils.sol:

        while (counts > 0 && !self.equals(off, other, otheroff)) {


Found in line 56 at 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol:

            if (t1 < 0) return (m - uint256(-t1));


Found in line 361 at 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol:

        while (scalar > 0) {


Found in line 95 at 2023-04-ens/contracts/resolvers/profiles/DNSResolver.sol:

        if (name.length > 0) {


Found in line 53 at 2023-04-ens/contracts/resolvers/profiles/ABIResolver.sol:

                abiset[contentType].length > 0


Found in line 55 at 2023-04-ens/contracts/wrapper/BytesUtils.sol:

        if (len > 0) {


Found in line 16 at 2023-04-ens/contracts/dnsregistrar/TLDPublicSuffixList.sol:

        return labellen > 0 && name.readUint8(labellen + 1) == 0;


Found in line 283 at 2023-04-ens/contracts/utils/UniversalResolver.sol:

        if (metaData.length > 0) {

------------------------------------------------------------------------ 

### Mitigation 

Use != 0 instead of > 0 & < 0 for unsigned integer comparison.