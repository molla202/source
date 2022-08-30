# source

```
wget -O SRC.sh https://raw.githubusercontent.com/Nodeist/Kurulumlar/main/Source/SRC && chmod +x SRC.sh && ./SRC.sh
```

### snap (tavsiye)
```
sudo systemctl stop sourced
sourced unsafe-reset-all
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.source/config/app.toml
wget -O $HOME/.source/config/addrbook.json "https://raw.githubusercontent.com/StakeTake/guidecosmos/main/source/sourcechain-testnet/addrbook.json"
cd
rm -rf ~/.source/data; \
wget -O - http://snap.stake-take.com:8000/source.tar.gz | tar xf -
mv $HOME/root/.source/data $HOME/.source
rm -rf $HOME/root
sudo systemctl restart sourced && journalctl -u sourced -f -o cat
```


### Start with state sync
```
sudo systemctl stop sourced
sourced unsafe-reset-all --home $HOME/.source
SEEDS=""; \
PEERS="9d16b552697cdce3c8b4f23de53708533d99bc59@165.232.144.133:26656,d565dd0cb92fa4b830662eb8babe1dcdc340c321@44.234.26.62:26656,2dbc3e6d52e5eb9357aec5cf493718f6078ffaad@144.76.224.246:36656"; \
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.source/config/config.toml
wget -O $HOME/.source/config/addrbook.json "https://raw.githubusercontent.com/StakeTake/guidecosmos/main/source/sourcechain-testnet/addrbook.json"
SNAP_RPC="https://testnet.sourceprotocol.io:26657"
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.source/config/config.toml
sudo systemctl restart sourced && journalctl -u sourced -f -o cat
```



Cüzdan Oluşturma
Yeni cüzdan oluşturmak için aşağıdaki komutu kullanabilirsiniz. Hatırlatıcıyı (mnemonic) kaydetmeyi unutmayın.
```
sourced keys add $SRC_WALLET
```
(OPSIYONEL) Cüzdanınızı hatırlatıcı (mnemonic) kullanarak kurtarmak için:
```
sourced keys add $SRC_WALLET --recover
```


```
sourced tx staking create-validator \
  --amount 999750usource \
  --from cüzdanadı \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(sourced tendermint show-validator) \
  --moniker nodeadı \
  --chain-id sourcechain-testnet \
  --fees 250usource
  ```
  
  
  Kullanışlı Komutlar
Servis Yönetimi
Logları Kontrol Et:
```
journalctl -fu sourced -o cat
```
Servisi Başlat:
```
systemctl start sourced
```
Servisi Durdur:
```
systemctl stop sourced
```
Servisi Yeniden Başlat:
```
systemctl restart sourced
```








