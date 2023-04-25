## Low 1: `DNSRegistrar` contract won't accept any proofs/claims after year 2106
Due to the inception and expiration fields of signed sets being 32-bit UNIX timestamps, see [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L101-L102) and also [RFC4034](https://www.ietf.org/rfc/rfc4034.txt), signature validation will fail after *Feb 07 2106* because of inception and expiration timestamp checks. Therefore the `DNSRegistrar` contract won't accept any new proofs/claims after this date.  
Moreover, upgrading the `DNSRegistrar` and related contracts to solve this in the future might lead to problems because [inceptions of claimed domains](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L32) are currently stored as `uint32`. Although DNSSEC uses 32-bit UNIX timestamps, I recommend to work with `uint40` in the ENS-DNS contracts to be future-proof.


## Non-critical 1: Consistent use of bytes20 instead of bytes32
In the [verify()](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/digests/SHA1Digest.sol#L13-L21) method of the `SHA1Digest` contract, the bytes20 return value of `hash.readBytes20(0)` is stored in a bytes32 variable just be compared to a bytes20 value again.  
  
Use bytes20 consistenly:
```diff
diff --git a/contracts/dnssec-oracle/digests/SHA1Digest.sol b/contracts/dnssec-oracle/digests/SHA1Digest.sol
index 97e1247..63c1c94 100644
--- a/contracts/dnssec-oracle/digests/SHA1Digest.sol
+++ b/contracts/dnssec-oracle/digests/SHA1Digest.sol
@@ -15,7 +15,7 @@ contract SHA1Digest is Digest {
         bytes calldata hash
     ) external pure override returns (bool) {
         require(hash.length == 20, "Invalid sha1 hash length");
-        bytes32 expected = hash.readBytes20(0);
+        bytes20 expected = hash.readBytes20(0);
         bytes20 computed = SHA1.sha1(data);
         return expected == computed;
     }
```


## Non-critical 2: Delete unused constants
Remove the unused constants `CLASS_INET` and `TYPE_TXT` from the [DNSClaimChecker](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17) contract:

```diff
diff --git a/contracts/dnsregistrar/DNSClaimChecker.sol b/contracts/dnsregistrar/DNSClaimChecker.sol
index 54950d1..bd51694 100644
--- a/contracts/dnsregistrar/DNSClaimChecker.sol
+++ b/contracts/dnsregistrar/DNSClaimChecker.sol
@@ -13,9 +13,6 @@ library DNSClaimChecker {
     using RRUtils for *;
     using Buffer for Buffer.buffer;

-    uint16 constant CLASS_INET = 1;
-    uint16 constant TYPE_TXT = 16;
-
     function getOwnerAddress(
         bytes memory name,
         bytes memory data
```
