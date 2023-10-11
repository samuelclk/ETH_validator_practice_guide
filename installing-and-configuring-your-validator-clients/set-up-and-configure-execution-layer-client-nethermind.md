# Set up and configure execution layer client (Nethermind)

### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Nethermind) and the consensus layer software (Teku) to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the Nethermind and Teku software to this JWT file later.

### Install dependencies - Unzip, Snappy & the GNU C Library

```bash
sudo apt-get update
sudo apt-get install unzip libsnappy-dev libc6-dev libc6 -y
```

### Install Nethermind and configure the service



[Download](https://downloads.nethermind.io/) the latest version of Nethermind and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://nethdev.blob.core.windows.net/builds/nethermind-1.20.1-9f39c0c7-linux-x64.zip
echo "444bf523e0db9c23243b365e717b5b15 nethermind-1.20.1-9f39c0c7-linux-x64.zip" | md5sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
nethermind-1.20.1-9f39c0c7-linux-x64.zip: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
unzip nethermind-1.20.1-9f39c0c7-linux-x64.zip -d nethermind
sudo cp -a nethermind /usr/local/bin/nethermind
rm -r nethermind-1.20.1-9f39c0c7-linux-x64.zip nethermind
```

Create an account (`nethermind`) without server access for Nethermind to run as a background service. This restricts potential attackers to only the Nethermind service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false nethermind
```

Create a directory for Nethermind to store the blockchain data of the execution layer. Then set the owner of this directory to `nethermind` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/nethermind
sudo chown -R nethermind:nethermind /var/lib/nethermind
```

Create a systemd configuration file for the Nethermind service to run in the background.

```bash
sudo nano /etc/systemd/system/nethermind.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Nethermind Execution Client (Goerli)
After=network.target
Wants=network.target

[Service]
User=nethermind
Group=nethermind
Type=simple
Restart=always
RestartSec=5
WorkingDirectory=/var/lib/nethermind
Environment="DOTNET_BUNDLE_EXTRACT_BASE_DIR=/var/lib/nethermind"
ExecStart=/usr/local/bin/nethermind/Nethermind.Runner \
  --config goerli \
  --datadir /var/lib/nethermind \
  --JsonRpc.JwtSecretFile /var/lib/jwtsecret/jwt.hex \
  --Sync.SnapSync true \
  --Sync.AncientBodiesBarrier 4367322 \
  --Sync.AncientReceiptsBarrier 4367322 \
  --Metrics.Enabled true 
  
[Install]
WantedBy=default.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Nethermind configuration summary:**

1. `--config`: Run the on the goerli testnet
2. `--datadir`: The directory for Nethermind to store the blockchain data of the execution layer
3. `--JsonRpc.JwtSecretFile`: The directory pointing to the JWT secret we generated earlier
4. `--Sync.SnapSync`: Use Nethermind's snap sync feature. More information [here](https://docs.nethermind.io/nethermind/ethereum-client/sync-modes)
5. `--Sync.AncientBodiesBarrier`: Prunes blocks before the [block](https://github.com/eth-clients/eth2-networks/blob/master/shared/mainnet/deposit\_contract\_block.txt) when the deposit contract became active.
6. `--Metrics.Enabled`: Enable monitoring metrics on the Nethermind service

### Start Nethermind

Reload the systemd daemon to register the changes made, start Nethermind, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start nethermind.service
sudo systemctl status nethermind.service
```

**Expected output:** The output should say Nethermind is **“active (running)”.** Press CTRL-C to exit and Nethermind will continue to run. It should take 48 - 72 hours for Nethermind to sync on the Mainnet.

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>sudo systemctl status nethermind.service</p></figcaption></figure>

Use the following command to check the logs of Nethermind’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu nethermind -o cat | ccze -A
```

**Expected output:**

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

If the Nethermind service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable nethermind.service
```

**Expected output:**

```
Created symlink /etc/systemd/system/default.target.wants/nethermind.service → /etc/systemd/system/nethermind.service.
```
