# Validator key generation

## Important

* In actual practice, it is highly recommended that you perform this step using an air-gapped machine - i.e. a device that has never connected to the public internet before. We will describe a few methods below.
* If this is not available, turn off all internet and wireless connection (e.g. Ethernet, WiFi, Bluetooth) before proceeding with the key generation step
* In both cases above, make sure you are in a safe environment (e.g. home or office) with a trusted WiFi network for building the validator key generation tool from source. Make sure to also physically block all camera devices - e.g. laptop cameras, Webcams, people standing behind you during this process

### Download the Staking Deposit CLI tool

We are going to simulate this air-gapped machine by using your server and running the following commands on it for this practice session.

This is the tool that generates your validator private key. You can refer to the downloadable files here - [https://github.com/ethereum/staking-deposit-cli/releases](https://github.com/ethereum/staking-deposit-cli/releases)

For simplicity, just paste the following code into the terminal of your **Google Cloud **_**VM**_**.**

_**Server machine:**_

```bash
curl -LO <https://github.com/ethereum/staking-deposit-cli/releases/download/v2.5.0/staking_deposit-cli-d7b5304-linux-amd64.tar.gz>
tar xvf staking_deposit-cli-d7b5304-linux-amd64.tar.gz
cd staking_deposit-cli-d7b5304-linux-amd64
ls
```

There should be an executable file named `deposit` shown.

### Run the Staking Deposit CLI tool

Run the following command and replace `<YourWithdrawalAddress>` with `0x4D496CcC28058B1D74B7a19541663E21154f9c84`

_**Note:** In actual practice, you will use your own Metamask Ethereum address. We are using a special address here so that we can get free Goerli ETH for our setup._

_**Server machine:**_

```bash
./deposit new-mnemonic --num_validators 1 --chain goerli --eth1_withdrawal_address 0x4D496CcC28058B1D74B7a19541663E21154f9c84
```

Follow the instructions shown on your terminal.

![Untitled](<../.gitbook/assets/Untitled (8).png>)

After this, a seed phrase will be generated. Needless to say, if you lose this you will lose all your staked ETH. You will write this down on nice **physical notebook** using a **ballpoint (not ink) pen** and guard this with your life in actual deployment. But for today you can simply paste it into your notebook app of your laptop.

![Untitled](<../.gitbook/assets/Untitled (9).png>)

![Untitled](<../.gitbook/assets/Untitled (10).png>)

There will be 2 files that will be generated once you are done:

1. The `deposit_data-[timestamp].json` file contains the public key for the validator and other information to link your staked ETH to your validator. This file will be used to complete the Goerli test network ETH staking deposit process at a later step. This file needs to go to your laptop (see below for steps).
2. The `keystore-[..].json` files contain the encrypted validator signing key. The password you entered earlier is used to decrypt this key. This key is used by your validator to sign transactions - eg. attest & propose blocks. This file will stay in your server machine.

### Create the password file for your validator signing keystore

**Server machine:**

Get the file name of your validator signing keystore by running the `ls` command in your `validator_keys` directory:

```sh
cd
cd staking_deposit-cli-d7b5304-linux-amd64/validator_keys
ls
```

With the `validator_signing_keystore_file_name` copied, create the password file.

<pre><code>sudo nano <a data-footnote-ref href="#user-content-fn-1">&#x3C;validator_signing_keystore_file_name></a>.txt
</code></pre>

Type in the password you used when generating your validator keys in the earlier step. Then save and exit the file with `CTRL + O, enter, CTRL + X`.

### Replicate the `deposit_data-[timestamp].json` onto your physical laptop

_**Server machine:**_

```bash
cd
cd staking_deposit-cli-d7b5304-linux-amd64/validator_keys
ls
```

Copy the file name of your `deposit_data-[timestamp].json` file and paste it in your notepad file on your laptop

_**Server machine:**_

```bash
sudo cat deposit_data-<timestamp>.json
```

1. Copy the output and open another notepad file on your laptop. Paste it here and save the file as a JSON format
2. Rename the file into the same file name as the `deposit_data-[timestamp].json` on your server (recall that you pasted this in your working notepad file earlier)

[^1]: 
