<h1 align="center"> dYmension </h1>

> Validatör Kurulumu..

> Eğer dYmensionda rolünüz yoksa katılamazsınız rollapps fam rolü.

> Validatörlere ayrılan token sayısı %1 supply'ın %40'ıdır.

> Platforma ayrılan %1 supply'ın %10'ıdır. [Buradan kullanabilirsin](https://portal.dymension.xyz/ibc)

> Bence [roller](https://github.com/ruesandora/dYmension-rollApp/blob/main/roller.md) kurarak başlayın.

> Community Kanalları: [Duyuru](https://t.me/RuesAnnouncement) - [Chat](https://t.me/RuesChat) - [Resmi dYmension kanalı](https://t.me/dYmensionTurkish) 

<h1 align="center"> Donanım ve Gereksinimler </h1>

> Sunucu olarak [Hetzner Kullanıyorum](https://github.com/ruesandora/Hetzner/blob/main/README.md)

```sh
# Benim kullandığım: 
4 CPU - 8 RAM - 150 SSD

# Dökümasyon önerisi:
4 CPU - 16 RAM - 500 SSD
```

<h1 align="center"> Kurulum </h1>

```console
# Güncellemeler:
sudo apt-get update && sudo apt-get upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

```console
# Go yüklüyoruz:
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.20.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile
go version
```

<h1 align="center"> Dymensionu yüklüyoruz </h1>

```console
sudo apt install git
git clone https://github.com/dymensionxyz/dymension.git
cd dymension
git checkout v1.0.2-beta

make install

dymd version
```
```console
# Node isminizi girin tırnakları kaldırın
dymd init <Nodeİsmi> --chain-id=froopyland_100-1
```

<h1 align="center"> Gerekli ayarlamalar </h1>

```console
# seedler
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0udym\"/;" ~/.dymension/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.dymension/config/config.toml
peers="e7857b8ed09bd0101af72e30425555efa8f4a242@148.251.177.108:20556,3410e9bc9c429d6f35e868840f6b7a0ccb29020b@46.4.5.45:20556"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.dymension/config/config.toml
seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:20556"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.dymension/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.dymension/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.dymension/config/config.toml

# pruning ve indexer
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.dymension/config/app.toml
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.dymension/config/config.toml
```

<h1 align="center"> Servis dosyası oluşturma </h1>

```console
# Servis dosyası
sudo tee /etc/systemd/system/dymd.service > /dev/null <<EOF
[Unit]
Description=dymd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which dymd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable dymd
sudo systemctl restart dymd
```

<h1 align="center"> Node'u başlatma </h1>

```console
# Hızlı eşleşmek için:
cd $HOME
apt install lz4
sudo systemctl stop dymd
cp $HOME/.dymension/data/priv_validator_state.json $HOME/.dymension/priv_validator_state.json.backup
rm -rf $HOME/.dymension/data
curl -o - -L http://dymension.snapshot.stavr.tech:1019/dymension/dymension-snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.dymension --strip-components 2
mv $HOME/.dymension/priv_validator_state.json.backup $HOME/.dymension/data/priv_validator_state.json
wget -O $HOME/.dymension/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Dymension/addrbook.json"
sudo systemctl restart dymd && journalctl -u dymd -f -o cat
```

<h1 align="center"> Cüzdan oluşturma </h1>

```console
dymd keys add <cüzdanİsmi>
```

> Faucetten token alın.

> SYNC olunca devam edebilirsiniz.

<h1 align="center"> Validatör oluşturma </h1>

> Kendinize göre ayarlayın:

```console
dymd tx staking create-validator \
--amount 1000000udym \
--pubkey $(dymd tendermint show-validator) \
--moniker "Validatörİsmi" \
--details "" \
--chain-id froopyland_100-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from rues \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025udym \
-y
```

> Explorer: [Burada](https://explorer.stavr.tech/dymension-testnet/staking/dymvaloper1ysp32qrrw3fzmqsneekqzmkux2rctmxxwvxq05)
