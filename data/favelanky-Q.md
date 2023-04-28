# Table of Contents

| Number | Issues Details                                                                         | Count |
| :----- | :------------------------------------------------------------------------------------- | :---- |
| [L-1]  | Zero address check is missing                                                          | 4     |
| [NC-1] | Local variable shadowing                                                               | 1     |

Total number of issues: 5

# Findings

## [L-1] Zero address check is missing`

Zero-address checks are a best-practise for input validation of critical address parameters. While the codebase applies this to most addresses in setters, there are many places where this is missing in constructors and setters.

<details>
<summary><i>There are 4 instances of this issue:</i></summary>

```solidity
File: dnsregistrar/DNSRegistrar.sol

55:     constructor(
56:         address _previousRegistrar,
57:         address _resolver,
58:         DNSSEC _dnssec,
59:         PublicSuffixList _suffixes,
60:         ENS _ens
61:     ) {
62:         previousRegistrar = _previousRegistrar;
63:         resolver = _resolver;
64:         oracle = _dnssec;
65:         suffixes = _suffixes;
66:         emit NewPublicSuffixList(address(suffixes));
67:         ens = _ens;
68:     }

55:     constructor(
56:         address _previousRegistrar,
57:         address _resolver,
58:         DNSSEC _dnssec,
59:         PublicSuffixList _suffixes,
60:         ENS _ens
61:     ) {
62:         previousRegistrar = _previousRegistrar;
63:         resolver = _resolver;
64:         oracle = _dnssec;
65:         suffixes = _suffixes;
66:         emit NewPublicSuffixList(address(suffixes));
67:         ens = _ens;
68:     }

101:     function proveAndClaimWithResolver(
102:         bytes memory name,
103:         DNSSEC.RRSetWithSignature[] memory input,
104:         address resolver,
105:         address addr
106:     ) public override {
107:         (bytes32 rootNode, bytes32 labelHash, address owner) = _claim(
108:             name,
109:             input
110:         );
111:         if (msg.sender != owner) {
112:             revert PermissionDenied(msg.sender, owner);
113:         }
114:         ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);
115:         if (addr != address(0)) {
116:             if (resolver == address(0)) {
117:                 revert PreconditionNotMet();
118:             }
119:             bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
120:             // Set the resolver record
121:             AddrResolver(resolver).setAddr(node, addr);
122:         }
123:     }

101:     function proveAndClaimWithResolver(
102:         bytes memory name,
103:         DNSSEC.RRSetWithSignature[] memory input,
104:         address resolver,
105:         address addr
106:     ) public override {
107:         (bytes32 rootNode, bytes32 labelHash, address owner) = _claim(
108:             name,
109:             input
110:         );
111:         if (msg.sender != owner) {
112:             revert PermissionDenied(msg.sender, owner);
113:         }
114:         ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);
115:         if (addr != address(0)) {
116:             if (resolver == address(0)) {
117:                 revert PreconditionNotMet();
118:             }
119:             bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
120:             // Set the resolver record
121:             AddrResolver(resolver).setAddr(node, addr);
122:         }
123:     }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L55-L68

</details>

## [NC-1] Local variable shadowing

Local variable shadowing is a common source of bugs. It is recommended to avoid it by changing the name of the local variable.
Change name of `resolver` variable in line 104 to avoid shadowing.

<details>
<summary><i>There are 1 instances of this issue:</i></summary>

```solidity
File: dnsregistrar/DNSRegistrar.sol

101:     function proveAndClaimWithResolver(
102:         bytes memory name,
103:         DNSSEC.RRSetWithSignature[] memory input,
104:         address resolver,
105:         address addr
106:     ) public override {
107:         (bytes32 rootNode, bytes32 labelHash, address owner) = _claim(
108:             name,
109:             input
110:         );
111:         if (msg.sender != owner) {
112:             revert PermissionDenied(msg.sender, owner);
113:         }
114:         ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);
115:         if (addr != address(0)) {
116:             if (resolver == address(0)) {
117:                 revert PreconditionNotMet();
118:             }
119:             bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
120:             // Set the resolver record
121:             AddrResolver(resolver).setAddr(node, addr);
122:         }
123:     }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L101-L123

</details>
