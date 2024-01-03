## 1.Pre-priquire
```bash
sudo apt-get install build-essential pkg-config libssl-dev clang git-lfs
git lfs install
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"
```
## 2.Cloning the repository
```bash
git clone https://github.com/penumbra-zone/penumbra
cd penumbra && git fetch && git checkout v0.64.1
```
## 3.Building the pcli client software
```bash
cargo build --release --bin pcli
```
## 4.Wallet
### Creating a new wallet
```bash
cargo run --quiet --release --bin pcli -- --version
cargo run --quiet --release --bin pcli init soft-kms generate
```
### Import a wallet
```bash
cargo run --quiet --release --bin pcli init soft-kms import-phrase
```
### View address
```bash
cargo run --quiet --release --bin pcli -- view address
```
## 5.Sync
```bash
cargo run --quiet --release --bin pcli view sync
cargo run --quiet --release --bin pcli view balance
cargo run --quiet --release --bin pcli view staked
```
## sent token: from number account to wallet address
```bash
cargo run --quiet --release --bin pcli tx send <No>penumbra --source <number account: 0,1,..> --to <wallet address>
```
## 6.Contributing : The minimum bid for this ceremony is 100penumbra.
```bash
cargo run --quiet --release --bin pcli -- ceremony contribute --phase 1 --bid 300penumbra --coordinator-address penumbra1qvqr8cvqyf4pwrl6svw9kj8eypf3fuunrcs83m30zxh57y2ytk94gygmtq5k82cjdq9y3mlaa3fwctwpdjr6fxnwuzrsy4ezm0u2tqpzw0sed82shzcr42sju55en26mavjnw4
```
## Upgrading pcli
[Updating pcli](https://guide.penumbra.zone/main/pcli/update.html)https://guide.penumbra.zone/main/pcli/update.html
