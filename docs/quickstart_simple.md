# Quickstart: Simple Flow

This section describes how to create a datatoken and host a dataset using the simplest flow.

Here are the the steps:

1. Initialize services
2. Create a new node.js project
3. Install dependancies
4. Create a config file and update contract addresses
5. Publish a new data token 
6. Mint 100 tokens
7. Transfer tokens between users.
8. Host a dataset

Let's go through each of these in detail.

## 1. Initialize services

We start by initializing the services. To do this, we clone the Barge repository and run it. This will run the current default versions of [Aquarius](https://github.com/oceanprotocol/aquarius), [Provider](https://github.com/oceanprotocol/provider-py), and [Ganache](https://github.com/trufflesuite/ganache-cli) with [our contracts](https://github.com/oceanprotocol/ocean-contracts) deployed to it.

```bash
git clone https://github.com/oceanprotocol/barge.git
cd barge/
./start_ocean.sh --with-provider2 --no-dashboard
```
## 2. Create a new node.js project

Start by creating a new Node.js project. Open a new terminal and enter the following commands: 

```bash
mkdir ocean-quickstart
cd ocean-quickstart
npm init
# Answer the questions in the command line prompt
cat > index.js
# On linux press CTRL + D to save
```

## 3. Install dependancies

Open the package.json file in a text editor and update the dependancies to include the following: 

```JSON
  "dependencies": {
    "@oceanprotocol/contracts": "^0.5.6",
    "@oceanprotocol/lib": "^0.6.5",
    "web3": "^1.3.0"
  }
```

Now in your terminal run the following command: 

```bash
npm install
```

## 4. Create a config file and update contract addresses

Create a new config.js file: 

```bash
cat > config.js
```

Now open the config.js in your code editor and enter the following:

```Javascript
const { ConfigHelper } = require("@oceanprotocol/lib");
const Web3 = require("web3");
const defaultConfig = new ConfigHelper().getConfig("development");

const urls = {
  networkUrl: "http://localhost:8545",
  aquarius: "http://localhost:5000",
  providerUri: "http://localhost:8030",
};

const contracts = {
  "DTFactory": "0x_YOUR_DTFactory_ADDRESS_",
  "BFactory": "0x_YOUR_DTFactory_ADDRESS_",
  "FixedRateExchange": "0x_YOUR_DTFactory_ADDRESS_",
  "Metadata": "0x_YOUR_Metadata_ADDRESS_",
  "Ocean": "0x_YOUR_Ocean_ADDRESS_"
};

const config = {
  ...defaultConfig,
  metadataCacheUri: urls.aquarius,
  providerUri: urls.providerUri,
  web3Provider: new Web3(urls.networkUrl),
};

module.exports = {
  config,
  contracts,
  urls,
};

```

Now check what your contract addresses are locally. In your terminal run:

```bash
cat ~/.ocean/ocean-contracts/artifacts/address.json
```

Next, update the contract addresses in your config.js file. Replace each of the place holders with the actual addresses that were outputted into your terminal. 

## 5. Publish a new data token 
Now open the `index.js` file in your text editor. Enter the following code and save the file:

```Javascript
const Web3 = require("web3");
const { Ocean, DataTokens } = require("@oceanprotocol/lib");

const { factoryABI } = require("@oceanprotocol/contracts/artifacts/DTFactory.json");
const { datatokensABI } = require("@oceanprotocol/contracts/artifacts/DataTokenTemplate.json");
const { config, contracts, urls } = require("./config");



const init = async () => {
  const ocean = await Ocean.getInstance(config);
  const blob = `http://localhost:8030/api/v1/services/consume`;

  const accounts = await ocean.accounts.list();
  const alice = accounts[0].id;
  console.log('Alice account address:', alice)

  const datatoken = new DataTokens(
    contracts.DTFactory,
    factoryABI,
    datatokensABI,
    new Web3(urls.networkUrl)
  );
  const tokenAddress = await datatoken.create(blob, alice);
  console.log(`Deployed datatoken address: ${tokenAddress}`);
};

init();
```

Now in your terminal, run the following command: 

```bash
node index.js
```

Congratulations, you've created your first Ocean datatoken! 🌊🐋

![alt text](https://github.com/oceanprotocol/contracts/blob/feature/1SS/v4PublisherFlow.jpg?raw=true)


Now save and run the `index.js` file:

```Bash
node index.js
```

In the terminal output you should now see the Data ID (did) outputed.  

Congratulations, you have published your first dataset! 🌊🐠🐡 Now you are ready for the [marketplace flow](docs/quickstart_marketplace.md). 