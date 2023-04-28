## 1. `now` VARIABLE IS AN ALIAS FOR `block.timestamp`, HENCE NOT SUITABLE TO BE USED AS VARIABLE NAMES.

In `DNSSECImpl.verifyRRSet()` function, variable `now` is used as a `uint256` input parameter to the function.

    function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
	
But the `now` variable is an alias for `block.timestamp`, which represents the current block's timestamp as seconds since the Unix epoch.
Hence it is recommended to change the variable name `now` for ease of understanding and to eliminate any confusion that may occur.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L109


## 2. GLOBAL VARIABLE `resolver` IS `SHADOWED` BY LOCAL VARIABLE `resolver`.

In `DNSRegistrar.proveAndClaimWithResolver()` function, `Shadowing` of the global variable `reserve` with the local variable `reserve` is observed.

`resolver` global variable declaration is below:

    address public immutable resolver;


`resolver` local variable declaration is as below:

    function proveAndClaimWithResolver(
        bytes memory name,
        DNSSEC.RRSetWithSignature[] memory input,
        address resolver,
        address addr
    )
	
Hence it is recommended to change the local variable name `resolver` in the `DNSRegistrar.proveAndClaimWithResolver()` function, for ease of readability and to eliminate any confusion that may occur due to variable `shadowing`.
	
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L30	
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L104
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L114	
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L116



## 3. USE OF `abi.encodePacked` WITH `keccak256` COULD LEAD TO HASH COLLISIONS.

In `DNSRegistrar.proveAndClaimWithResolver()` function, use of `abi.encodePacked` with `keccak256` could lead to hash collisions. 

            bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
			
It is recommended to use `abi.encode()` instead of `abi.encodePacked()`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L119

There are two more instances of this issue observed:

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L151
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L185


## 4. NO INPUT VALIDATION DONE FOR `address(0)` FOR STATE VARIABLE ASSIGNMENTS IN THE `DNSRegistrar.constructor()`.

No input validation done for `address(0)` for state variable assignments in the `DNSRegistrar.constructor()`.

    constructor(
        address _previousRegistrar,
        address _resolver,
        DNSSEC _dnssec,
        PublicSuffixList _suffixes,
        ENS _ens
    ) {
        previousRegistrar = _previousRegistrar;
        resolver = _resolver;
        oracle = _dnssec;
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
        ens = _ens;
    }

Hence it is recommended to perform input validation for critical variables which are set in the constructor such as `resolver`, `oracle` etc ...
Because if null values are assigned to these critical state variables could result in redeployment of the smart contract.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L55-L68

## 5. FOLLOWING TYPOS ARE FOUND IN THE NATSPEC COMMENTS OF THE CODE BASE.

    * @return A new bytes object containing the `owner name` from the RR. 
	
Should be corrected as `DNS name`
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L185

    * @dev Attempts to verify a signed RRSET against an already known hash. This function assumes
    *      that the record
	
Incomplete comment as shown above. - DNSSECImpl.verifyWithDS()
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L324-L325

    * @param proof The serialized DS or `DNSKEY` record to use as proof.
	
The `DNSSECImpl.verifyWithDS()` only verifies with the DS records. Hence the `DNSKEY` should be removed from the comment.
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L328

    * @param proof The serialized `DS or DNSKEY` record to use as proof.
	
In the `DNSSECImpl.verifyWithKnownKey()` function, is only called by the `DNSSECImpl.verifySignature()` function, when `proofRR.dnstype == DNSTYPE_DNSKEY`. Hence above comment should be changed to include only the `DNSKEY record`, and `DS` part should be removed.
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L252

    * @dev Returns a DNS format name at the specified offset of self.
	
In the comments of the `RRUtils.readName()` function the specific DNS format is not given. In other comments it is mentioned whether the format is `wire format` or the `label-sequence` format. So it is recommended to specifically mention the DNS format expected here in the comment.
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L36

## 6. MULTIPLE `nested` `for` and `while` LOOPS COULD TRIGGER `block gas limit exceed` ERROR

The `DNSSECImpl.verifyRRSet()` function is used to verify the signed RRSet for the domain which is requested to be claimed. 
But the implementation of the `verifyRRSet` function calls multiple nested `for` and `while` loops in a single transaction.

    function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    ){
	...
        for (uint256 i = 0; i < input.length; i++) {
            RRUtils.SignedSet memory rrset = validateSignedSet(
                input[i],
                proof,
                now
            );	
	...
	}


So in the future when the ENS system is evolved and multiple sub domain names are requested it could lead to higher number of iterations of these `for` and `while` loops. 
Since these loops are unbounded,  this could lead to `out of gas` or `block gas limit exceed` errors. And the corresponding transactions could revert and the protocol will not be usable any more.
Hence it is recommended to Optimize the smart contract code to minimize the number of nested loops.  

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118-L123
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L186-L190
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L260-L264
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L336-L340
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L380-L403

## 7. THE `DNSSECImpl.verifyRRSet()` FUNCTION USES BOTH `implicit` AND `explicit` RETURN VALUES. 

In the `named return values` of the function signature of `DNSSECImpl.verifyRRSet()`, the returned bytes array is defined as `bytes memory rrs`. 
So the parameter which is expected to be returned is defined as `rrs`. 

    function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
        public
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception){
		
		...
		
		return (proof, inception);
    }
		
But actually the returned bytes values is `proof`. 
Even though this does not impact the protocol execution it can be confusing to the devs and the auditors. 
Hence it is recommended to change the `named returned value` in the function signature as `bytes memory proof` for ease of readability and understanding of the code.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L107-L128

## 8. `domain` NAME INCLUDES THE `length` BYTE IN IT'S `byte array`, HENCE THE `suffixes` SHOULD ALSO INCLUDE THE `length byte` IN IT'S LIST FOR EACH OF THE SUFFIXES.

In the `DNSRegistrar.enableNode()` function, the `domain` is checked against the `suffixes` and will be reverted if the `domain` is not included in the `suffixes` list. 

        bytes memory parentName = name.substring(
            labelLen + 1,
            name.length - labelLen - 1
        );

Here the domain includes the `lenght` byte of the `parentNode`. Because the `domain` is given in the `DNS wire format`.
Hence the `suffixes` list should also include lenght bit for each of the suffixes. 

        if (!suffixes.isPublicSuffix(domain)) {
            revert InvalidPublicSuffix(domain);
        }

Else, if the `suffixes` list only includes the bytes array of the `domain` name the `DNSRegistrar.enableNode()` function will revert with `InvalidPublicSuffix(domain)` error. 

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L143-L146
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L168-L170

## 9. NO NATSCPEC COMMENTING IS USED. 

NATSPEC commenting is not used in the functions of the `DNSRegistrar.sol` smart contract