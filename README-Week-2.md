# Integrate your own contracts into Uniswap Fork

[Week 2 recording](https://drive.google.com/drive/folders/1-R8xd1Q7K2JD0TcfVp4BzEk-e__PiCKW) is posted now.

## deploy your own Factory and Router smart contracts
You should already have cloned the course repo from Week 1. If not, clone it: 
```
git clone https://github.com/BlockDevsUnited/DEX-Course-u-exchange
cd DEX-Course-u-exchange
```

### Deploy Factory contract
Copy contracts/UExchangeFactory_flat.sol to Remix

Compile Contract
- Change Compiler version to 0.5.16

Run&Deploy contract
- Environment: Injected Web3
- Account: Copy your xDai address from metamask
- Contract: ```UExchangeFactory - contracts/UExchangeFactory_flat.sol```
- Deploy: Takes one argument - expand to enter it
  - _FEETOSETTER: you can copy your address on xDai from metamask

Verify Contract
- Find the contract on https://blockscout.com
- Search by your address and click the contract address from Transactions tab   
- Go to "Code" tab and click on "Verify&Publish"
- Choose "Via flattened source code" and click "Next"
- Fill in the form with Contract Name, Optimization value and copy & paste contract code in the field "Enter the contract code"
- For "Try to guess arguments", choose "YES" and click on "Verify". Wait for the code to get successfully published.

In ```scripts/contracts_info.json```
- update factory_contract_address with the address of the newly deployed contract

### Calculate INIT_CODE_PAIR_HASH
INIT_CODE_HASH is a hash of the initcode of a contract. The initcode is the code that creates the bytecode that is stored on-chain. 
Go back to Remix and open Deploy tab and Deployed Contracts and expand UExchangeFactory contract  
- Click on the blue button INIT_CODE_PAIR_HASH and copy the hex value starting with 0x
- In ```scripts/contracts_info.json``` update init_code_pair_hash with the hex value from the previous line

### Deploy Router Contract

Copy contracts/UExchangeRouter_flat.sol to Remix
- Search the contract for string "init code hash" for hex to find this line (around 129):
```
hex'fbc46437b443cd8d82755f5a02d9fc3e51b9ae6ddc401bd1158b1cb07013e265' // init code hash
```
- Replace the hash with the PAIR_INIT_CODE_HASH from your previous step and make sure to remove the 0x in the hash

Compile Contract
- Change Compiler version to 0.6.6
- Enable optimization: 200

Run&Deploy contract
- Environment: Injected Web3
- Account: Copy your xDai address from metamask
- Contract: ```UExchangeRouter02 - contracts/UExchangeRouter_flat.sol```
- Deploy: Takes two arguments - expand to enter them
  - _FACTORY: This is the address of your factory contract that you deployed in the previous step. Copy it from blockscout.com.
  - _WETH: This is the WETH contract address on xdai chain. You can find it on [blockscout.com]
  - Go to https://www.blockscout.com/xdai/mainnet/tokens and Tokens tab and All and search for WETH and copy the address of WETH with maximum holders.
- Verify contract (optional) 

In ```scripts/contracts_info.json``` update "router_contract_address" with the address of our deployed router contract

## Update SDK
Clone the source repository locally
```bash
git clone https://github.com/UdotCASH/u-exchange-sdk
cd u-exchange-sdk
```
In ```src/constants.ts``` update these two lines with your contract values:
```
export const FACTORY_ADDRESS = '0xF3eAD80d7ad58CbA12F73F295d0Dc845c0dA2B39'
export const INIT_CODE_HASH = '0xfbc46437b443cd8d82755f5a02d9fc3e51b9ae6ddc401bd1158b1cb07013e265'
```
In ```package.json``` update package version

Next, prepare the package and publish to npmjs
1. Install dependencies ```npm install```
1. Login to npm: ```npm login```
1. Publish ```npm publish --access public```
1. Verify that your package is in https://www.npmjs.com/package/ by searching for it.

## Integrate the interface

### Update contract info in the interface source
Go to the interface folder
```
cd DEX-Course-u-exchange
```

In ```package.json```
- update u-exchange-sdk package with the name and verion of your SDK package deployed in previous step
- run `yarn`

In src folder update with the sdk package
```
cd src
find . -type f -print -exec sed -i s,'\@udotcash\/u\-exchange\-sdk','\@violeta\.at\.bww\/dex\-course\-u\-exchange\-sdk',g {} \;
```

In ```src/constants/v1/index.ts```
- update factory address
- update u-exchange package

In ```src/constants/index.ts```
- update router address

### Test the interface locally
In the terminal start the interface
```
yarn
yarn start
```
Then open in your browser http://localhost:3000

Follow [Week 1 tutorial](README-Week-1.md) to publish the interface to github pages
