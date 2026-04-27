# Bridge

NOX token bridge. ETH <-> Cellframe.

---

ETH to Cellframe: lock on eth, emit on cellframe
Cellframe to ETH: burn on cf, unlock on eth

ETH->CF is automated. Reverse is manual for now.

Bridge: `0x70Fb00075879E7D9d87EA5536c6c374cc2d14435`

---

Flow (ETH -> CF):

1. approve NOX to bridge
2. call `bridgeToCell(cfRecipient, amount)`
3. 0.5% fee taken, tokens locked
4. listener picks up event, waits 3 confirmations
5. processor creates cellframe emission
6. emission needs 3/4 owner cert signatures
7. DAG-POA validators include in block
8. tx_create transfers to user wallet

---

Backend:

```
bridge/
  config.py     env, rpc urls, abi
  listener.py   watches eth events
  processor.py  handles pending txs
  emission.py   cellframe emission logic
  cli.py        cellframe-node-cli wrapper
```

Run: `python bridge_listener.py`

---

DB tables:

`bridge_transactions` - all txs, status tracking
`bridge_addresses` - eth<->cf wallet links

Status: 0=new, 1=pending, 2=confirmed, 3=done, 4=failed

---

Cellframe emission commands:

```sh
# create
cellframe-node-cli token_emit -net Backbone -chain_emission zerochain \
  -token NOX -emission_value <amt> -addr <CF_ADDR> -certs nox_owner1

# sign (repeat for each cert)
cellframe-node-cli token_emit sign -net Backbone -chain zerochain \
  -emission <HASH> -certs nox_owner2

# process to chain
cellframe-node-cli mempool_proc -net Backbone -chain zerochain -datum <HASH>

# transfer to wallet
cellframe-node-cli tx_create -net Backbone -chain main \
  -from_emission <HASH> -certs nox_owner1
```

---

Env vars:

```
BRIDGE_ADDRESS=0x70Fb...
REQUIRED_CONFIRMATIONS=3
POLL_INTERVAL=12
CELLFRAME_NETWORK=Backbone
EMISSION_CERTS=nox_owner1,nox_owner2,nox_owner3
DATABASE_URL=postgresql://...
```

---

Check status:

```sh
curl https://0xnox.com/api/v1/bridge/stats
cellframe-node-cli mempool_list -net Backbone -chain zerochain
tail -f /var/log/0xnox-bridge.log
```

---

Frontend:

```
js/bridge/
  core.js           state
  data.js           fetch
  transfer.js       form
  transfer-execute.js  txs
  reverse.js        cf->eth
```

---

Stuck emission? Check mempool, might need validators. We run a masternode that can help.

Never restart the node unless absolutely necessary.
