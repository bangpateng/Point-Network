<p style="font-size:14px" align="right">
<a href="https://t.me/bangpateng_group" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
<a href="https://bangpateng.com/" target="_blank">Visit our website <img src="https://user-images.githubusercontent.com/38981255/184068977-2d456b1a-9b50-4b75-a0a7-4909a7c78991.png" width="30"/></a>
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/38981255/185550018-bf5220fa-7858-4353-905c-9bbd5b256c30.jpg">
</p>

# #Point Network Testnet Incentivized

## Minta Faucet Menggunakan Address Metamask (Kalo Udah Sekip)

- Isi Form : https://pointnetwork.io/testnet-form (Tunggu 24 Jam Akan Dapat Email dan Coin Test Masuk ke Metamask)
- Add RPC di Metamask (Untuk Memastikan Udah Ada Faucet Landing)

```
Network Title: Point XNet Triton
RPC URL: https://xnet-triton-1.point.space/
Chain ID: 10721
SYMBOL: XPOINT
```

# #Instalisasi Bahan-Bahan Node

## Update

```
sudo apt-get update
sudo apt-get install build-essential
```
```
sudo apt install make
sudo apt install git
sudo apt install screen
```

## Instal Go

```
if ! [ -x "$(command -v go)" ]; then
  ver="1.18.2"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
fi
```

Check Go Version : `go version`

## Clone Repository

```
git clone https://github.com/pointnetwork/point-chain
```

## Open Folder

```
cd point-chain
git checkout xnet-triton
make install
```

# #Install Node
```
evmosd config keyring-backend file
evmosd config chain-id point_10721-1
```
```
evmosd keys add <Nama_Wallet> --keyring-backend file
```
Nama Wallet : anti Dengan Nama Wallet Kalian Tanpa (<>)
(Jangan Lupa Backup Address dan Pharse Nya)
```
evmosd init [Nama_Validator] --chain-id point_10721-1
```
Noted : Tetep pake ([]] Contoh = `evmosd init [BangPateng] --chain-id point_10721-1

## Instal Genesis dan Config
```
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/config.toml
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/genesis.json
mv config.toml genesis.json ~/.evmosd/config/
```
## Running Node
```
screen -R evmos
```
```
evmosd start --json-rpc.enable=true --json-rpc.api "eth,txpool,personal,net,debug,web3"
```
CTRL A D (Agar Jalan di Background) Lalu Close Aja Buka Tab Baru

