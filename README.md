# <h1 align="center"> Quicksilver Mainnet Node Turkish </h1> 
![A0_Website_OG_Generic](https://miro.medium.com/max/1200/1*iJTk82YFiyxv_qDWPzURSw.png)

## Linkler:
 * [Quicksilver Resmi Twitter](https://twitter.com/quicksilverzone)
 * [Quicksilver Resmi Telegram](https://t.me/quicksilverzone)
 * [Quicksilver Resmi Discord](https://discord.gg/TTvGzjFTPD)
 * [Quicksilver Explorer](https://quicksilver.explorers.guru/)
 
## Minimum sistem gereksinimleri

* 4 cores (max. clock speed possible)

* 16GB RAM

* 500GB+ of NVMe or SSD disk

## Başlangıç
```
sudo su

cd

sudo apt update && sudo apt upgrade -y

sudo apt-get install make build-essential gcc git jq chrony screen -y

screen -S quicksilver
```

## Go'yu yükleyin
```
sudo rm -rvf /usr/local/go/

wget https://golang.org/dl/go1.19.3.linux-amd64.tar.gz

sudo tar -C /usr/local -xzf go1.19.3.linux-amd64.tar.gz

rm go1.19.3.linux-amd64.tar.gz

export GOROOT=/usr/local/go

export GOPATH=$HOME/go

export GO111MODULE=on

export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
```

#### * Go versiyonunu kontrol edin. (Çıktı:`go version go1.19.3 linux/amd64`)
```
go version
```

## Quicksilver'ı yükleyin
```
git clone https://github.com/ingenuity-build/quicksilver && cd quicksilver

git fetch origin --tags

git checkout v1.0.0

make install
```

#### * Quicksilver versiyonunu kontrol edin. (Çıktı:`v1.0.0`)
```
quicksilverd version
```

## Düğümü başlatmak için gerekli adımlar

#### * <> işaretlerini kaldırın.
```
quicksilverd init <NODEADI> --chain-id quicksilver-1
```

#### * Yeni cüzdan oluşturun veya eski cüzdanınızı kurtarın.
```
quicksilverd keys add <CUZDANADI>

quicksilverd keys add <CUZDANADI> --recover
```

#### * Genesis dosyasını yükleyin ve ikinci komut ile çıktıyı kontrol edin. (Çıktı:`8bfc3aa7a81eb8c1a2452bdb8d256b372ecfdd67c634b4f63846f755ef4dd815`)
```
wget -O ~/.quicksilverd/config/genesis.json https://raw.githubusercontent.com/ingenuity-build/mainnet/main/genesis.json

jq . -S -c ~/.quicksilverd/config/genesis.json | shasum -a 256
```

#### * Minimum gas değerini ayarlayın.
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0001uqck\"/;" ~/.quicksilverd/config/app.toml
```
#### * Seed node'ları tanımlayın.
```
SEEDS="20e1000e88125698264454a884812746c2eb4807@seeds.lavenderfive.com:11156,babc3f3f7804933265ec9c40ad94f4da8e9e0017@seed.rhinostake.com:11156,00f51227c4d5d977ad7174f1c0cea89082016ba2@seed-quick-mainnet.moonshot.army:26650,b4ce26d04be70fc2f01fb34c912db224a66029f0@34.132.212.0:26656"

sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" ~/.quicksilverd/config/config.toml
```

#### * Servis dosyasını oluşturalım.
```
sudo tee  /etc/systemd/system/quicksilverd.service /dev/null <<EOF

[Unit]
Description=Quicksilver Service
After=network.target

[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME
ExecStart=$HOME/go/bin/quicksilverd start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### * Düğümü başlatın.
```
sudo systemctl daemon-reload && systemctl enable quicksilverd
sudo systemctl restart quicksilverd && journalctl -o cat -fu quicksilverd
```

#### * Senkronize durumunu kontrol edin. `"catching_up": false` çıktısını alana kadar diğer adıma geçmeyin.
```
quicksilverd status 2>&1 | jq .SyncInfo
```

#### * Doğrulayıcıyı oluşturun. (Doğruylayıcı oluşturmadan önce cüzdanınızda QCK bulunmalı)
```
quicksilverd tx staking create-validator \
--amount 50000000uqck \
--commission-max-change-rate "0.1" \
--commission-max-rate "0.20" \
--commission-rate "0.1" \
--min-self-delegation "1" \
--website="https://github.com/mulosbron" \
--pubkey=$(quicksilverd tendermint show-validator) \
--moniker <NODEADI> \
--chain-id quicksilver-1 \
--from <CUZDANADI> \
-y
```
#### * Son olarak yukardaki komutun verdiği hash'i explorer'da aratın. Sonuç `success` ise olmuştur.
![quicksilver](https://user-images.githubusercontent.com/91866065/208239193-54d83ef4-5135-4f69-99eb-c9e945dc1752.png)

![quicksilver2](https://user-images.githubusercontent.com/91866065/208239258-133168ab-fa12-43a0-aa62-0eaeb8b0a3e7.png)
