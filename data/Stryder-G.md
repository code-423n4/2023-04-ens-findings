# [G-01] Using the ternary operator saves more Gas 

Link : https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L68

Consider using a ternary operator instead of assigning a value to a variable and then changing afterwards
This saves the gas for the assigning and the re-assigning of the variable 

**POC** 

    pragma solidity 0.8.9;
    contract test1{
        // gas cost : 814
        function comapre1(uint x , uint y) public returns (uint ){
            uint temp = x;
            if(x  > y){
                temp = y;
            }

            return temp;
        }
        // gas cost : 777
        function compare2(uint x , uint y) public returns(uint){
            return x < y ? x : y; 
        }
    }

Total gas saved: 40 gas per instance 

Link: https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L123-L128
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150-L158
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L188-L200

# [G-02] Internal function called only once can be inlined 

An internal function that is only called once from the contract can be inlined to the calling function so save 
gas cost in calling of the function 

Total Instances : https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L52-L59

# [G-03] Uint16 can be changed to Uint8 

Since the value assigned to a constant variable can't be changed , so the uint16 can be changed to uint8 as the value of 1 can 
be contained in it as well 

Total instaces : https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16
                https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L17

# [G-04] Inline the variables used only once 

Varibales that are used only once can be inlined to save some gas ,they dont need to be stored in memory as 
they are only used once 

For example : https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L77

The code can be changed from : 

    modifier onlyOwner() {
        Root root = Root(ens.owner(bytes32(0)));
        address owner = root.owner();
        require(msg.sender == owner);
        _;
    }

to 

    modifier onlyOwner() {
        require(msg.sender == Root(ens.owner(bytes32(0))).owner());
        _;
    }

# [G-05] Make State Variables immutable that are only assigned at the constructor 

State variables that are only assigned at the constructor and not changed after that can be made 
immutable . If the variables are known from before the deployment of the contract , they can be made constant and 
their value can just be hardcoded in the contract 

Link : https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L39

# [G-06] Use unchecked for checking for Null Address/address(0)

Consider adding the Null Address check inside the unchecked block as this doesnt add any additional checks that might 
increase the gas cost. 

Link: https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L102
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115-L116
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187

# [G-08] Modifier called only once can be inlined to the function 

Consider adding the check directly to the function if the check is required only once in the contract 
No need of creating a modifier for just one check 

Link: https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78
