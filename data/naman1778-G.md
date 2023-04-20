## Use assembly to write address storage values

When writing value for variables whose type is address, make use of assembly code instead of solidity code.

    File: contracts/dnsregistrar/DNSRegistrar.sol

        previousRegistrar = _previousRegistrar;
        resolver = _resolver;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;

        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }

        function testGas() public {
            c0.setOwnerAssembly(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
            c1.setOwner(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
        }
    }

    contract Contract0 {

        address owner;
        function setOwnerAssembly(address _owner) public {
            assembly{
                sstore(owner.slot,_owner)
            }
        }

    }

    contract Contract1 {
        address owner;
        function setOwner(address _owner) public {
            owner = _owner;
        }

    }

#### Gas Report

    |  Contract0 contract                       |                 |       |        |       |         |
    |-------------------------------------------|-----------------|-------|--------|-------|---------|
    | Deployment Cost                           | Deployment Size |       |        |       |         |
    | 35287                                     | 207             |       |        |       |         |
    | Function Name                             | min             | avg   | median | max   | # calls |
    | setOwnerAssembly                          | 22324           | 22324 | 22324  | 22324 | 1       |


    |  Contract1 contract                       |                 |       |        |       |         |
    |-------------------------------------------|-----------------|-------|--------|-------|---------|
    | Deployment Cost                           | Deployment Size |       |        |       |         |
    | 48499                                     | 273             |       |        |       |         |
    | Function Name                             | min             | avg   | median | max   | # calls |
    | setOwner                                  | 22363           | 22363 | 22363  | 22363 | 1       |

## Use assembly to check for *address(0)*

Checking zero address can be improved by replacing the require statement with Assembly.Solidity has a lot of guardrails that can be removed (with care) for optimization purposes, especially for simple functionality like checking if an address is zero.

    File: contracts/dnsregistrar/OffchainDNSResolver.sol

            if (dnsresolver != address(0)) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

    File: contracts/dnsregistrar/DNSRegistrar.sol

        if (addr != address(0)) {

        if (owner == address(0) || owner == previousRegistrar) {

            if (resolver == address(0)) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

        if (address(algorithm) == address(0)) {

        if (address(digests[digesttype]) == address(0)) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.solidity_notZero(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
            c1.assembly_notZero(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
        }
    }


    contract Contract0 {

        error ZeroAddress();

        function solidity_notZero(address toCheck) public pure returns(bool success) {
            if(toCheck == address(0)) revert ZeroAddress();

            return true;
        }
    }

    contract Contract1{

    error ZeroAddress();
    function assembly_notZero(address toCheck) public pure returns(bool success) {
        assembly {
            if iszero(toCheck) {
                let ptr := mload(0x40)
                mstore(ptr, 0xd92e233d00000000000000000000000000000000000000000000000000000000) // selector for `ZeroAddress()`
                revert(ptr, 0x4)
            }
        }
        return true;
    }
    }

#### Gas Report

    | src/test/GasTest.t.sol:Contract0 contract |                 |     |        |     |         |
    |-------------------------------------------|-----------------|-----|--------|-----|---------|
    | Deployment Cost                           | Deployment Size |     |        |     |         |
    | 55905                                     | 311             |     |        |     |         |
    | Function Name                             | min             | avg | median | max | # calls |
    | solidity_notZero                          | 323             | 323 | 323    | 323 | 1       |


    | src/test/GasTest.t.sol:Contract1 contract |                 |     |        |     |         |
    |-------------------------------------------|-----------------|-----|--------|-----|---------|
    | Deployment Cost                           | Deployment Size |     |        |     |         |
    | 50099                                     | 281             |     |        |     |         |
    | Function Name                             | min             | avg | median | max | # calls |
    | assembly_notZero                          | 317             | 317 | 317    | 317 | 1       |

## Use nested *if* and, avoid multiple check combinations

Using nesting is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

    File: contracts/dnsregistrar/OffchainDNSResolver.sol

        178: if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

        128: if (x0 == 0 && y0 == 0) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;

        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }

        function testGas() public {
            c0.checkAge(19);
            c1.checkAgeOptimized(19);
        }
    }

    contract Contract0 {

        function checkAge(uint8 _age) public returns(string memory){
            if(_age>18 && _age<22){
                return "Eligible";
            }
        }

    }

    contract Contract1 {

        function checkAgeOptimized(uint8 _age) public returns(string memory){
            if(_age>18){
                if(_age<22){
                    return "Eligible";
                }
            }
        }
    }

#### Gas Report

    | Contract0 contract                        |                 |     |        |     |         |
    |-------------------------------------------|-----------------|-----|--------|-----|---------|
    | Deployment Cost                           | Deployment Size |     |        |     |         |
    | 76923                                     | 416             |     |        |     |         |
    | Function Name                             | min             | avg | median | max | # calls |
    | checkAge                                  | 651             | 651 | 651    | 651 | 1       |


    | Contract1 contract                        |                 |     |        |     |         |
    |-------------------------------------------|-----------------|-----|--------|-----|---------|
    | Deployment Cost                           | Deployment Size |     |        |     |         |
    | 76323                                     | 413             |     |        |     |         |
    | Function Name                             | min             | avg | median | max | # calls |
    | checkAgeOptimized                         | 645             | 645 | 645    | 645 | 1       |

## Use != 0 instead of > 0 for unsigned integer comparison

    File: contracts/dnssec-oracle/RRUtils.sol	

    304: while (counts > 0 && !self.equals(off, other, otheroff)) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    361: while (scalar > 0) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

## Instead of counting down in *for* statements, count up

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

    File: contracts/dnssec-oracle/BytesUtils.sol

    77: for (uint256 idx = 0; idx < shortest; idx += 32) {

    341: for (uint256 i = 0; i < len; i++) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/RRUtils.sol

    384: for (uint256 i = 0; i < data.length + 31; i += 32) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    325: for (uint256 i = 0; i < exp; i++) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    118: for (uint256 i = 0; i < input.length; i++) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.AddNum();
            c1.AddNum();
        }
    }


    contract Contract0 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=0;i<=9;i++){
                _num = _num +1;
            }
            num = _num;
        }
    }


    contract Contract1 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=9;i>=0;i--){
                _num = _num +1;
            }
            num = _num;
        }
    }

