So In OffchainDNSResolver contract on line 39. They are using a string "gatewayURL" that is not changing in the contract except the constructor.

before:
string public gatewayURL;

So mostly when we only need to change the variable in the constructor only. We use immutable so we can not use that with strings at the moment.

It is better to use constant here and add the link that they want to add in the constructor it will cost them much less gas for reads.

after:
string public constant gatewayURL="their string";

as this is mostly useable for other contracts to resolve the ENS and for them it will cost less gas.

