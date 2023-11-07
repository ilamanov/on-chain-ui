# Signing and sending transactions

You can also request transaction signature, and then send the transaction through the user's wallet. Here is an example (deployed at [0x8A95F73C1dF49899954162FC9156ADa4646cbB72](https://monobase.xyz/sepolia/address/0x8A95F73C1dF49899954162FC9156ADa4646cbB72) on Sepolia):

![](assets/sign-and-send-tx.gif)

Here is the code:

```Solidity
function getUI(address forAddress) public view override returns (string memory) {
    return string.concat(
    	...,
    	depositAction(forAddress)
    );
}

function depositAction(address forAddress) public view returns (string memory) {
	bytes memory depositCall = abi.encodeCall(thisContract.deposit, ());
	return string.concat(
		"<form ",
		  "type=\"tx\" ",
		  "ui-tx=\"/", addressToString(address(thisContract)), "/", bytesToString(depositCall), "\", ",
		  "ui-tx-value=\"$depositAmount\" ",
		  "ui-post=\"/this/", bytesToString(abi.encodePacked(this.onDeposit.selector)), "$ui-txHash\" ",
		  "ui-target=\"#tx-message\" ",
		  "ui-swap=\"innerHTML\" ",
		  "style=\"display: flex; gap: 10px; margin-top: 30px;\">",
			"<input name=\"depositAmount\" class=\"input\" placeholder=\"wei\" type=\"uint256\"/>",
    		"<button ",
			  "type=\"submit\" ",
			  "class=\"button center-children\">",
				"Deposit", loadingSpinner(),
			"</button>",
		"</form>",
		"<div ",
		  "id=\"tx-message\" ",
		  "style=\"color: #4d7c0f; margin-top: 20px;\">",
		"</div>"
	);
}

function onDeposit(bytes32 txHash) public payable returns (string memory) {
	string memory txHashString = bytesToString(abi.encodePacked(txHash));
	return string.concat(
		"Success! tx hash: ",
		"<a href=\"https://monobase.xyz/", getChainSlug(), "/tx/", txHashString, "\">",
			txHashString,
		"</a>"
	);
}
```

As you can see, in order to sign and send transactions, your `<form>` or `<button>` type needs to be set to `type="tx"`. You then specify what transaction to send via the `ui-tx` attribute. In this case it's `ui-tx="/0x7b79995e5f793a07bc00c21412e50ecae098e7f9/0xd0e30db0"` where `0x7b79995e5f793a07bc00c21412e50ecae098e7f9` is the address of the WETH contract and `0xd0e30db0`is the calldata for the `deposit` function.

You can also optionally specify the value you want to send along with the transaction using the `ui-tx-value` attribute. And of course you have access to the user input via `$[inputName]` keyword. So in the example above, we specify the value to send via `ui-tx-value="$depositAmount"` where `depositAmount` is the name of the `<input>` field.

After the transactions is signed and sent, `ui-post` will execute as normal. At this point you have access to the special variables `$ui-txHash` (of type `bytes32`) and `$ui-txOutput` (of type `bytes`). These will have the transaction hash and output. In the example above, we display the transaction hash (wrapped in a link) to the user using `ui-post="/this/0x7b896725$ui-txHash` where `0x7b896725` is the function selector of the `onDeposit` function.
