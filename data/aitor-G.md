Use storage instead of memory for structs:

If you are not returning an entire struct (all fields) in a function then it is more gas efficient to use a storage pointer rather than using a memory pointer (which copies all the values in the struct from storage into memory). If you use a memory pointer and do not access all the struct fields in your function, then you are performing unnecessary sloads. (ref: https://solodit.xyz/issues/8901#)

Contract: https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

1.- line 120:

```
struct RRIterator {
        bytes data;
        uint256 offset;
        uint16 dnstype;
        uint16 class;
        uint32 ttl;
        uint256 rdataOffset;
        uint256 nextOffset;
    }
````
2.- lines 136, 150, 158 -> It is possible to save gas by using storage instead of memory in each of these 3 functions:

```
  
    function iterateRRs(
        bytes memory self,
        uint256 offset
    ) internal pure returns (RRIterator memory ret) {
        ret.data = self;
        ret.nextOffset = offset;
        next(ret);
    }

    
    function done(RRIterator memory iter) internal pure returns (bool) {
        return iter.offset >= iter.data.length;
    }


    function next(RRIterator memory iter) internal pure {
        iter.offset = iter.nextOffset;
        if (iter.offset >= iter.data.length) {
            return;
        }
```

