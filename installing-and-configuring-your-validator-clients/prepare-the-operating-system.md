# Prepare the operating system

### Update your server

Now that we have provisioned the hardware and accompanying Ubuntu operating system for your server, the first thing we will do to prepare your server is to run an OS update by entering in your command line:

```bash
sudo apt update -y && sudo apt upgrade -y
```

### Configure timekeeping

We need to make sure the time on our device is the same with all other nodes so that we are able to sync with everyone else. If our timekeeping is off, we will start missing attestations (and rewards!). Verify this by running:

```bash
timedatectl
```

And check that NTP service is “active”. See screenshot below.

<figure><img src="../.gitbook/assets/Untitled (6).png" alt=""><figcaption></figcaption></figure>

If not, turn it on by running:

```bash
sudo timedatectl set-ntp on
```

### **Create a Swap Space**

A swap space _(”back-up” memory space carved out from disk space)_ is used to prevent out-of-memory errors.

Recommended swap space:

```bash
RAM     Swap Size
  8GB           3GB
 12GB           3GB
 16GB           4GB
 24GB           5GB
 32GB           6GB
 64GB           8GB
128GB          11GB
```

Create swap file:

```bash
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Make your OS remember the swap space settings even after rebooting: &#x20;

```bash
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
sudo sysctl vm.swappiness=10
sudo sysctl vm.vfs_cache_pressure=50
sudo nano /etc/sysctl.conf
```

Add the following to the end of the configuration file:

```bash
vm.swappiness=10
vm.vfs_cache_pressure=50
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

Check your new swap space with the following commands.

```bash
htop
```

_**Expected output:**_

<figure><img src="../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

Press `CTRL + C` to exit the `htop` interface and double check your swap settings by running:

```
free -h
```

_**Expected output:**_

<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>
