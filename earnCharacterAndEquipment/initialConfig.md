This is to set things up after launch of initial character contracts
Deploy:

### Contract Setup:

#### (order overlaps with backend setup section so please read both)

- Launch Middleman and Personas contracts first, use addresses when launching Character Contract
- Set Treasury address
- Make certain all contracts and implementations have been verified
- Set/confirm setting of Middleman + Persona addresses in LRCharacter
- Add Persona Ceilings in LRCharPersonas (using initCeilings for first run)
- Max supplies of all active TokenIds set (use setMaxSupplies, for all at once, set very high since controlled on backend)
- Set minters in all contracts, if not set already
- Set Token URIs
- Make certain the Link Subscription for deployed network is topped off in the VRF UI

### Setup Related to Backend:

#### (order overlaps with contract setup section so please read both)

- create VRF subscription on chainlink
- fund the subscription with LINK
- generate a key pair to be used as signing key
- after contract deployment/setup, generate a sig and check if the tx pass through using tenderly’s simulator
- upload metadata jsons and images for all tokens

#### Finally:

- Move ownership over to Earn Gnosis Safe
