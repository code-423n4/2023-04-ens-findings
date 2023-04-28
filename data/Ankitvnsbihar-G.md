># DNSRegistrar.sol( https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol )
>## G-1)   In line no.82 of DNSRegistrar.sol where   
>   -emit NewPublicSuffixList(address(suffixes)); )  
>should change to 
>   +emit NewPublicSuffixList(address(_suffixes));) and it will save gas.




>## G-2)  in line no. 66 of DNSRegistrar.sol  
 -emit NewPublicSuffixList(address(suffixes));) 
should change to 
 +emit NewPublicSuffixList(address(_suffixes));) will save gas.



>## G-3) In line no. 76 of OnlyOwner modifier of DNSRegistrar.sol contract where
-require(msg.sender == owner);
should change to 
+require(msg.sender == root.owner()); to save gas








 