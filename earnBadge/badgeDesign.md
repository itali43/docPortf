# Badges Design

# Overview

Users can earn badges through interacting with GameFi communities or contributing to the Earn Alliance. Badges are unique NFTs that will have the original minted owner attached to them and can be shown on the owner's profile only. Badges have a separate utility where they can earn tokens when staked on their profile. In this case, there are badges that can be owned or earned which they can be shown off for investment purposes.

# How to Earn on Earn Alliance!

Earning a badge will be calculated by the current state of completion which Earn Alliance verifies. Earn Alliance will check various data sources to validate the completion of the badge state which is triggered by the user to verify completion. Verifying completion is a heavy task and must be protected by CAPTCHA rules and have cool-down periods to prevent overloading our systems.

Once a badge has been verified to be completed, the user may request to MINT the badge via providing a signed payload by our backend servers which our smart contracts verify and mint the specified NFT. Protection mechanisms to ensure this message can only be minted once must be in place. To Mint, we will take a small fee equal to $1.337 dollars in MATIC. In the future, we will take fees in $ECT and should consider making the fee token flexible and driven by the signed payload.

Once the token has been minted, the user should be able to go to their profile and see their unlisted tokens. There they can “List” their token (otherwise known as staking) which will attach it to their profile and remove the transferability of the token.

Each NFT will have its own calculated value of points which is used as shares within the staking pool of Earn Alliance. Based on the ownership of the staking pool, the user will get a % of the daily rewards.

In the beginning, $ECT rewards are calculated, but not mint-able until the token launch itself.

## Genesis NFT

We will be giving out 1337 Genesis NFTs with randomized weights to earn $ECT tokens and 5 different rarities. We will sell these at a .01337 eth floor and need to ensure we have the best strategy for not incurring high gas fees. A max 5 can be minted per address.

# Wallets

We do not have any wallet mechanism on our website at the moment. Accounts will need to be able to connect their wallets via metamask or other wallet means in order to sign that they own the wallet.

### User Mint Procedure

1. The user approaches the backend to verify his/her minting status.
2. If mint status is ok, the user can request a unique signed message for the user to mint an NFT.
3. The user passes in the signature and signed message to the contract.
4. The contract verifies the signature and message received from the user and mints the NFT.

### Smart contract badge mint flow

The deployed contract has a private variable which has the address of the backend signer. It receives the signature and signed message.

1. If the signature and signed message checks out then the signed message is recorded in the contract (so that it cannot be used again) and contacts the Chainlink VRF for a random number.
2. The transaction is reverted if the sign and/or signed message is not valid.
3. Chainlink VRF sends back the random number by invoking the `filfillRandomWords()` function on the smart contract.
4. The smart contract uses the random number to assign properties to the NFT.
   1. It records the minted NFT’s properties to the Badge registry (a `mapping` from `badgeId` to a struct that records the properties).
   2. It emits an event that the NFT was minted
5. The user can invoke the `getBadge()` function to get the properties of the NFT.
6. The user obtains a new signed message and signature, and passes that along with the URL that hosts `metadata.json` for the given `badgeId` to the `setTokenURI()` function. The smart contract verifies the sign and then sets the URI for the given `badgeId` .
7. Anyone (any other user or even a marketplace like OpenSea) can query the `tokenURI()` method using the `badgeId` and get the link to the `metadata.json` and then render the properties of that Badge NFT.

### Signing Transactions

The below video demonstrates how the message signing works. It requires the use of `ethers` node module.

