##How to Create a Smart Contract on Neo X  

**Author:** Ujjwal   

## Introduction  
This tutorial will guide you through **creating, deploying, and interacting** with a smart contract on **Neo X**. Smart contracts on Neo are written in **C#** and compiled into NeoVM-compatible bytecode.  

By the end of this guide, you'll have a working **Neo smart contract** that enables **token transfers**, deployed on the **Neo TestNet**.  

## Table of Contents  
- [Prerequisites](#prerequisites)  
- [Setting Up the Project](#step-1-setting-up-the-project)  
- [Writing the Smart Contract](#step-2-writing-the-smart-contract)  
- [Compiling the Smart Contract](#step-3-compiling-the-smart-contract)  
- [Deploying the Smart Contract](#step-4-deploying-the-smart-contract)  
- [Interacting with the Contract](#step-5-interacting-with-the-smart-contract)  
- [Deploying on Neo TestNet](#deploying-on-neo-testnet)  
- [Troubleshooting](#troubleshooting)  
- [Next Steps](#next-steps)  

---

## Prerequisites  
Before we start, ensure you have the following installed:  

- **.NET SDK** → [https://dotnet.microsoft.com/en-us/download](https://dotnet.microsoft.com/en-us/download)  
- **Neo N3 CLI** → [https://developers.neo.org/docs/n3/setup/cli](https://developers.neo.org/docs/n3/setup/cli)  
- **Neo Express** for local deployment → [https://developers.neo.org/docs/n3/tools/neo-express](https://developers.neo.org/docs/n3/tools/neo-express)  
- **A Neo Wallet** (e.g., Neon Wallet) → [https://neonwallet.com/](https://neonwallet.com/)  

---

## Step 1: Setting Up the Project  
Create a new C# smart contract project:  

```sh
mkdir NeoX-Contract && cd NeoX-Contract
dotnet new classlib -n NeoXToken
cd NeoXToken
dotnet add package Neo.SmartContract.Framework


---

##Step 2: Writing the Smart Contract

Create a new file NeoXToken.cs inside the NeoXToken directory and add the following code:

using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Services;
using Neo.SmartContract.Framework.Native;
using System.Numerics;

public class NeoXToken : SmartContract
{
    private static readonly StorageMap Balances = new(Storage.CurrentContext, "balances");

    public static void OnDeploy() => Storage.Put(Storage.CurrentContext, "deployed", "true");

    public static bool Transfer(UInt160 from, UInt160 to, BigInteger amount)
    {
        if (!Runtime.CheckWitness(from) || amount <= 0) return false;

        BigInteger fromBalance = (BigInteger)Balances.Get(from);
        if (fromBalance < amount) return false;

        Balances.Put(from, fromBalance - amount);
        BigInteger toBalance = (BigInteger)Balances.Get(to);
        Balances.Put(to, toBalance + amount);

        return true;
    }

    public static BigInteger BalanceOf(UInt160 address) => (BigInteger)Balances.Get(address);
}


---

##Step 3: Compiling the Smart Contract

Run the following command to compile the smart contract into .nef format:

dotnet build -c Release

The compiled contract will be available in:

bin/Release/netstandard2.1/NeoXToken.nef


---

##Step 4: Deploying the Smart Contract

Using Neo Express (Local TestNet)

neo-express create
neo-express run
neo-express contract deploy NeoXToken.nef --wallet mywallet.json


---

##Step 5: Interacting with the Smart Contract

Checking Balance

neo-express contract invoke NeoXToken BalanceOf --wallet mywallet.json --args "[<your_address>]"

Transferring Tokens

neo-express contract invoke NeoXToken Transfer --wallet mywallet.json --args "[<from_address>, <to_address>, 10]"


---

Deploying on Neo TestNet

To deploy on Neo TestNet, follow these steps:

1. Get TestNet GAS → https://neowish.ngd.network/faucet


2. Use a Public RPC Node → https://developers.neo.org/docs/n3/network/testnet


3. Deploy the Contract using Neo CLI:

neo-cli deploy NeoXToken.nef --wallet testnet_wallet.json


4. Verify the Contract on https://explorer.onegate.space/




---

## Troubleshooting  

| Error | Solution |
|-------|---------|
| `neo-express command not found` | Ensure Neo Express is installed and added to PATH |
| `Compilation fails` | Check .NET SDK version and ensure Neo framework is installed |
| `Contract deployment fails` | Ensure the wallet has enough GAS tokens |
| `Invoke function returns false` | Verify if `from_address` has sufficient balance |



---

#Next Steps

Learn Smart Contract Security → https://developers.neo.org/docs/n3/guidelines/smart-contract-security

Explore Decentralized Apps (dApps) on Neo

Join the Neo Developer Community → https://discord.com/invite/neo
