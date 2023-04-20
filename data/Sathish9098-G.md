





GAS‑1	abi.encode() is less efficient than abi.encodepacked()	1	100
GAS‑2	<array>.length Should Not Be Looked Up In Every Loop Of A For-loop	2	194
GAS‑3	Use calldata instead of memory for function parameters	6	1800
GAS‑4	Setting the constructor to payable	3	39
GAS‑5	Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function	2	56
GAS‑6	Using delete statement can save gas	10	-
GAS‑7	++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)	5	30
GAS‑8	Functions guaranteed to revert when called by normal users can be marked payable	3	63
GAS‑9	Use hardcoded address instead address(this)	4	-
GAS‑10	It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied	4	-
GAS‑11	internal functions only called once can be inlined to save gas	63	1386
GAS‑12	Optimize names to save gas	9	198
GAS‑13	<x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables	25	-
GAS‑14	Public Functions To External	6	-
GAS‑15	require() Should Be Used Instead Of assert()	3	-
GAS‑16	require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas	3	-
GAS‑17	Save gas with the use of specific import statements	44	-
GAS‑18	Shorten the array rather than copying to a new one	1	-
GAS‑19	Splitting require() Statements That Use && Saves Gas	1	9
GAS‑20	Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It	1	-
GAS‑21	Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead	40	-
GAS‑22	++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops	7	245
GAS‑23	Using unchecked blocks to save gas	4	80
GAS‑24	Use v4.8.1 OpenZeppelin contracts	1	-
GAS‑25	Use solidity version 0.8.19 to gain some gas boost	19	1672
GAS‑26	Use uint256(1)/uint256(2) instead for true and false boolean states	2	10000