[https://www.youtube.com/watch?v=Y6MtQG6IEGk](https://www.youtube.com/watch?v=Y6MtQG6IEGk)

[This](https://github.com/earn-alliance/Badge-Smart-Contract) repo contains the link to the badge contracts. Inside the `contracts` directory there is a contract `TestBadge.sol` which is used for simulating the functionality of the partnership badges. This contract has a function `verify(string calldata *message, bytes calldata signature)` which returns true if the `_*message` is signed by the contract owner/deployer. Below code block demonstrates the usage of the function. It presents a test of the contract using chai Framework.

```jsx
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("TestBadge", function () {
  let testbadgeContract, owner, addr1;

  before(async function () {
    [owner, addr1] = await ethers.getSigners(2);

    const TestBadge = await ethers.getContractFactory("TestBadge");

    testbadgeContract = await TestBadge.deploy(1000, 414);
    await testbadgeContract.deployed();
  });

  it("Verify Signature Works", async function () {
    const message = "Test Message";
    const hash = await testbadgeContract.getMessageHash(message);
    const validSig = await owner.signMessage(ethers.utils.arrayify(hash));
    const invalidSig = await addr1.signMessage(ethers.utils.arrayify(hash));

    expect(await testbadgeContract.verify(message, validSig)).to.equal(true);
    expect(await testbadgeContract.verify(message, invalidSig)).to.equal(false);
  });
});
```

In the above code block, `owner` is the contract deploying wallet while `addr1` is another wallet. Inside the test titled “Verify Signature Works”, we define a test message inside the `message` constant and then obtain the hash of it using the function `getMessageHash(string memory _message)` which is part of `TestBadge.sol` as well.

We then use `ethers.utils.arrayfy()` to convert the hash to bytes and then sign it first by `owner` wallet and then by `addr1` wallet. The message signed by the former is supposed to evaluate to true using the `verify(string calldata *message, bytes calldata signature)` method.\*

## The Smart Contract Design

The smart contract will be based upon _ERC721_ standard and will have a modified `safeMint()` function. The minter will have to first obtain a signed payload from the backend server. When the minter invokes the special case of `safeMint()`, called `badgeMint()` that will check the total supply of that badge hasn’t been exceeded, the signed payload sent as a method parameter, as well as the enum of the badge that is being minted (also a sent method parameter).

<aside>
🗒️ The enum of the badge will correspond to the badges we mint.  So our first badge (Discord badge) will be the 0th badge, etc.  This allows us to have multiple badges without reproducing the same slightly modified contract every time we want to launch a new badge.

</aside>

If the signature matches the signature which was set by the deployer/owner upon deployment and the badge enum is valid, then the request to mint an NFT will be considered valid. After this the `safeMint()` function (which is called internally by `badgeMint()`) will send a request to Chainlink VRF for one `unint256` random number. It will map the request ID minter address and the signed payload. This will conclude the `safeMint()` function.

The actual NFT mint process will be completed when the Chainlink VRF invokes the `fulfillRandomness()` function inside the smart contract. The function takes in the request ID and the random number (received in an array of `uint256`). Based on the random number received, the NFT will be minted and an event regarding this will be emitted from the contract.

After the NFT has been minted, the minter will invoke the `getBadge()` function in the contract. This will return all the characteristics of the NFT badge along without the signed payload which was used to mint the badge. These data can be used to create a `metadata.json` file and then publicly host it somewhere (for example, IPFS or Arweave).

There will be a `setBadgeURI()` function on the contract. This can only be invoked by providing the URL where the `metadata.json` has been hosted and a new signed payload from the backend. Once the signed payload has been verified, the link will be recorded to the badge ID.

The `tokenURI()` method can then be invoked to get the URI of the `metadata.json` file.

### Points to Note:

- When the contract is deployed, the deployer sets the a valid signature which is then used to verify invocations to `badgeMint()` / `safeMint()` and `setBadgeURI()` methods.
- The contract will maintain a struct that would contain the characteristics of the Badge that is minted. As such the properties of the badge and the range of those properties will have to be predefined in the contract.
- A mapping from request ID to bool will be maintained to keep a record of all signed payloads. This will prevent anyone from using an old Signed Payload. This is done on the backend of the server.
- The `tokenURI()` method will have the same function prototype as specified in OpenSea metadata standards. This will allow an additional functionality for the Badge being listed on OpenSea/Seaport.
- There will be 3 different contracts for 3 different kinds of NFTs as discussed over the call and over Slack on 30/05/2022.

## NFT Design Choices

### Gatcha / Randomness

We are deciding to create a pool of NFTs which have a distribution of rarity. What’s cool about this?

1. Early users get a higher chance of getting rare NFTs.
2. People don’t know what they will get, so they are incentivized to come and see what’s inside of the box.

For example, if there are 5000 NFTs and 5% (250) of them have the ultra-rare version of the NFT pack, then the first person who joins the community to mint has a 5% chance of getting it.

NFT rarity will not only look better but reap better rewards for the user.

### Unique per wallet

Only one mint per wallet is allowed. That means the backend would verify that something has been accomplished for a specific address/account.

### Soulbound & Non-Escrow Staking

Our NFTs will be soul bound, meaning, you cannot transfer them to another wallet once you’ve got them. They will also be non-escrow stake-able, though this second feature need not be used as yet. Inspired by erc721nes.
