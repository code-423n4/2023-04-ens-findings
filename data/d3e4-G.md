# ECDSA signature verification optimizations
Verifying ECDSA signatures is a crucial component of the protocol, but extremely costly in gas, especially as it is currently implemented. This gas cost presents a significant barrier to ENS domain registration.
We can make significant improvements to this. The gas savings achieved here outweigh any other gas savings in the contracts by a wide margin. The docs quote a figure of [1,663,953 gas](https://docs.ens.domains/contract-api-reference/dns-registrar#gas-cost) (currently about 0.07 ETH, or $130 USD) for domain registration, most of which is due to signature verification. With the below techniques we can expect to reduce this by some 60%.

## Double scalar multiplication optimizations
ECDSA signature verification is performed by `EllipticCurve.verifySignature()`. Given a signature $(r,s)$, an elliptic curve base point $G$, public key $Q$ and a (hashed) message $m$, verifying the signature involves checking that the x-coordinates of $s^{-1}(mG + rQ)$ and $(r,s)$ are the same.
This double scalar multiplication is the expensive part of the algorithm. Currently each term is calculated separately using the operations doubling (DBL) and adding 1 (ADD), and then the two are added. For example, for 434 (110110010 in binary), $434Q$ would be calculated as
```
1   Q
    2Q      DBL
1   3Q      ADD
    6Q      DBL
0           -
    12Q     DBL
1   13Q     ADD
    26Q     DBL
1   27Q     ADD
    54Q     DBL
0           -
    108Q    DBL
0           -
    216Q    DBL
1   217Q    ADD
    434Q    DBL
0           -
```
Thus, for a bit-length of N, this scalar multiplication algorithm requires N-1 DBL and, on average, N/2 ADD (the number of non-zero bits). For the double scalar multiplication we need 2(N-1) DBL and, on average, about N ADD.

### Shamir's trick
A simple first improvement is to use Shamir's trick for double scalar multiplication. The trick is to double the sum of the two terms together and add either $G$, $Q$ or $G + Q$ (or nothing). An example is sufficient to explain this.
$189G + 434Q$ is calculated as follows. Note that 189 in binary is 10111101.

Pre-compute G + Q. Now we calculate
```
189 434
0   1     0G +    Q
          0G +   2Q     DBL
1   1     1G +   3Q     ADD G + Q
          2G +   6Q     DBL
0   0                   -
          4G +  12Q     DBL
1   1     5G +  13Q     ADD G + Q
         10G +  26Q     DBL
1   1    11G +  27Q     ADD G + Q
         22G +  54Q     DBL
1   0    23G +  54Q     ADD G
         46G + 108Q     DBL
1   0    47G + 108Q     ADD G
         94G + 216Q     DBL
0   1    94G + 217Q     ADD     Q
        188G + 434Q     DBL
1   0   189G + 434Q     ADD G
```
We see that we now only need N-1 DBL and, on average, about N/2 ADD. Since the double scalar multiplication accounts for most of the gas cost for verification, with this alone we can cut the gas cost almost in half!

### Hybrid Binary-Ternary Joint Sparse Form representation
The doubling and adding method can be improved by also allowing tripling (achieved by first doubling x and then adding x). For example, instead of computing $27Q$ in the sequence $1Q, 2Q, 3Q, 6Q, 12Q, 13Q, 26Q, 27Q$, using 4 DBL and 3 ADD, we can calculate it as $Q, 2Q, 3Q, 6Q, 9Q, 18Q, 27Q$, tripling three times by adding 1Q to itself twice to get 3Q, and then add 3Q to itself twice to get $9Q$, and finally add $9Q$ to itself twice to get $27Q$, which uses only 3 DBL and 3 ADD. There are many possible paths of doubling, tripling and adding which all arrive at the same result.
This can be applied to the double scalar multiplication as well, by finding a path suitable for both numbers simultaneously. Finding the optimal such path is hard, but reducing the number of ADD to 0.32N can be achieved (still N DBL). See [this](https://eprint.iacr.org/2008/285.pdf) for details.

This representation can be calculated off-chain, and then passed as a 2xN byte array along with the signature to `verifySignature()` where it determines the sequence of doublings, triplings, and additions. Some precomputations must also be performed.

### Reducing the bit length of the double scalar
Instead of calculating $R := (x,y) = s^{-1}(mG + rQ)$ and checking that $r = x$, we can rewrite the condition as
$vmG + vrQ - vsR = \mathcal O$,
where $\mathcal O$ is the identity element, and where we have multiplied the equation by a judiciously chosen $v$.
$v$ is chosen such that $vr$ and $vs$ are as small as possible, with bit length at most half of N. This amounts to finding the shortest vector in a modular lattice. See [this](https://www.sciencedirect.com/science/article/pii/030439759200021I) for an algorithm.

Note that $G$ is known and always the same. Therefore we can store precomputed values for $G, 2G, 4G, ..., 255G$ in the contract. Possibly taking the complement of vm to reduce the number of non-zero bits, we only need to do about N/3 ADD.

To calculate $vrQ - vsR$ we can use the techniques mentioned above. $R$ can be reconstructed from $r$, which is already provided, but the y-coordinate may also be computed off-chain and passed into the function.
Since $vr$ and $vs$ now have bit lengths of at most N/2 we only need to perform N/2 DBL and 0.16N ADD. Adding the calculation of vmG we have a final total of N/2 DBL and 0.4N ADD.

Since the double scalar multiplication is responsible for most of the computational effort, using the above techniques we can expect a gas saving per verification of some 60%.

Note that the optimizations are mostly about relegating computations off-chain. We can therefore spend considerable resources on finding optimal results there, and if in future better such optimizations are found (such as an optimal binary-ternary path or (vr,vs) with fewer non-zero bits), the contract can, without changes, immediately make use of them.

### Double and add can be combined
Let $P = (x_P, y_P)$, $Q = (x_Q, y_Q)$. The sum of $P$ and $Q$ is $S = P + Q = (x_S, y_S)$, where
$\lambda_S = \frac{y_Q − y_P}{x_Q − x_P}$
$x_S = \lambda_S^2 − x_Q − x_P$
$y_S = (x_P − x_S)\lambda_S − y_P$.
The double of $P$ is $2P=(x_2P, y_2P)$, where the derivative is instead
$\lambda_{2P} = \frac{3x_P^2 + a}{2y_P}$.
Denoting field modular inverse by $I$ and multiplication by $M$, the cost of point addition is $3M+1I$ and of doubling $4M+1I$. If we calculate $2P + Q$ as a doubling followed by an addition the cost if $7M+2I$.
But if we combine the two into one operation by writing $2P + Q$ as $P+(P+Q)=P+S$ we observe that
$\lambda_{P+S} = \frac{y_S − y_P}{x_S − x_P} = \frac{2y_P}{x_P − x_S} - \lambda_S$
$x_{P+S} = \lambda_S^2 − x_P − x_S$
$y_{P+S} = (x_P − x_S)\lambda_S − y_P$.
We can thus skip the calculation of $y_S$. The cost of this combined operation is now only $5M+2I$.

## Compute modular inverse off-chain
`EllipticCurve.inverseMod()` is used twice in `EllipticCurve.verifySignature()`. This value can be computed off-chain and then, in `verifySignature()` simply checked that it is the inverse.

## Use affine coordinates with value checks of inverse computations instead of projective coordinates
The above suggestion to compute inverses off-chain can be exploited even further. The current implementation uses projective coordinates. The purpose of this is to defer divisions until the very end, to avoid having to compute modular inverses. But this adds unnecessary complexity and other overhead instead. In our case we can simply relegate all inverse computations off-chain and have the caller provide `verifySignature()` with all the resulting values, i.e. instead of calculating $z = x/y$, the signer provides $z$ (calculated off-chain) and we simply check that $zx = y$.



# Other gas optimizations

## Simplify name comparison check
[RRUtils.compareNames()](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L275-L327) is only ever used to check for inequality at [DNSClaimChecker.sol#L34](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L34). `compareNames(self, other)` returns 0 iff `equals(self, other)` So this check can be reduced from
```solidity
if (iter.name().compareNames(buf.buf) != 0) continue;
```
to
```solidity
if (!iter.name().equals(buf.buf)) continue;
```
This saves about 9549 gas when comparing `_ens.foo.test` against `_ens.foo.tesu`.
Also, if the full comparison in `compareNames()` is not going to be used here, the function can be removed.

## More intuitive and cheaper `serialNumberGte()`
In `RRUtils.serialNumberGte()`
```solidity
unchecked {
    return int32(i1) - int32(i2) >= 0;
}
```
can be reformatted to
```solidity
unchecked {
    return i1 - i2 < 2**31;
}
```
This saves 5 gas per call, and is also much more intuitive.

## Remove unused functions
`EllipticCurve.multiplyPowerBase2()`
`EllipticCurve.multipleGeneratorByScalar()`
