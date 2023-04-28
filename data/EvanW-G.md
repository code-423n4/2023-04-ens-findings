[G-01] Use uncheck{}

In function `isSubdomainOf`, we can apply unchecked{} on count `counts-—` . This is because the less value can be returned from `labelCount()` is zero and if the value of counts is zero, it will be blocked by the while loop condition. Thus, underflow will not exist.

Also the same as the `counts` in line294 in `compareNames()`

```solidity
contracts/dnssec-oracle/RRUtils.sol
267: while (counts > othercounts) {
268:             off = progress(self, off);
269:             counts--
270:         }

294:            counts--;
```

Suggestion as an example:

```solidity
267: while (counts > othercounts) {
268:             off = progress(self, off);
269:             unchecked {counts--;}
270:         }
```

[G-02] Reduce duplicated code

For the `verify()` function on `RSASHA1Algorithm.sol`. We can define a memory variable to reduce gas fee of deployment.

It can also be implied to the `verify()` function on `RSASHA256Algorithm.sol`

```solidity
contracts/dnssec-oracle/algorethms/RSASHA1Algorithm.sol
24:     if (exponentLen != 0) {
               exponent = key.substring(5, exponentLen);
               modulus = key.substring(
                   exponentLen + 5,
                   key.length - exponentLen - 5
               );
           } else {
               exponentLen = key.readUint16(5);
               exponent = key.substring(7, exponentLen);
               modulus = key.substring(
                   exponentLen + 7,
                   key.length - exponentLen - 7
               );
37:        }
```

Suggestion:

```solidity
uint CONST_1;
if (exponentLen != 0) {
    CONST_1 = 5;
} else {
    CONST_1 = 7;
    exponentLen = key.readUint16(5);
}
exponent = key.substring(CONST_1, exponentLen);
modulus = key.substring(
    exponentLen + CONST_1,
    key.length - exponentLen - CONST_1
);
```

[Gas-03] Reduce unnecessary code

Reducing unnecessary code could save some deployment gas

```solidity
contracts/dnssec-oracle/algorethms/EllipticCurve.sol
function isZeroCurve(
        uint256 x0,
        uint256 y0
    ) internal pure returns (bool isZero) {
        if (x0 == 0 && y0 == 0) {
            return true;
        }
        return false;
    }
```

Suggestion:

```solidity
function isZeroCurve(
        uint256 x0,
        uint256 y0
    ) internal pure returns (bool isZero) {
        return x0 == 0 && y0 == 0;
    }
```

[Gas-04] No need to define memory variables if they won’t be reused

In order to save both deployment gas fee and execution gas fee, it’s suggested to only define memory variables if it will be reused.

```solidity
contracts/digests/SHA1Digest.sol
13: function verify(
          bytes calldata data,
          bytes calldata hash
      ) external pure override returns (bool) {
          require(hash.length == 20, "Invalid sha1 hash length");
          bytes32 expected = hash.readBytes20(0);
          bytes20 computed = SHA1.sha1(data);
          return expected == computed;
21:   }
```

Suggestion:

```solidity
function verify(
        bytes calldata data,
        bytes calldata hash
    ) external pure override returns (bool) {
        require(hash.length == 20, "Invalid sha1 hash length");
        return hash.readBytes20(0) == SHA1.sha1(data);
    }
```