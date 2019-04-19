# EOSIO cross chain token contracts
## Terms
`source` the network where a cross chain token transfer is sent from

`target` the network where a cross chain token transfer is sent to

`native` the real token on it's native network (EOS on EOS network, TLOS on Telos network, WBI on Worbli network)

`peg` the "IOU" representation of a native token

`gateway` the contract that sends/receives tokens on either the source or target

`oracle` the off-chain program that observes multiple networks and relays the information between them

## Gateway contract
### Tables
extended_assets - To track registered/approved native extended_assets by an id (same id on all other networks?)
remote_networks - To track remote networks by an id
balances - To track deposits that have not been sent or withdrawn
transfers - Pending transfers that were created during the send action


### Actions
open(account, asset_id) - To pay ram to create a row to deposit an asset into

send(account, asset_id, network_id, remote_account, amount, memo)

withdraw(account, asset_id, amount)

transfer(account, asset_id, amount, memo)




## Peg token contract
### Tables
### Actions

## Native to peg
alice wants to convert 1 TLOS on the Telos network (native on source network) to 1 TLOSPEG on the EOS network (peg on target network)

1. alicetlos sends a trx with 2 or 3 actions:
	* IF NOT ALREADY A BALANCE: gateway::open to pay for ram
	* eosio.token::transfer to transfer 1 TLOS to gateway
	* gateway::send - to tell the gateway to reduce from alice's balance that she had just transferred to the contract in the previous action, and to create an outgoing transfer row for oracles to observe and notify the target network's gateway about
2. Oracles observe this and start calling the transfer action on the target gateway
3. Once the oracles have reached quorum, the gateway contract will do an inline action to the peg token contract to issue 1 TLOSPEG to alice
4. Once transferred, the oracles will call completetransfer on the sending network, once quorum is reached on completion, the source gateway contract will delete the transfer row

## Peg to native



