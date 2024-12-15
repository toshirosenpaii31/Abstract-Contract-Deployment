# Abstract-Contract-Deployment

1. Setup Environment
Install Node.js (v18.0.0 or later) and Hardhat CLI. If you're on Windows, use WSL2 for better compatibility.
Create a project folder and initialize a Hardhat project:

INPUT COMMAND:
mkdir my-abstract-project && cd my-abstract-project
npx hardhat init

( Select "Create a TypeScript project" for recommended settings.)

2. Install Dependencies
Run the following command to install necessary plugins for Abstract:


INPUT COMMAND:

npm install -D @matterlabs/hardhat-zksync @matterlabs/zksync-contracts zksync-ethers@6 ethers@6

3. Configure Hardhat
Update hardhat.config.ts with the Abstract network configuration:

INPUT COMMAND:

import { HardhatUserConfig } from "hardhat/config";
import "@matterlabs/hardhat-zksync";

const config: HardhatUserConfig = {
  zksolc: { version: "latest", settings: { enableEraVMExtensions: false } },
  defaultNetwork: "abstractTestnet",
  networks: {
    abstractTestnet: {
      url: "https://api.testnet.abs.xyz",
      ethNetwork: "sepolia",
      zksync: true,
      verifyURL: "https://api-explorer-verify.testnet.abs.xyz/contract_verification",
    },
  },
  solidity: { version: "0.8.24" },
};
export default config;

4. Write and Compile Your Contract
Create a file contracts/HelloAbstract.sol with the following example:

INPUT COMMAND:

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract HelloAbstract {
    function sayHello() public pure returns (string memory) {
        return "Hello, World!";
    }
}


Compile it:

npx hardhat clean
npx hardhat compile --network abstractTestnet


5. Deploy the Contract
Obtain testnet ETH from the faucet or bridge from Sepolia.
Set your wallet's private key in the Hardhat environment:

INPUT COMMAND : 

npx hardhat vars set DEPLOYER_PRIVATE_KEY

Write a deployment script deploy/deploy.ts:
typescript
Copy code

import { Wallet } from "zksync-ethers";
import { HardhatRuntimeEnvironment } from "hardhat/types";
import { Deployer } from "@matterlabs/hardhat-zksync";

export default async function (hre: HardhatRuntimeEnvironment) {
    const wallet = new Wallet(hre.vars.get("DEPLOYER_PRIVATE_KEY"));
    const deployer = new Deployer(hre, wallet);
    const artifact = await deployer.loadArtifact("HelloAbstract");
    const contract = await deployer.deploy(artifact);
    console.log(`Contract deployed to ${await contract.getAddress()}`);
}


Deploy the contract

npx hardhat deploy-zksync --script deploy.ts


6. Verify the Contract
Run:


INPUT COMMAND :

npx hardhat verify --network abstractTestnet YOUR_CONTRACT_ADDRESS
