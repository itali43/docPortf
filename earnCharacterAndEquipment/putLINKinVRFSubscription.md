# TOP UP LINK TOKEN INTO VRF SUBSCRIPTION

###### This details how to send LINK token in the right form in order to top up the VRF Subscription.

## Background and Context

- We use the Chainlink VRF Subscription to get provably random numbers for our Last Remains NFT.
- We need their token, LINK, to use the Chainlink VRF system that provides the random numbers.
- This explains how to do that, and how to put it in the right place so that it can be used, let's go!---------

## How to get the LINK in the first place

### Mainnet

1. First buy some MATIC. The benevolent and powerful Jess has done this in past.
2. Once you have it in your wallet, head over to (Uniswap)[https://app.uniswap.org/#/swap], select polygon as a network.
3. You're going to swap your MATIC -> CHAINLINK so search for chainlink (LINK) in the second textfield's token selector
4. Decide how much to buy, approve and transact!
5. You now have a version of LINK, but likely not the one that you use in the VRF. This other version has a special function (ERC667 explains it if you wanna go deep later)
6. (Go to Peg Swap to convert)[https://pegswap.chain.link/] your 'more tradeable' LINK into VRF compatable LINK
7. That's it for conversion and acquisition!

### Testnet

What about test net? It's easy!

Go to the mumbai polygon LINK (faucet)[https://faucets.chain.link/mumbai] and then add it to your subscription via the UI.. mainnet is the only one that needs converting.

## How to send it to the VRF

You have your LINK now and it's converted into the proper kind of LINK (with ERC667 compatability, cool right?), so now it's time to send it to the VRF. This part has a UI, so not so bad.

### IMPORTANT NOTE, RE: TOP UPS!!! ANYONE CAN DO THIS:

That's right, you don't have to own the VRF Subscription account... if you want to top off a random person's VRF account, there's no stopping you. But let's stick to ours for now.

### Mainnet

As of writing, we use this mainnet subscription ID (721), which corresponds to (this user interface on the chainlink site)[https://vrf.chain.link/polygon/721].

Once you make sure you're on the right network (in the UI and on your wallet) and connect the wallet you have your VRF-ready LINK in, you'll see a button in the top right labeled `Actions`. Click it and choose "Fund Subscription". It should show a textfield labeled "Add funds (LINK)". If you converted your LINK right you should see your wallet balance below the field. Select how much you want to send, and click "Confirm". This will take you through a wallet txn process, and then in a few blocks your LINK will show up topped off!

### Testnet

(Hop over here)[https://vrf.chain.link/mumbai/4046], presuming our testnet Subscription ID is still 4046. You'll see a button in the top right labeled `Actions`. Click it and choose "Fund Subscription". It should show a textfield labeled "Add funds (LINK)". If you converted your LINK right you should see your wallet balance below the field. Select how much you want to send, and click "Confirm". This will take you through a wallet txn process, and then in a few blocks your LINK will show up topped off!

## What if I want to add or remove a Consumer? Is that in the Gnosis Safe?

Unfortunately no. Defender/Gnosis Safe didn't look to have a coherent integration (you have to use a CLI to transfer it and receive it..).

So it is owned by an EOA wallet. Alan should have it, or know who has it.
