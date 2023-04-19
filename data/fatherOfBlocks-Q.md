**contracts/dnssec-oracle/algorithms/EllipticCurve.sol**
- L128 - Instead of the implementation made it is much simpler to read directly do: "return x0 == 0 && y0 == 0;"

- L393 - There is commented code that gives you confusion and therefore less understanding. Those comments should be removed.


**contracts/utils/NameEncoder.sol**
- L28/45 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.
