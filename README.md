# Big-update
------------------------------------------
sudo apt update && sudo apt upgrade -y
---------------------------------------------
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu -y
-------------------------------------------------
cd $HOME
ver="1.18.6.6.7
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gzhttps://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -

---------------------------------------------------
go version 1.5.5.7

--------------------------------------
cd $HOME
rm -rf celestia-app
git clone https://github.com/celestiaorg/celestia-app.git
cd celestia-app
git checkout v0.6.1.4.2

-------------------------------------------
git clone https://github.com/celestiaorg/networks

--------------------------
CELESTIA_NODENAME="MY_NODE" 
CELESTIA_WALLET="MY_WALLET"
CELESTIA_CHAIN="mamaki"

Ø§Ø·Ù„Ø§Ø¹Ø§Øª  Ø¯Ø§Ø®Ù„ "" Ø±Ùˆ Ú©Ù†ÛŒØ¯(ÙÙ‚Ø· Ø®Ø· Ø§ÙˆÙ„ Ùˆ Ø¯ÙˆÙ…) Ùˆ Ø§Ø·Ù„Ø§Ø¹Ø§Øª Ø®ÙˆØ¯ØªÙˆÙ† Ø±Ùˆ Ù‚Ø±Ø§Ø± Ø¨Ø¯ÛŒØ¯
Ø¨Ù‡ Ø¹Ù†ÙˆØ§Ù† Ù…Ø«Ø§Ù„ :

CELESTIA_NODENAME="amir" 
CELESTIA_WALLET="amir8372"

----------------------------------------------------------
echo 'export CELESTIA_CHAIN='$CELESTIA_CHAIN >> $HOME/.bash_profile
echo 'export CELESTIA_NODENAME='${CELESTIA_NODENAME} >> $HOME/.bash_profile
echo 'export CELESTIA_WALLET='${CELESTIA_WALLET} >> $HOME/.bash_profile
source $HOME/.bash_profile

------------------------------------------------
celestia-appd init $CELESTIA_NODENAME --chain-id $CELESTIA_CHAIN

-------------------------------------
cp $HOME/networks/mamaki/genesis.json $HOME/.celestia-app/config/

---------------------------------------
sed -i 's/mode = \"full\"/mode = \"validator\"/g'

