# Mina_Testworld2_guide


Setup for Ubuntu 20

### Mina
```
sudo rm /etc/apt/sources.list.d/mina*.list
echo "deb [trusted=yes] http://packages.o1test.net/ focal rampup" | sudo tee /etc/apt/sources.list.d/mina-rampup.list
sudo apt-get update
```
```
sudo apt-get install -y mina-berkeley=2.0.0rampup5-55b7818
```


- Download the zip file from the e-mail to your PC and open it hebelehübele.zip
- We create a keys file on the server ~/keys .
```
mkdir ~/keys
```
- edit the authority of the file
```
chmod 700 ~/keys
```
* paste your community-151-key to /keys/my-wallet
```
nano ~/keys/my-wallet
```
* Press Ctrl+X to save the file
* Press Y then ENTER to exit
```
chmod 600 ~/keys/my-wallet
```
*	Paste the community-151-key.pub into /keys/my-wallet.pub
```
nano ~/keys/my-wallet.pub
```
## Generating libp2p keypair

* Generate keypair from libp2p. Be sure that this keypair is saved not in the same file as your keypair obtained from email
You will be asked to put a password. You have to paste password from community-151-password from email.

```
mina libp2p generate-keypair -privkey-path /root/keys/keys
```
---------------
## Configure your node
* Open .mina-env file
```
nano ~/.mina-env
```
* Paste the following config into .mina-env
*  in --external-ip your have to put ip of your server and in `MINA_PRIVKEY_PASS`, `UPTIME_PRIVKEY_PASS` and `MINA_LIBP2P_PASS` it is needed to paste your community-151-password
```
MINA_PRIVKEY_PASS="community-151-password"
UPTIME_PRIVKEY_PASS="community-151-password"
EXTRA_FLAGS="--log-json --log-snark-work-gossip true --internal-tracing --insecure-rest-server --config-directory /root/.mina-config/ --external-ip  `ip of your server`   --itn-keys  f1F38+W3zLcc45fGZcAf9gsZ7o9Rh3ckqZQw6yOJiS4=,6GmWmMYv5oPwQd2xr6YArmU1YXYCAxQAxKH7aYnBdrk=,ZJDkF9EZlhcAU1jyvP3m9GbkhfYa0yPV+UdAqSamr1Q=,NW2Vis7S5G1B9g2l9cKh3shy9qkI1lvhid38763vZDU=,Cg/8l+JleVH8yNwXkoLawbfLHD93Do4KbttyBS7m9hQ= --itn-graphql-port 3089 --uptime-submitter-key  /root/keys/my-wallet --uptime-url https://block-producers-uptime-itn.minaprotocol.tools/v1/submit --metrics-port 10001 --enable-peer-exchange  true --libp2p-keypair /root/keys/keys --log-precomputed-blocks true --max-connections 200  --generate-genesis-proof  true --block-producer-key /root/keys/my-wallet --node-status-url https://nodestats-itn.minaprotocol.tools/submit/stats  --node-error-url https://nodestats-itn.minaprotocol.tools/submit/stats  --file-log-rotations 500"
RAYON_NUM_THREADS=6
MINA_LIBP2P_PASS="community-151-password"
```
* Give access to /.mina-env
```
chmod 600 ~/.mina-env
```

## Change peer list
* Go to `/usr/lib/systemd/user/mina.service` and open in any text editor
* Cahnge the peer_list_url `from https://storage.googleapis.com/seed-lists/berkeley_seeds.txt` to `https://storage.googleapis.com/seed-lists/testworld-2-0_seeds.txt`
```
[Unit]
Description=Mina Daemon Service
After=network.target
StartLimitIntervalSec=60
StartLimitBurst=3

[Service]
Environment="PEERS_LIST_URL=https://storage.googleapis.com/seed-lists/berkeley_seeds.txt"
Environment="LOG_LEVEL=Info"
Environment="FILE_LOG_LEVEL=Debug"
EnvironmentFile=%h/.mina-env
Type=simple
Restart=always
RestartSec=30
ExecStart=/usr/local/bin/mina daemon \
  $EXTRA_FLAGS
ExecStop=/usr/local/bin/mina client stop-daemon

[Install]
WantedBy=default.target
```

## Launch Mina node
```
systemctl --user daemon-reload
systemctl --user restart mina
systemctl --user enable mina
sudo loginctl enable-linger
```
* If you did all correctly you will see running logs
* Run your logs
```
journalctl --user-unit mina -n 1000 -f
```

* After 5 min check node status and verify the GIT SHA-1 and chain ID:
* Git SHA-1: 55b7818
* Chain ID: 332c8cc05ba8de9efc23a011f57015d8c9ec96fac81d5d3f7a06969faf4bce92

# Useful commands
* Check node status
```
mina client status
```
* Stop your node
```
systemctl --user stop mina
```
* Restart your node
```
systemctl --user restart mina
```
* Check logs
```
journalctl --user-unit mina -n 1000 -f
```
  
  
