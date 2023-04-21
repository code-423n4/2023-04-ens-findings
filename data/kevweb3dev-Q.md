[L-01]

The test coverage rate of the project is ~85%. With some files not having any test cases. Testing all functions is best practice in terms of security criteria.

Due to its capacity, test coverage is expected to be 100%.

|File                              |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
|----------------------------------|----------|----------|----------|----------|----------------|
| dnsregistrar\                    |    88.29 |    65.71 |    83.33 |    84.03 |                |
|  DNSClaimChecker.sol             |    90.48 |     62.5 |      100 |     91.3 |          60,63 |
|  OffchainDNSResolver.sol         |    97.62 |       75 |      100 |    94.12 |     91,119,126 |
|  RecordParser.sol                |        0 |        0 |        0 |        0 |... 34,37,38,39 |
|  DNSRegistrar.sol                |    87.18 |    67.86 |    66.67 |    83.93 |... 128,169,202 |
| dnssec-oracle\                   |     95.6 |       75 |    94.12 |    93.58 |                |
|  BytesUtils.sol                  |    94.74 |    66.67 |    94.12 |    92.86 |... 266,267,373 |
|  RRUtils.sol                     |      100 |    93.75 |      100 |      100 |                |
|  DNSSECImpl.sol                  |    92.86 |    78.57 |      100 |    88.17 |... 394,405,421 |
|  SHA1.sol                        |      100 |      100 |        0 |        0 |              7 |
| dnssec-oracle\algorithms\        |    79.09 |    45.16 |       80 |    84.04 |                |
|  RSASHA1Algorithm.sol            |      100 |       50 |      100 |    76.92 |       30,31,32 |
|  EllipticCurve.sol               |    73.86 |    44.44 |    70.59 |    84.21 |... 394,397,411 |
|  P256SHA256Algorithm.sol         |      100 |       50 |      100 |      100 |                |
|  ModexpPrecompile.sol            |      100 |      100 |      100 |      100 |                |
|  RSASHA256Algorithm.sol          |      100 |       50 |      100 |    76.92 |       29,30,31 |
|  RSAVerify.sol                   |      100 |      100 |      100 |      100 |                |
| dnssec-oracle\digests\           |      100 |      100 |      100 |      100 |                |
|  SHA1Digest.sol                  |      100 |      100 |      100 |      100 |                |
|  SHA256Digest.sol                |      100 |      100 |      100 |      100 |                |
| utils\                           |    96.26 |    81.48 |    88.24 |     95.6 |                |
|  NameEncoder.sol                 |      100 |      100 |      100 |      100 |                |
|  HexUtils.sol                    |      100 |      100 |      100 |      100 |                |
|----------------------------------|----------|----------|----------|----------|----------------|