---------------------------------------------
BOOTSTRAP_PEERS=$(curl -sL https://raw.githubusercontent.com/celestiaorg/networks/master/mamaki/bootstrap-peers.txt | tr -d '\n')

--------------------------------------------------
echo $BOOTSTRAP_PEERS

------------------------------------------------
sed -i.bak -e "s/^bootstrap-peers *=.*/bootstrap-peers = \"$BOOTSTRAP_PEERS\"/" $HOME/.celestia-app/config/config.toml

-----------------------------------------------------------
sed -i 's/timeout-commit = ".*/timeout-commit = "25s"/g' $HOME/.celestia-app/config/config.toml
sed -i 's/peer-gossip-sleep-duration *=.*/peer-gossip-sleep-duration = "2ms"/g' $HOME/.celestia-app/config/config.toml

-----------------------------------------------------------------
max_num_inbound_peers=40 
max_num_outbound_peers=10 
max_connections=50

----------------------------------------------------------------
sed -i -e "s/^use-legacy *=.*/use-legacy = false/;\
s/^max-num-inbound-peers *=.*/max-num-inbound-peers = $max_num_inbound_peers/;\
s/^max-num-outbound-peers *=.*/max-num-outbound-peers = $max_num_outbound_peers/;\
s/^max-connections *=.*/max-connections = $max_connections/" $HOME/.celestia-app/config/config.toml

------------------------------------------------
pruning_keep_recent="100" 
pruning_interval="10"

------------------------------------------------------

sed -i -e "s/^pruning *=.*/pruningÂ = \"custom\"/;\
s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/;\
s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.celestia-app/config/app.toml

--------------------------------------------

sed -i 's/snapshot-interval *=.*/snapshot-interval = 0/' $HOME/.celestia-app/config/app.toml

---------------------------------------------------
celestia-appd tendermint unsafe-reset-all --home $HOME/.celestia-app

---------------------------------------------------

celestia-appd config chain-id $CELESTIA_CHAIN
celestia-appd config keyring-backend test

-------------------------------------------------------
tee $HOME/celestia-appd.service > /dev/null <<EOF
[Unit]
  Description=celestia-appd Cosmos daemon
  After=network-online.target
[Service]
  User=$USER
  ExecStart=$(which celestia-appd) start
  Restart=on-failure
  RestartSec=3
  LimitNOFILE=65535
[Install]
  WantedBy=multi-user.target
EOF

---------------------------------------------------
sudo mv $HOME/celestia-appd.service /etc/systemd/system/

----------------------------------------------------
sudo systemctl enable celestia-appd
sudo systemctl daemon-reload
sudo systemctl restart celestia-appd && journalctl -u celestia-appd -f -o cat

------------------------------------------------------------
Press CTRL+C to stop the logs

-------------------------------------------------------------

sudo systemctl stop celestia-appd

--------------------------------------------------------------

cd $HOME
rm -rf ~/.celestia-app/data
mkdir -p ~/.celestia-app/data
SNAP_NAME=$(curl -s https://snaps.qubelabs.io/celestia/Â | \
    egrep -o ">mamaki.*tar" | tr -d ">")
wget -O - https://snaps.qubelabs.io/celestia/${SNAP_NAME}Â | tar xf - \
    -C ~/.celestia-app/data/

------------------------------------------------------------------
Ù…Ù†ØªØ¸Ø± Ø¨Ù…Ø§Ù†ÛŒØ¯ ØªØ§ Ú¯Ø±Ù‡ Ø´Ù…Ø§ Ø§Ú©Ø«Ø± Ø¨Ù„ÙˆÚ© Ù‡Ø§ Ø±Ø§ Ø§Ø² Ø²Ù†Ø¬ÛŒØ±Ù‡ Ø¨Ù„ÙˆÚ©ÛŒ Celestia Ø¯Ø§Ù†Ù„ÙˆØ¯ Ú©Ù†Ø¯

--------------------------------------------------
sudo systemctl restart celestia-appd && journalctl -u celestia-appd -f -o cat

-------------------------------------------------
CTRL+C Ø±Ø§ ÙØ´Ø§Ø± Ø¯Ù‡ÛŒØ¯ ØªØ§ Ù„Ø§Ú¯ Ù‡Ø§ Ù…ØªÙˆÙ‚Ù Ø´ÙˆÙ†Ø¯

---------------------------------
Ù‚Ø¨Ù„ Ø§Ø² Ø§Ø¯Ø§Ù…Ù‡ØŒ Ø¨Ø§ Ú©Ø¯ Ø²ÛŒØ± Ù…Ø·Ù…Ø¦Ù† Ø´ÙˆÛŒØ¯ Ú©Ù‡ Ø¨Ù‡ Ø·ÙˆØ± Ú©Ø§Ù…Ù„ Ø¨Ø§ Ø´Ø¨Ú©Ù‡ Ù‡Ù…Ú¯Ø§Ù… Ø´Ø¯Ù‡ Ø§ÛŒØ¯ 

curl -s localhost:26657/status | grep block_height
------------------------------------------------
Ø¨Ø§ÛŒØ¯ Ù…Ù†ØªØ¸Ø± Ø¨Ù…Ø§Ù†ÛŒØ¯ ØªØ§ Ú¯Ø±Ù‡ Validator Ø´Ù…Ø§ Ù‡Ù…Ù‡ Ø¨Ù„ÙˆÚ©â€ŒÙ‡Ø§ Ø±Ø§ Ø§Ø² Ø²Ù†Ø¬ÛŒØ±Ù‡ Ø¨Ù„ÙˆÚ©ÛŒ Ø³Ù„Ø³ØªÛŒØ§ Ø¯Ø§Ù†Ù„ÙˆØ¯ Ú©Ù†Ø¯

---------------------------------------------------

celestia-appd keys add $CELESTIA_WALLET

Ø§Ø·Ù„Ø§Ø¹Ø§Øª Ø¯Ø±ÛŒØ§ÙØªÛŒ Ø§ÛŒÙ† Ø¨Ø®Ø´ Ø±Ùˆ ØªÙˆÛŒ Ù†ÙˆØª Ù¾Ø¯ Ø¨Ø±Ø§ÛŒ Ø®ÙˆØ°Ø®ÛŒØ±Ù‡

-----------------------------------
CELESTIA_ADDR=$(celestia-appd keys show $CELESTIA_WALLET -a) 
echo $CELESTIA_ADDR 
echo 'export CELESTIA_ADDR='${CELESTIA_ADDR} >> $HOME/.bash_prof

------------------------------------------------
CELESTIA_VALOPER=$(celestia-appd keys show $CELESTIA_WALLET --bech val -a) 
echo $CELESTIA_VALOPER 
echo 'export CELESTIA_VALOPER='${CELESTIA_VALOPER} >> $HOME/.bash_profile 
source $HOME/.bash_profile

--------------------------------------------
Ø¨Ø§ Ù„ÛŒÙ†Ú© https://discord.gg/celestiacommunityÂ ÙˆØ§Ø±Ø¯ Ø¯ÛŒØ³Ú©ÙˆØ±Ø¯ Ù¾Ø±ÙˆÚ˜Ù‡ Ø¨Ø´ÛŒØ¯ Ùˆ Ø¨Ù‡ Ø¨Ø®Ø´ mamaki faucet  Ø¨Ø±ÛŒØ¯ Ùˆ Ø¨Ù‡ Ø´Ú©Ù„ Ø²ÛŒØ± Ø§Ø¯Ø±Ø³ Ø®ÙˆØ¯ØªÙˆÙ† Ø±Ùˆ ÙˆØ§Ø±Ø¯ Ú©Ù†ÛŒØ¯ Ùˆ Ø¯Ø±Ø®ÙˆØ§Ø³Øª ÙØ§Ø³Øª Ø´Ø¨Ú©Ù‡ Ø±Ùˆ Ø¨Ø¯ÛŒØ¯
$request celestia1a35035fu83jkeeqz00d3jmt3k5wu5x3lyvn6qp

------------------------------------- 
Ø¨Ø§ Ú©Ø¯ Ø²ÛŒØ± Ù‡Ù… Ù…ÛŒØªÙˆÙ†ÛŒØ¯ Ù…ÙˆØ¬ÙˆØ¯ÛŒ ÙˆÙ„Øª Ø®ÙˆØ¯ØªÙˆÙ† Ø±Ùˆ Ø¨Ø¨Ù†ÛŒØ¯  Ú©Ù† Ø±Ùˆ ÙˆØ§Ø±Ø¯ Ú©Ù†ÛŒØ¯ 