#### Gas Report

    | Contract0 contract                        |                 |      |        |      |         |
    |-------------------------------------------|-----------------|------|--------|------|---------|
    | Deployment Cost                           | Deployment Size |      |        |      |         |
    | 77011                                     | 311             |      |        |      |         |
    | Function Name                             | min             | avg  | median | max  | # calls |
    | AddNum                                    | 7040            | 7040 | 7040   | 7040 | 1       |

    | Contract1 contract                        |                 |      |        |      |         |
    |-------------------------------------------|-----------------|------|--------|------|---------|
    | Deployment Cost                           | Deployment Size |      |        |      |         |
    | 73811                                     | 295             |      |        |      |         |
    | Function Name                             | min             | avg  | median | max  | # calls |
    | AddNum                                    | 3819            | 3819 | 3819   | 3819 | 1       |

## Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

    File: contracts/dnssec-oracle/RRUtils.sol

    381: require(data.length <= 8192, "Long keys not permitted");
     
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    33: require(data.length == 64, "Invalid p256 signature length");

    40: require(data.length == 68, "Invalid p256 key length");

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    File: contracts/dnssec-oracle/digests/SHA1Digest.sol

    17: require(hash.length == 20, "Invalid sha1 hash length");

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

    File: contracts/dnssec-oracle/digests/SHA256Digest.sol

    16: require(hash.length == 32, "Invalid sha256 hash length");

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

## State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    420: if (address(digests[digesttype]) == address(0)) {

    423: return digests[digesttype].verify(data, digest);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## Avoid using state variable in emit (130 gas)

Using a state variable in SetOwner emits wastes gas.

    File: contracts/dnsregistrar/DNSRegistrar.sol
   
    66: emit NewPublicSuffixList(address(suffixes));

    82: emit NewPublicSuffixList(address(suffixes));

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

