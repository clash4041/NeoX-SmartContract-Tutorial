# How to Create a Smart Contract on Neo X  

**Author:** Ujjwal

## Introduction  
In this tutorial, we’ll walk through the process of creating, deploying, and interacting with a smart contract on **Neo X**. Smart contracts on Neo are written in **C#** and compiled into NeoVM-compatible bytecode.  

By the end of this guide, you'll have a working **Neo smart contract** that performs simple **token transfers**, deployed on the **Neo TestNet**.  

## Prerequisites  
Before we start, ensure you have:  
- **.NET SDK** installed ([https://dotnet.microsoft.com/en-us/download](https://dotnet.microsoft.com/en-us/download))  
- **Neo N3 CLI** installed ([https://developers.neo.org/docs/n3/setup/cli](https://developers.neo.org/docs/n3/setup/cli))  
- **Neo Express** for local contract deployment ([https://developers.neo.org/docs/n3/tools/neo-express](https://developers.neo.org/docs/n3/tools/neo-express))  
- A **Neo wallet** (e.g., Neon Wallet)  

## Step 1: Setting Up the Project  
```sh
mkdir NeoX-Contract && cd NeoX-Contract
dotnet new classlib -n NeoXToken
cd NeoXToken
dotnet add package Neo.SmartContract.Framework


## Step 2: Writing the Smart Contract

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

## Step 3: Compiling the Smart Contract

Run the following command to compile the smart contract into .nef format:

dotnet build -c Release

The compiled contract will be available in bin/Release/netstandard2.1/NeoXToken.nef.

## Step 4: Deploying the Smart Contract

Using Neo Express (Local TestNet)

neo-express create
neo-express run
neo-express contract deploy NeoXToken.nef --wallet mywallet.json

## Step 5: Interacting with the Smart Contract

Checking Balance

neo-express contract invoke NeoXToken BalanceOf --wallet mywallet.json --args "[<your_address>]"

Transferring Tokens

neo-express contract invoke NeoXToken Transfer --wallet mywallet.json --args "[<from_address>, <to_address>, 10]"

##Conclusion

You have successfully:
✅ Written a Neo X smart contract
✅ Compiled & deployed it on a local test network
✅ Interacted with it using CLI commands

To deploy on Neo TestNet, you’ll need GAS tokens and a Neo RPC node. Refer to https://developers.neo.org/docs/n3/tutorials for deployment.

Resources & Next Steps

https://developers.neo.org/

https://developers.neo.org/docs/n3/examples

https://github.com/neo-project
