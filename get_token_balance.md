## Get token balance

## prerequisite : need to include ethers.js(version 5.0)

### the way you get the balance for ether and other erc20 token is different.

if you create provider like this, it might not return balance with the accurate amount
```javascript
new ethers.getDefaultProvider(currentNetwork, projectId);
```javascript
I recommend you to create provider according to the api you use
```
new ethers.providers.EtherscanProvider

new ethers.providers.InfuraProvider

new ethers.providers.AlchemyProvider

new ethers.providers.CloudflareProvider

new ethers.providers.JsonRpcProvider
```
this is how you create a function to return the balance of the address you give as parameter


```javascript
// whether to use this as a globale variable is your choice
window.ethersProvider = new ethers.providers.InfuraProvider(localStorage.getItem('user_network'));

async function get_any_token_balance(contractAddress, address, symbol)
{
	try
	{
		//const provider = new ethers.providers.InfuraProvider(localStorage.getItem('user_network'));
		if (symbol === 'eth')
		{
			
			const token_balance = await window.ethersProvider.getBalance(address);
			return token_balance;
		}
		else
		{
      // you can other abi like this, but, I chose to use abi with essential functions only
			//const contractABI = standard_token_abi;
      
			const contractABI = [
				// Read-Only Functions
				"function balanceOf(address owner) view returns (uint256)",
				"function decimals() view returns (uint8)",
				"function symbol() view returns (string)",
			
				// Authenticated Functions
				"function transfer(address to, uint amount) returns (boolean)",
			
				// Events
				"event Transfer(address indexed from, address indexed to, uint amount)"
			];
      // create contract of your token
			const contract = new ethers.Contract(contractAddress, contractABI, window.ethersProvider);
      
      // get the token balance of the address you give as a parameter
			const token_balance = await contract.balanceOf(address);
			return token_balance.toString();
		}
	}
	catch (err)
	{
		console.log(err);
	}
}
```
