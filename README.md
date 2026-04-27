# 0xNOX

NOX ecosystem hub. Launches tokens, bridges to Cellframe, hosts NONOS apps.

https://0xnox.com

---

**Launchpad** - bonding curve token launches, graduate to uniswap
**Terminal** - trade, charts, comments
**Bridge** - ETH <> Cellframe for NOX
**Capsules** - NONOS app store, devs upload signed .noxc files
**Staking** - lock NOX, earn rewards, NFT boosts

Separate docs in `/docs` folder.

---

Vanilla JS frontend, no react/vue nonsense. FastAPI backend. PostgreSQL. Talks to ETH via ethers, Cellframe via node CLI.

```
frontend/js/
  launchpad/   trading/   bridge/   capsule/   staking/

backend/
  api/routes/   bridge/   db/
```

---

Contracts (ETH mainnet):

NOX `0x0a26c80Be4E060e688d7C23aDdB92cBb5D2C9eCA`
Factory `0xed32A86f95c5593Aac4dB1937Ed21078da200d7c`
Bridge `0x70Fb00075879E7D9d87EA5536c6c374cc2d14435`
Staking `0xa94d6009790Ba13597A1E1b7cF4e1531eA513613`
NFT Pass `0x7b575DD8e8b111c52Ab1e872924d4Efd4DF403df`
Capsules `0xB47FBd4E668faD29C374523B1f7F82EA8ba178D7`

---

Run locally:

```sh
cd backend && python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt && uvicorn api.main:app --port 8085
# frontend is static, nginx or python -m http.server
```

Prod: frontend at `/var/www/0xnox.com` (nginx), backend at `/opt/0xnox` (uvicorn + bridge_listener).

---

Built for NONOS. The OS that boots from RAM, runs signed capsules, no disk.
https://nonos.io
