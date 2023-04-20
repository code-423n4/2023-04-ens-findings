Floating Pragma Solidity Version and consistency:

Throughout the system contracts codebases, the version of Solidity used is ^0.8.4. This version indicates that any compiler version after 0.8.4 can be used to compile the source code. This could lead to issues during the compiler time and in a future versions. It is recommended to remove the ^ to set the pragma. 
In addition, out of the scope, the project uses another versions, such a ^0.8.11 or ^0.8.13. It is recommended to set to the same pragma.