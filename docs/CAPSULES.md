# Capsules

NONOS app marketplace. Devs build capsules, upload to IPFS, register on-chain. Users download and run.

---

Capsule = signed NONOS app package

Contains: ELF binary + manifest + assets + signature

Format: `.noxc`

Registry: `0xB47FBd4E668faD29C374523B1f7F82EA8ba178D7`

---

File structure:

```
+------------------+
| NOXC Header      |  64 bytes
+------------------+
| Manifest (JSON)  |
+------------------+
| Binary (ELF)     |
+------------------+
| Assets           |  optional
+------------------+
| Signature        |  64 bytes
+------------------+
```

Header:
```
0x00  u32 magic = 0x43584F4E ("NOXC")
0x04  u16 version = 0x0001
0x08  u32 manifest_offset/size
0x10  u32 binary_offset/size
0x18  u32 assets_offset/size
0x20  [32] binary_sha256
```

---

Manifest:

```json
{
  "id": "0x...",
  "name": "my-app",
  "version": "1.0.0",
  "description": "...",
  "dev_addr": "0x...",
  "dev_pubkey": "0x...",
  "caps": "0x0003",
  "mem_min": 16,
  "mem_max": 256,
  "price": "0",
  "category": "utility",
  "binary_sha256": "0x..."
}
```

id = keccak256(name:version)

---

Capabilities bitmap:

```
0: network
1: filesystem
2: graphics
3: audio
4: usb
5: raw disk
6: kernel
7: crypto hw
```

NONOS enforces at runtime. cant do what manifest doesnt declare.

---

Build flow:

1. write app for nonos (rust, no_std, x86_64-nonos target)
2. compile to elf
3. open 0xnox.com capsule builder
4. connect wallet
5. generate/import ed25519 signing key
6. upload elf + assets
7. fill manifest, select caps
8. build capsule
9. upload to ipfs
10. publish to registry contract

---

Registry contract:

```solidity
createCapsule(payToken, name, ipfsCid, price)
updateCapsule(capsuleId, ipfsCid, price)
purchaseCapsule(capsuleId)
```

Purchases go to dev_addr. Free apps = price 0.

---

Signing:

Ed25519 keypair. Private key stays in browser localStorage (encrypted). Public key in manifest. Signature covers whole capsule.

NONOS bootloader verifies sig before running anything. Bad sig = wont run.

---

IPFS:

Capsules stored on IPFS. CID registered on chain. Uses public gateways.

Set custom gateway:
```js
localStorage.setItem('ipfs_gateway', 'https://...')
```

---

Frontend:

```
js/capsule/
  core.js         state
  keys.js         keypair mgmt
  manifest.js     build manifest
  builder.js      assemble capsule
  ipfs.js         upload
  registry.js     contract calls
  data-loader.js  fetch list
```

---

Building for NONOS:

Target: `x86_64-unknown-nonos`

```sh
cargo build --release --target x86_64-unknown-nonos
```

No libc, freestanding, position independent. See NONOS SDK docs.

---

Verification on boot:

1. magic check
2. version check
3. sha256 matches manifest
4. signature valid
5. caps acceptable

Any fail = rejected, doesnt run.
