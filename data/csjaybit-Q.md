<h2>[NC-1] There should be a comment in "compare" function where "NOT" operator is used in BytesUtils.sol  </h2>

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L91
Here, in above line not operator(~) is used, it is used only once in ENS protocol in scope, bitwise XOR(^) and not(~) are not used much and a comment should be there so developers can understand better


<h2>[NC-2] In contract SHA1.sol there is an event declared which is never emited  </h2>
 https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/SHA1.sol#L4
here  event Debug(bytes32 x); is declared but never emited in SHA1.sol contract

<h2>[NC-3] next is a pure function which returns nothing</h2>
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L158-L180
view function dont read any stats from blockchain, but inorder to read the calculation we did we need to return value.

<h2>[NC-4] Lack of comments in some contracts</h2>
ENS protocol has used assembly language frequently, developers need more context to understand what is happening in assembly, the following contracts which have less comments are DNSClaimChecker.sol, RecordParser.sol, OffchainDNSResolver.sol, DNSRegistrar.sol, RRUtils.sol
also imported functions are used without any descriptions in DNS claim checker,ex. it should be commented what buffer/buff function do for more readability.