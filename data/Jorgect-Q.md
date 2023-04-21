# Low issue

## Low-1 Name colision
Be aware of how you name variables, if two variables has the same name may cause problem in the logic.
```
contract DNSRegistrar.sol line 101
 function proveAndClaimWithResolver(
        bytes memory name,
        DNSSEC.RRSetWithSignature[] memory input,
        address resolver, //name colision
        address addr
```
Recomendation:  change resolver for _resolver

```
contract DNSSECLmpl.sol line 107
function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now //@audit use _now
    )
```
Recomendation:  change now for _now

## low-2 add license to your contracts 
add "SPDX-License-Identifier: <SPDX-License>"  on top of your contracts
