Title: Consider using more descriptive variable names

Proof of Concept:
[BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol)
Use descriptive variable names to make the code easier to read and understand.
For example, instead of using `ret` you could name it `output` instead to indicate what the variable represents
________________________________________________________________________

Title: Remove unncessary comments

Proof of Concept:
[NameEncoder.sol#L22-L23](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L22-L23)
Remove the unnecessary comments as they do not add any value to the code and only increase the size of the bytecode.
________________________________________________________________________