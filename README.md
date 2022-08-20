<p style="font-size:14px" align="right">
<a href="https://t.me/bangpateng_group" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
<a href="https://bangpateng.com/" target="_blank">Visit our website <img src="https://user-images.githubusercontent.com/38981255/184068977-2d456b1a-9b50-4b75-a0a7-4909a7c78991.png" width="30"/></a>
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/38981255/185550018-bf5220fa-7858-4353-905c-9bbd5b256c30.jpg">
</p>

## Specification VPS

CPU : 4 or more physical CPU cores
RAM : 32 GB
Storage : 500GB SSD
Connection : 100 Mbps
OS : Ubuntu 18.04 +

# #Point Network Testnet Incentivized

## Setting up vars
```
NODENAME=Bang_Pateng
```
Bang_Pateng Ganti Dengan nama Validator kalian

```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export EVMOS_CHAIN_ID=point_10721-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages

```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies

```
sudo apt install curl build-essential git wget jq make gcc tmux -y
```

## Install Go

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

## Download and build binaries

```
cd $HOME
git clone https://github.com/pointnetwork/point-chain && cd point-chain
git checkout xnet-triton
make install
```

## Install Node

```
evmosd config chain-id $EVMOS_CHAIN_ID
evmosd config keyring-backend file
```

## Init App

```
evmosd init $NODENAME --chain-id $EVMOS_CHAIN_ID
```

## Download genesis and addrbook

```
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/config.toml
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/genesis.json
mv config.toml genesis.json ~/.evmosd/config/
```

### Validasi:

```
evmosd validate-genesis
```

## Create Service

```
sudo tee /etc/systemd/system/evmosd.service > /dev/null <<EOF
[Unit]
Description=evmos
After=network-online.target

[Service]
User=$USER
ExecStart=$(which evmosd) start --home $HOME/.evmosd
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service

```
sudo systemctl daemon-reload
sudo systemctl enable evmosd
sudo systemctl restart evmosd && sudo journalctl -u evmosd -f -o cat
```

## Buat dompet

Untuk membuat dompet baru Anda dapat menggunakan perintah di bawah ini Masukan Pharse Metamask Kalian dan Jangan lupa simpan mnemonicnya Validator

```
evmosd keys add $WALLET
```

(OPSIONAL) Untuk memulihkan dompet Anda menggunakan frase seed

```
evmosd keys add $WALLET --recover
```

Untuk mendapatkan daftar dompet saat ini

```
evmosd keys list
```

## Save Info Wallet

```
EVMOS_WALLET_ADDRESS=$(evmosd keys show $WALLET -a)
```
Masukan Pharse Wallet
```
EVMOS_VALOPER_ADDRESS=$(evmosd keys show $WALLET --bech val -a)
```
Masukan Pharse Wallet
```
echo 'export EVMOS_WALLET_ADDRESS='${EVMOS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EVMOS_VALOPER_ADDRESS='${EVMOS_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Minta Faucet Menggunakan Address Metamask (Kalo Udah Sekip)

- Isi Form : https://pointnetwork.io/testnet-form (Tunggu 24 Jam Akan Dapat Email dan Coin Test Masuk ke Metamask)
- Add RPC di Metamask (Untuk Memastikan Udah Ada Faucet Landing)

```
Network Title: Point XNet Triton
RPC URL: https://xnet-triton-1.point.space/
Chain ID: 10721
SYMBOL: XPOINT
```

## Cara Convert Token

Note : Jika Saldo faucet Yang kalian Punya Berbeda Address atau Ada di Wallet Metamask Yang Berbeda.
Intinya, Untuk Kalian Yang Claim Token Faucet Pakai Wallet Pertama Metamask dan Wallet Validator itu Berbeda..

## Export Private Key Validator kalian Dengan Perintah

```
evmosd keys unsafe-export-eth-key $WALLET --keyring-backend file
```

- Masukan Pharse atau Password Keyring (Sesuai Yang kalian Bikin Saat Buat Wallet)
- Salin Private Key nya
- Import ke Metamask
- Pindahkan dan Kirim Token XPOINT Yang ada di Address Pertama ke Address `0x..` Yang Baru kalian Import Tadi

## Convert XPOINT

- Buka Situs : https://evmos.me/utils/tools
- Connect Wallet
- Masukan Address `0x..` Metamaskan kalian di Addres Conventer anda Akan Melihat Address `Evmosxxxx` dan Pastikan Sama Dengan Address Wallet Validator Kalian
- Silahkan Check Ke Vps kalian dengan Perintah `evmosd query bank balances address-evmos-kalian`
- Maka Tara Saldo Anda Sudah Ada

## Buat Validator (Pastikan Status False Dan Saldo Udah Ada)

### Check Status (Jika Sudah False dan Token Sudah Landing baru Buat Validator)

```
evmosd status 2>&1 | jq .SyncInfo
```

### Check Saldo 

```
evmosd query bank balances $EVMOS_WALLET_ADDRESS
```
Jika Command di atas Error `$EVMOS_WALLET_ADDRESS` menjadi `Address Kalian`

## Create Validator Nya

```
evmosd tx staking create-validator \
  --amount 10000000000000000000000apoint \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "10000000000000000000000" \
  --pubkey  $(evmosd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $EVMOS_CHAIN_ID
```

## Edit Validator

```
evmosd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$EVMOS_CHAIN_ID \
  --from=$WALLET
  ```

## Delete Node (Permanent)

```
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmos* -rf
sudo rm $(which evmosd) -rf
sudo rm $HOME/.evmosd -rf
sudo rm $HOME/point-chain -rf
```
