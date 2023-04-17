https://gist.github.com/YinjiDawn/8442dfeff30af72ac83cd8ac657ef954

1)Input Validation: The hexStringToBytes32 and hexToAddress functions do not perform input validation to check if the input str is a valid hex string. If the input str contains invalid characters or has an odd length, it may result in unexpected behavior. It's important to ensure that proper input validation is performed to avoid potential issues.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

1)The unchecked block is used to avoid redundant checks for underflow and length before the loop. While it can save gas, it also carries the risk of causing unexpected behavior if the input name is not properly formatted, such as containing leading or trailing dots. It's important to ensure that the input name is properly validated and formatted to avoid potential issues.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol

1) The code does not include any input validation checks to ensure that the input data is valid or within expected bounds. For example, it does not check if the input data is of the correct length or if it is empty. It is important to validate and sanitize input data to prevent potential vulnerabilities, such as buffer overflows or other unexpected behavior.

2)The code does not include an explicit visibility specifier for the sha1 function, which means that it will default to public visibility. It is generally recommended to explicitly specify the visibility of functions to prevent potential issues and improve code clarity.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

1)The function validateRRSet does not check if the input parameter is empty before iterating over it. This may result in unexpected behavior or gas wastage if an empty array is passed as input.

2)The code does not check if the input array length is within reasonable limits. If the input array is too large, it may result in high gas costs or even out-of-gas errors. It's recommended to add appropriate input validation checks to ensure that the array length is reasonable.

3)The validateSignedSet function uses bytes32(0) as a default value for the keyTag variable. However, this may not always be a valid default value, and it's better to use a specific value or a different approach to handle uninitialized variables.

4)he validateSignedSet function does not check if the signer name in the RRSetWithSignature input matches the signer name in the DNSKEY or DS record being validated. This may allow validation of incorrect signatures. It's recommended to add a check to ensure that the signer name in the input matches the signer name in the DNSKEY or DS record being validated.

5): The validateSignedSet function does not handle cases where the signature is not yet valid (inception is in the future) or has expired (expiration is in the past). This may allow validation of invalid signatures. It's recommended to add appropriate checks to ensure that the signature is valid in terms of its inception and expiration times.

6)The code does not handle cases where the RRSetWithSignature input contains wildcard names or NSEC/NSEC3 records. This may result in incorrect validation of DNS records. It's recommended to add appropriate checks or error handling to ensure that only positive proofs are allowed and wildcard names, NSEC, and NSEC3 records are not supported, as mentioned in the code comments.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1)Consider using bytes32 instead of bytes for key, data, and sig parameters in the verify function, as bytes32 has a lower gas cost compared to bytes. You can convert the bytes parameters to bytes32 before passing them to the internal functions readUint8, substring, readUint16, rsarecover, and sha256, respectively.

2)In the verify function, consider using bytes.length instead of key.length and result.length for better readability and to avoid unnecessary gas costs of repeatedly computing the length of key and result in expressions like key.length - exponentLen - 5 and result.length - 32. You can store bytes.length in a local variable for reuse.

3)Consider using the constant keyword instead of view for the RSAVerify contract, as it indicates that the contract does not modify the contract state and only reads from external contracts, which may result in gas optimizations.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1)Consider using bytes32 instead of bytes for key, data, and signature parameters in the verify function, as bytes32 has a lower gas cost compared to bytes. You can convert the bytes parameters to bytes32 before passing them to the internal functions sha256, parseSignature, and parseKey, respectively.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

1)The function inverseMod() uses an int256 for intermediate calculations, which may result in higher gas costs compared to using uint256 directly. Since all inputs and outputs are uint256, you can consider refactoring the function to use uint256 for intermediate calculations as well.

2)The function isZeroCurve() returns true if both x0 and y0 are zero. However, in elliptic curve cryptography, the point at infinity (also known as the "zero" point) is typically represented as (0, 0) in affine coordinates, and as (0, 1, 0) in projective coordinates. You may want to update the implementation of isZeroCurve() to check for both (0, 0) and (0, 1, 0) to properly identify the point at infinity.

3)The function validateSignature() includes a commented out condition rs[1] > lowSmax which is not used in the validation logic. If this condition is intended to be part of the validation, you should update the implementation accordingly.

4)he function mulmod() is used multiple times in the code. Consider using the unchecked keyword before the calls to mulmod() to save gas costs, as it disables the automatic overflow checking, which is unnecessary in this case as the inputs and outputs are already constrained to valid curve parameters.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1): The verify() function does not handle the case where the key input is not long enough to contain the expected data. For example, if the key input is shorter than 7 bytes, the key.readUint8(4) and key.readUint16(5) calls will result in reading data out of bounds and may lead to unexpected behavior. It is recommended to add appropriate length checks to ensure that the input key is of the expected length before reading data from it.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

1)The readSignedSet() function does not have a return statement, which will result in a compilation error. To fix this, you should add a return statement at the end of the function, like so:

function readSignedSet(
    bytes memory data
) internal pure returns (SignedSet memory self) {
    self.typeCovered = data.readUint16(RRSIG_TYPE);
    self.algorithm = data.readUint8(RRSIG_ALGORITHM);
    self.labels = data.readUint8(RRSIG_LABELS);
    self.ttl = data.readUint32(RRSIG_TTL);
    self.expiration = data.readUint32(RRSIG_EXPIRATION);
    self.inception = data.readUint32(RRSIG_INCEPTION);
    self.keytag = data.readUint16(RRSIG_KEY_TAG);
    self.signerName = readName(data, RRSIG_SIGNER_NAME);
    self.data = data.substring(
        RRSIG_SIGNER_NAME + self.signerName.length,
        data.length - RRSIG_SIGNER_NAME - self.signerName.length
    );
    return self; // Add this return statement
}

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

1)Use of revert with Arguments: The contract uses the revert statement with custom error messages, such as revert PermissionDenied(msg.sender, owner);, revert PreconditionNotMet();, revert StaleProof();, and revert InvalidPublicSuffix(domain);. It is generally recommended to avoid passing arguments to the revert statement, as it could lead to misleading or ambiguous error messages, which could make it harder to identify and fix issues.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

1)The function does not perform any input validation to check if the input string is in the expected format. It assumes that the input string follows the format where key-value pairs are separated by an equal sign (=) and terminated by a space character (' '). If the input string does not adhere to this format, the function may not work correctly and could produce unexpected results.

2)The function calculates the next offset to start reading from based on the position of the terminator character. However, the terminator is calculated as the end of the input string if not found, which may not always be correct. Depending on the expected format of the input string, this may lead to incorrect offset calculation and subsequent parsing errors.

3)The function uses multiple string manipulation functions such as find and substring from the BytesUtils library, which may not be the most efficient approach for parsing large input strings. Depending on the use case and performance requirements, a more efficient algorithm for parsing key-value pairs may need to be implemented.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

1)The resolveCallback function takes response and extraData as bytes calldata parameters, but it does not use the response parameter in the function body. Make sure to utilize the response parameter in your logic if it is intended to be used.

2)The parseRR function returns an address and bytes memory, but the returned bytes memory variable context is not used in the function body. Make sure to utilize the context variable in your logic if it is intended to be used.

3)In the readTXT function, the TODO comment mentions concatenating multiple text fields, but the function currently only reads a single text field. If you intend to concatenate multiple text fields, you should implement that logic in this function.

4)The parseAndResolve function checks if the nameOrAddress starts with "0x" to determine if it's an address, but this check may not be sufficient to determine if the provided input is a valid Ethereum address. You should consider using a more robust address validation mechanism, such as the isContract function from the Address library in OpenZeppelin.

