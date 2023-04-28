## [N-01] constants should be defined rather than using magic numbers

Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals.

There are 233 instances of this issue in 16 files:

    File: contracts/dnsregistrar/DNSClaimChecker.sol
    /// @audit 5
    25: buf.init(name.length + 5);

    /// @audit 0x613d3078
    72: if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

    File: contracts/dnsregistrar/OffchainDNSResolver.sol

    /// @audit 5
    144: if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {

    /// @audit 5
    149: uint256 lastTxtIdx = txt.find(5, txt.length - 5, " ");

    /// @audit 5
    151: address dnsResolver = parseAndResolve(txt, 5, txt.length);

    /// @audit 5
    154: address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);

    /// @audit 1
    157: txt.substring(lastTxtIdx + 1, txt.length - lastTxtIdx - 1)

    /// @audit 1
    170: return data.substring(startIdx + 1, fieldLength);

    /// @audit 1
    178: if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

    /// @audit 2
    180: idx + 2,

    /// @audit 1
    217: parentNode = textNamehash(name, separator + 1, lastIdx);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

    File: contracts/dnsregistrar/RecordParser.sol

    /// @audit 1
    38: value = input.substring(separator + 1, terminator - separator - 1);

    /// @audit 1
    39: nextOffset = terminator + 1;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

    File: contracts/dnsregistrar/DNSRegistrar.sol

    /// @audit 1
    141: labelHash = name.keccak(1, labelLen);

    /// @audit 1
    145: name.length - labelLen - 1

    /// @audit 1
    183: bytes32 parentNode = _enableNode(domain, offset + len + 1);

    /// @audit 1
    184: bytes32 label = domain.keccak(offset + 1, len);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/BytesUtils.sol

    /// @audit 32
    20: ret := keccak256(add(add(self, 32), offset), len)

    /// @audit 32
    74: selfptr := add(self, add(offset, 32))
    
    /// @audit 32
    75: otherptr := add(other, add(otheroffset, 32))
    
    /// @audit 32
    77: for (uint256 idx = 0; idx < shortest; idx += 32) {
    
    /// @audit 32
    87: if (shortest - idx >= 32) {
    
    /// @audit 2, 8, 32, 1
    90: mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
    
    /// @audit 32
    95: selfptr += 32;
    
    /// @audit 32
    96: otherptr += 32;
    
    /// @audit 2, 0xFFFF
    198: ret := and(mload(add(add(self, 2), idx)), 0xFFFF)
    
    /// @audit 4, 0xFFFFFFFF
    214: ret := and(mload(add(add(self, 4), idx)), 0xFFFFFFFF)
    
    /// @audit 32
    228: require(idx + 32 <= self.length);
    
    /// @audit 32
    230: ret := mload(add(add(self, 32), idx))
    
    /// @audit 20
    244: require(idx + 20 <= self.length);
    
    /// @audit 32
    247: mload(add(add(self, 32), idx)),
    
    /// @audit 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF000000000000000000000000
    248: 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF000000000000000000000000
    
    /// @audit 32
    265: require(len <= 32);
    
    /// @audit 256, 32, 1
    268: let mask := not(sub(exp(256, sub(32, len)), 1))
    
    /// @audit 32
    269: ret := and(mload(add(add(self, 32), idx)), mask)
    
    /// @audit 32
    275: for (; len >= 32; len -= 32) {
    
    /// @audit 32
    279: dest += 32;
    
    /// @audit 32
    280: src += 32;
    
    /// @audit 256, 32, 1
    285: uint256 mask = (256 ** (32 - len)) - 1;
    
    /// @audit 32
    312: dest := add(ret, 32)
    
    /// @audit 32
    313: src := add(add(self, 32), offset)
    
    /// @audit 00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F
    323: hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";
    
    /// @audit 52
    337: require(len <= 52);
    
    /// @audit 0x30, 0x7A
    343: require(char >= 0x30 && char <= 0x7A);
    
    /// @audit 0x30
    344: decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);
    
    /// @audit 0x20
    345: require(decoded <= 0x20);
    
    /// @audit 1
    346: if (i == len - 1) {
    
    /// @audit 5
    349: ret = (ret << 5) | decoded;
    
    /// @audit 5
    352: uint256 bitlen = len * 5;
    
    /// @audit 5
    355: ret = (ret << 5) | decoded;
    
    /// @audit 8, 2
    356: } else if (len % 8 == 2) {
    
    /// @audit 3, 2
    358: ret = (ret << 3) | (decoded >> 2);
    
    /// @audit 2
    359: bitlen -= 2;
    
    /// @audit 8, 4
    360: } else if (len % 8 == 4) {
    
    /// @audit 1, 4
    362: ret = (ret << 1) | (decoded >> 4);
    
    /// @audit 4
    363: bitlen -= 4;
    
    /// @audit 8, 5
    364: } else if (len % 8 == 5) {
    
    /// @audit 4, 1
    366: ret = (ret << 4) | (decoded >> 1);
    
    /// @audit 1
    367: bitlen -= 1;
    
    /// @audit 8, 7
    368: } else if (len % 8 == 7) {
    
    /// @audit 2, 3
    370: ret = (ret << 2) | (decoded >> 3);
    
    /// @audit 3
    371: bitlen -= 3;
    
    /// @audit 256
    376: return bytes32(ret << (256 - bitlen));

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/RRUtils.sol	
    
    /// @audit 1
    27: idx += labelLen + 1;
    
    /// @audit 8192
    381: require(data.length <= 8192, "Long keys not permitted");
    
    /// @audit 31,32
    384: for (uint256 i = 0; i < data.length + 31; i += 32) {
    
    /// @audit 32
    387: word := mload(add(add(data, 32), i))
    
    /// @audit 8
    390: uint256 unused = 256 - (data.length - i) * 8;
    
    /// @audit 0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00
    395: 0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>
    
    /// @audit 8
    396: 8;
    
    /// @audit 0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF
    398: 0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
    
    /// @audit 0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF
    402: 0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +
    
    /// @audit 0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000
    404: 0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>
    
    /// @audit 16
    405: 16);
    
    /// @audit 0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF
    408: 0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +
    
    /// @audit 0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000
    410: 0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>
    
    /// @audit 16
    411: 16);
    
    /// @audit 8
    412: ac1 = (ac1 << 8) + ac2;
    
    /// @audit 0x00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF
    415: 0x00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF) +
    
    /// @audit 0xFFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000
    417: 0xFFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000) >>
    
    /// @audit 32
    418: 32);
    
    /// @audit 0x0000000000000000FFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF
    421: 0x0000000000000000FFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF) +
    
    /// @audit 0xFFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF0000000000000000
    423: 0xFFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF0000000000000000) >>
    
    /// @audit 64
    424: 64);
    
    /// @audit 0x00000000000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    427: 0x00000000000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF) +
    
    /// @audit 128
    428: (ac1 >> 128);
    
    /// @audit 16,0xFFFF
    429: ac1 += (ac1 >> 16) & 0xFFFF;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol
    
    /// @audit 4
    22: uint16 exponentLen = uint16(key.readUint8(4));
    
    /// @audit 5
    24: exponent = key.substring(5, exponentLen);
    
    /// @audit 5
    26: exponentLen + 5,
    
    /// @audit 5
    27: key.length - exponentLen - 5
    
    /// @audit 5
    30: exponentLen = key.readUint16(5);
    
    /// @audit 7
    31: exponent = key.substring(7, exponentLen);
    
    /// @audit 7
    33: exponentLen + 7,
    
    /// @audit 7
    34: key.length - exponentLen - 7
    
    /// @audit 20
    44: return ok && SHA1.sha1(data) == result.readBytes20(result.length - 20);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol
    
    /// @audit 1
    111: return (0, 1, 0);
    
    /// @audit 3
    174: u = mulmod(u, 2, p);
    
    /// @audit 3
    178: v = mulmod(v, 2, p);
    
    /// @audit 3
    181: t = mulmod(x0, 3, p);
    
    /// @audit 2
    188: x0 = mulmod(2, v, p);
    
    /// @audit 2
    195: y0 = mulmod(2, y0, p);
    
    /// @audit 1
    342: } else if (scalar == 1) {
    
    /// @audit 1
    350: uint256 base2Z = 1;
    
    /// @audit 1
    351: uint256 z1 = 1;
    
    /// @audit 2
    355: if (scalar % 2 == 0) {
    
    /// @audit 1
    359: scalar = scalar >> 1;
    
    /// @audit 2,1
    364: if (scalar % 2 == 1) {
    
    /// @audit 1
    368: scalar = scalar >> 1;
    
    /// @audit 1
    392: if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
    
    /// @audit 1
    396: if (!isOnCurve(Q[0], Q[1])) {
    
    /// @audit 1
    405: uint256 sInv = inverseMod(rs[1], n);
    
    /// @audit 1
    407: (x2, y2) = multiplyScalar(Q[0], Q[1], mulmod(rs[0], sInv, n));
    
    /// @audit 2
    414: uint256 Px = inverseMod(P[2], p);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
    
    /// @audit 64
    33: require(data.length == 64, "Invalid p256 signature length");
    
    /// @audit 32
    34: return [uint256(data.readBytes32(0)), uint256(data.readBytes32(32))];
    
    /// @audit 2
    39: ) internal pure returns (uint256[2] memory) {
    
    /// @audit 68
    40: require(data.length == 68, "Invalid p256 key length");
    
    /// @audit 4,36
    41: return [uint256(data.readBytes32(4)), uint256(data.readBytes32(36))];

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol
    
    /// @audit 5
    26: 5,
    
    /// @audit 32
    27: add(input, 32),
    
    /// @audit 32
    29: add(output, 32),

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

    File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol
    
    /// @audit 4
    21: uint16 exponentLen = uint16(key.readUint8(4));
    
    /// @audit 5
    23: exponent = key.substring(5, exponentLen);
    
    /// @audit 5
    25: exponentLen + 5,
    
    /// @audit 5
    26: key.length - exponentLen - 5
    
    /// @audit 5
    29: exponentLen = key.readUint16(5);
    
    /// @audit 7
    30: exponent = key.substring(7, exponentLen);
    
    /// @audit 7
    32: exponentLen + 7,
    
    /// @audit 7
    33: key.length - exponentLen - 7
    
    /// @audit 32
    43: return ok && sha256(data) == result.readBytes32(result.length - 32);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/RSAVerify.sol
    
    /// @audit 20
    17: require(hash.length == 20, "Invalid sha1 hash length");

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

    File: contracts/dnssec-oracle/digests/SHA256Digest.sol
    
    /// @audit  32
    16: require(hash.length == 32, "Invalid sha256 hash length");

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

    File: contracts/dnssec-oracle/SHA1.sol

    /// @audit 0x40
    9: let scratch := mload(0x40)

    /// @audit 32
    13: data := add(data, 32)

    /// @audit 1, 64
    16: let totallen := add(and(add(len, 1), 0xFFFFFFFFFFFFFFC0), 64)

    /// @audit 9
    17: switch lt(sub(totallen, len), 9)

    /// @audit 64
    19: totallen := add(totallen, 64)

    /// @audit 0x6745230100EFCDAB890098BADCFE001032547600C3D2E1F0
    22: let h := 0x6745230100EFCDAB890098BADCFE001032547600C3D2E1F0

    /// @audit 32
    29: if lt(count, 32) {

    /// @audit 256, 32, 1
    30: let mask := not(sub(exp(256, sub(32, count)), 1))

    /// @audit 64
    39: i := add(i, 64)

    /// @audit 32
    42: mstore(add(scratch, 32), readword(data, add(i, 32), len))

    /// @audit 64
    45: switch lt(sub(len, i), 64)

    /// @audit 0x80
    47: mstore8(add(scratch, sub(len, i)), 0x80)

    /// @audit 64
    51: switch eq(i, sub(totallen, 64))

    /// @audit 32
    54: add(scratch, 32),

    /// @audit 8
    55: or(mload(add(scratch, 32)), mul(len, 8))

    /// @audit 64
    61: let j := 64

    /// @audit 128
    62: } lt(j, 128) {

    /// @audit 12
    63: j := add(j, 12)

    /// @audit 12
    67: mload(add(scratch, sub(j, 12))),

    /// @audit 32
    68: mload(add(scratch, sub(j, 32)))

    /// @audit 56
    71: mload(add(scratch, sub(j, 56))),

    /// @audit 64
    72: mload(add(scratch, sub(j, 64)))

    /// @audit 2
    77: mul(temp, 2),

    /// @audit 0xFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFE
    78: 0xFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFE

    /// @audit 0x80000000
    81: div(temp, 0x80000000),

    /// @audit 0x0000000100000001000000010000000100000001000000010000000100000001
    82: 0x0000000100000001000000010000000100000001000000010000000100000001

    /// @audit 128
    88: let j := 128

    /// @audit 320
    89: } lt(j, 320) {

    /// @audit 24
    90: j := add(j, 24)

    /// @audit 24
    94: mload(add(scratch, sub(j, 24))),

    /// @audit 64
    95: mload(add(scratch, sub(j, 64)))

    /// @audit 112
    98: mload(add(scratch, sub(j, 112))),

    /// @audit 128
    99: mload(add(scratch, sub(j, 128)))

    /// @audit 4
    104: mul(temp, 4),

    /// @audit 0xFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFC
    105: 0xFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFC

    /// @audit 0x40000000
    108: div(temp, 0x40000000),

    /// @audit 0x0000000300000003000000030000000300000003000000030000000300000003
    109: 0x0000000300000003000000030000000300000003000000030000000300000003

    /// @audit 80 
    120: } lt(j, 80) {

    /// @audit 1
    121: j := add(j, 1)

    /// @audit 20
    123: switch div(j, 20)

    /// @audit 0x100000000000000000000
    127: div(x, 0x100000000000000000000),

    /// @audit 0x10000000000
    128: div(x, 0x10000000000)

    /// @audit 0x1000000000000000000000000000000
    130: f := and(div(x, 0x1000000000000000000000000000000), f)

    /// @audit 0x10000000000
    131: f := xor(div(x, 0x10000000000), f)

    /// @audit 0x5A827999
    132: k := 0x5A827999

    /// @audit 0x1000000000000000000000000000000
    137: div(x, 0x1000000000000000000000000000000),

    /// @audit 0x100000000000000000000
    138: div(x, 0x100000000000000000000)

    /// @audit 0x10000000000
    140: f := xor(div(x, 0x10000000000), f)

    /// @audit 0x6ED9EBA1
    141: k := 0x6ED9EBA1

    /// @audit 0x1000000000000000000000000000000
    146: div(x, 0x1000000000000000000000000000000),

    /// @audit 0x100000000000000000000
    147: div(x, 0x100000000000000000000)

    /// @audit 0x10000000000
    149: f := and(div(x, 0x10000000000), f)

    /// @audit 0x1000000000000000000000000000000
    152: div(x, 0x1000000000000000000000000000000),

    /// @audit 0x100000000000000000000
    153: div(x, 0x100000000000000000000)

    /// @audit 0x8F1BBCDC
    157: k := 0x8F1BBCDC

    /// @audit 0x1000000000000000000000000000000
    162: div(x, 0x1000000000000000000000000000000),

    /// @audit 0x100000000000000000000
    163: div(x, 0x100000000000000000000)

    /// @audit 0x10000000000
    165: f := xor(div(x, 0x10000000000), f)

    /// @audit 0xCA62C1D6
    166: k := 0xCA62C1D6

    /// @audit 0x80000000000000000000000000000000000000000000000
    172: 0x80000000000000000000000000000000000000000000000

    /// @audit 0x1F
    174: 0x1F

    /// @audit 0x800000000000000000000000000000000000000
    178: div(x, 0x800000000000000000000000000000000000000),

    /// @audit 0xFFFFFFE0
    179: 0xFFFFFFE0

    /// @audit 0xFFFFFFFF
    184: temp := add(and(x, 0xFFFFFFFF), temp)
 
    /// @audit 4
    188: mload(add(scratch, mul(j, 4))),
 
    /// @audit 0x100000000000000000000000000000000000000000000000000000000
    189: 0x100000000000000000000000000000000000000000000000000000000

    /// @audit 0x10000000000
    194: div(x, 0x10000000000),

    /// @audit 0x10000000000000000000000000000000000000000
    195: mul(temp, 0x10000000000000000000000000000000000000000)

    /// @audit 0xFFFFFFFF00FFFFFFFF000000000000FFFFFFFF00FFFFFFFF
    200: 0xFFFFFFFF00FFFFFFFF000000000000FFFFFFFF00FFFFFFFF

    /// @audit 0x4000000000000, 0xC0000000
    204: and(div(x, 0x4000000000000), 0xC0000000),

    /// @audit 0x400000000000000000000, 0x3FFFFFFF
    205: and(div(x, 0x400000000000000000000), 0x3FFFFFFF)

    /// @audit 0x100000000000000000000
    207: 0x100000000000000000000

    /// @audit 0xFFFFFFFF00FFFFFFFF00FFFFFFFF00FFFFFFFF00FFFFFFFF
    214: 0xFFFFFFFF00FFFFFFFF00FFFFFFFF00FFFFFFFF00FFFFFFFF

    /// @audit 0x100000000
    223: div(h, 0x100000000),

    /// @audit 0xFFFFFFFF00000000000000000000000000000000
    224: 0xFFFFFFFF00000000000000000000000000000000

    /// @audit 0x1000000
    227: div(h, 0x1000000),

    /// @audit 0xFFFFFFFF000000000000000000000000
    228: 0xFFFFFFFF000000000000000000000000

    /// @audit 0x10000, 0xFFFFFFFF0000000000000000
    231: and(div(h, 0x10000), 0xFFFFFFFF0000000000000000)

    /// @audit 0x100, 0xFFFFFFFF00000000
    233: and(div(h, 0x100), 0xFFFFFFFF00000000)

    /// @audit 0xFFFFFFFF
    235: and(h, 0xFFFFFFFF)

    /// @audit 0x1000000000000000000000000
    237: 0x1000000000000000000000000

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

    File: contracts/utils/NameEncoder.sol

    /// @audit 2
    15: dnsName = new bytes(length + 2);

    /// @audit 1
    25: for (uint256 i = length - 1; i >= 0; i--) {

    /// @audit 1
    27: dnsName[i + 1] = bytes1(labelLength);

    /// @audit 1
    31: bytesName.keccak(i + 1, labelLength)

    /// @audit 1
    36: labelLength += 1;

    /// @audit 1
    37: dnsName[i + 1] = bytesName[i];

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol

    File: contracts/utils/HexUtils.sol

    /// @audit 47, 58
    25: if and(gt(c, 47), lt(c, 58)) {

    /// @audit 48
    26: ascii := sub(c, 48)

    /// @audit 64, 71
    30: if and(gt(c, 64), lt(c, 71)) {

    /// @audit 65, 10
    31: ascii := add(sub(c, 65), 10)

    /// @audit 96, 103
    35: if and(gt(c, 96), lt(c, 103)) {

    /// @audit 97, 10
    36: ascii := add(sub(c, 97), 10)

    /// @audit 0xff
    40: ascii := 0xff

    /// @audit 32
    43: let ptr := add(str, 32)

    /// @audit 2
    47: i := add(i, 2)

    /// @audit 1
    50: let byte2 := getHex(byte(0, mload(add(ptr, add(i, 1)))))

    /// @audit 0xff
    52: if or(eq(byte1, 0xff), eq(byte2, 0xff)) {

    /// @audit 4
    56: let combined := or(shl(4, byte1), byte2)

    /// @audit 40
    73: if (lastIdx - idx < 40) return (address(0x0), false);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

## [N-02] Use named parameters for mapping type declarations

Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.

There are 3 instances of this issue in 2 files:

    File: contracts/dnsregistrar/DNSRegistrar.sol

    32: mapping(bytes32 => uint32) public inceptions;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    45: mapping(uint8 => Algorithm) public algorithms;

    46: mapping(uint8 => Digest) public digests;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [N-03] Use a modifier for access control

Consider using a modifier to implement access control instead of inlining the condition/requirement in the function’s body.

There is 1 instance of this issue in 1 file:

    File: contracts/dnsregistrar/DNSRegistrar.sol

    111: if (msg.sender != owner) {
    112:     revert PermissionDenied(msg.sender, owner);
    113: }

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

## [N-04] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

There are 32 instances of this issue in 6 files:

    File: contracts/dnsregistrar/DNSClaimChecker.sol

    16: uint16 constant CLASS_INET = 1;

    17: uint16 constant TYPE_TXT = 16;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

    File: contracts/dnsregistrar/OffchainDNSResolver.sol

    29: uint16 constant CLASS_INET = 1;

    30: uint16 constant TYPE_TXT = 16;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

    File: contracts/dnssec-oracle/BytesUtils.sol

    322: bytes constant base32HexTable =

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/RRUtils.sol

    72: uint256 constant RRSIG_TYPE = 0;

    73: uint256 constant RRSIG_ALGORITHM = 2;

    74: uint256 constant RRSIG_LABELS = 3;

    75: uint256 constant RRSIG_TTL = 4;

    76: uint256 constant RRSIG_EXPIRATION = 8;

    77: uint256 constant RRSIG_INCEPTION = 12;

    78: uint256 constant RRSIG_KEY_TAG = 16;

    79: uint256 constant RRSIG_SIGNER_NAME = 18;

    210: uint256 constant DNSKEY_FLAGS = 0;

    211: uint256 constant DNSKEY_PROTOCOL = 2;

    212: uint256 constant DNSKEY_ALGORITHM = 3;

    213: uint256 constant DNSKEY_PUBKEY = 4;

    236: uint256 constant DS_KEY_TAG = 0;

    237: uint256 constant DS_ALGORITHM = 2;

    238: uint256 constant DS_DIGEST_TYPE = 3;

    239: uint256 constant DS_DIGEST = 4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol
    
    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    21: uint256 constant a =

    23: uint256 constant b =

    25: uint256 constant gx =

    27: uint256 constant gy =

    29: uint256 constant p =

    31: uint256 constant n =

    34: uint256 constant lowSmax =

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    27: uint16 constant DNSCLASS_IN = 1;

    29: uint16 constant DNSTYPE_DS = 43;

    30: uint16 constant DNSTYPE_DNSKEY = 48;

    32: uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [N-05] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

There are 15 instances of this issue in 5 files:

    File: contracts/dnssec-oracle/BytesUtils.sol

    19: assembly {

    73: assembly {

    80: assembly {

    197: assembly {

    213: assembly {

    229: assembly {

    245: assembly {

    267: assembly {

    276: assembly {

    286: assembly {

    311: assembly {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/RRUtils.sol

    386: assembly {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

     23: assembly {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

     File: contracts/dnssec-oracle/SHA1.sol

     7: assembly {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

     File: contracts/utils/HexUtils.sol

     17: assembly {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

## [N-06] Take advantage of Custom Error’s return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

There are 4 instances of this issue in 2 files:

    File: contracts/dnsregistrar/DNSRegistrar.sol

    34: error NoOwnerRecordFound();

    36: error PreconditionNotMet();

    37: error StaleProof();

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    38: error InvalidRRSet();

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [N-07] Inconsistent Solidity Versions

Different Solidity compiler versions are used, the following contracts mix versions:

    File: contracts/dnsregistrar/DNSClaimChecker.sol

    2: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

    File: contracts/dnsregistrar/OffchainDNSResolver.sol

    2: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

    File: contracts/dnsregistrar/RecordParser.sol

    2: pragma solidity ^0.8.11;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

    File: contracts/dnsregistrar/DNSRegistrar.sol

    3: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/BytesUtils.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/RRUtils.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

    File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol

    File: contracts/dnssec-oracle/digests/SHA1Digest.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

    File: contracts/dnssec-oracle/digests/SHA256Digest.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    2: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

    File: contracts/dnssec-oracle/SHA1.sol

    1: pragma solidity >=0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

    File: contracts/utils/NameEncoder.sol

    2: pragma solidity ^0.8.13;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol

    File: contracts/utils/HexUtils.sol

    2: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

## [N-08] Inconsistent method of specifying a floating pragma

Some files use >=, some use ^. The instances below are examples of the method that has the fewest instances for a specific version. Note that using >= without also specifying <= will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so ^ should be preferred regardless of the instance counts

There is 1 instance of this issue:

    File: contracts/dnssec-oracle/SHA1.sol

    1: pragma solidity >=0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

## [N-09] File does not contain an SPDX Identifier

There are 11 instances of this issue in 11 files:

    File: contracts/dnssec-oracle/BytesUtils.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/RRUtils.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

    File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

    File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

    File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

    File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol

    File: contracts/dnssec-oracle/digests/SHA1Digest.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

    File: contracts/dnssec-oracle/digests/SHA256Digest.sol

    1: pragma solidity ^0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

    File: contracts/dnssec-oracle/SHA1.sol

    1: pragma solidity >=0.8.4;

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

## [N-10] Events is missing indexed fields

Index event fields make the field more quickly accessible to off-chain.
Each event should use three indexed fields if there are three or more fields.

There are 3 instances of this issue in 2 files:

    File: contracts/dnsregistrar/DNSRegistrar.sol

    47: event Claim(
    48:     bytes32 indexed node,
    49:     address indexed owner,
    50:     bytes dnsname,
    51:     uint32 inception
    52: );

    53: event NewPublicSuffixList(address suffixes);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/SHA1.sol

    4: event Debug(bytes32 x);

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

## [N-11] No same value input control

There are 3 instances of this issue in 2 files:

    File: contracts/dnsregistrar/DNSRegistrar.sol

    80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
    81:     suffixes = _suffixes;
    82:     emit NewPublicSuffixList(address(suffixes));
    83: }

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
    65:     algorithms[id] = algo;
    66:     emit AlgorithmUpdated(id, address(algo));
    67: }

    75: function setDigest(uint8 id, Digest digest) public owner_only {
    76:     digests[id] = digest;
    77:     emit DigestUpdated(id, address(digest));
    78: }

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [N-12] Contract Owner Has Too Many Privileges

The owner of the contracts has too many privileges relative to standard users. The consequence is disastrous if the contract owner’s private key has been compromised. And, in the event the key was lost or unrecoverable, no implementation upgrades and system parameter updates will ever be possible.

For a project this grand, it increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure; and, here are some of the incidents that could possibly transpire:

Transfer ownership and mess up with all the setter functions, hijacking the entire protocol.

Consider:
-- Splitting privileges (e.g. via the multisig option) to ensure that no one address has excessive ownership of the system
-- Clearly documenting the functions and implementations the owner can change
-- Documenting the risks associated with privileged users and single points of failure
-- Ensuring that users are aware of all the risks associated with the system

## [N-13] Typo

There is 1 instance of this issue in 1 file: 

    File: contracts/dnssec-oracle/BytesUtils.sol
    
    /// @audit rune 
    42: *      contents of the two bytes are equal. Comparison is done per-rune,

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

## [N-14] Inconsistent method of specifying NatSpec

At some place /** is used while at other places /* have been used. It is recommended to use /** method. 

There are 15 instances of this issue in 2 files:

    File: contracts/dnssec-oracle/BytesUtils.sol

    6: /*
    7:  * @dev Returns the keccak-256 hash of a byte range.

    24: /*
    25:  * @dev Returns a positive number if `other` comes lexicographically after

    39: /*
    40:  * @dev Returns a positive number if `other` comes lexicographically after

    102: /*
    103:  * @dev Returns true if the two byte ranges are equal.

    121 /*
    122: * @dev Returns true if the two byte ranges are equal with offsets.

    140: /*
    141:  * @dev Compares a range of 'self' to all of 'other' and returns True iff

    158: /*
    159:  * @dev Returns true if the two byte ranges are equal.

    173: /*
    174:  * @dev Returns the 8-bit number at the specified index of self.

    186: /*
    187:  * @dev Returns the 16-bit number at the specified index of self.

    202: /*
    203:  * @dev Returns the 32-bit number at the specified index of self.

    218: /*
    219:  * @dev Returns the 32 byte value at the specified index of self.

    232: /*
    233:  * @dev Returns the 32 byte value at the specified index of self.

    253: /*
    254:  * @dev Returns the n byte value at the specified index of self.

    294: /*
    295:  * @dev Copies a substring into a new byte string.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

    File: contracts/dnssec-oracle/DNSSECImpl.sol

    13: /*
    14:  * @dev An oracle contract that verifies and stores DNSSEC-validated DNS records.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol


