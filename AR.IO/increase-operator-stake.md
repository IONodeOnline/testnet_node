# steps to check and stake the node
## 1. Check wallet balance information
```bash
https://dev.arns.app/v1/contract/bLAgYxAdX2Ry-nt6aH2ixgvJXbpsEYm28NgJgyqfs-U/balances/<wallet address>
```
=> "balance": < numbers >
## 2. Edit file increase-operator-stake.ts with update => const qty = < number of balance >
```bash
git clone https://github.com/ar-io/testnet-contract
cd testnet-contract
nano tools/increase-operator-stake.ts
```
## 3. Run
```bash
yarn ts-node tools/increase-operator-stake.ts
```
