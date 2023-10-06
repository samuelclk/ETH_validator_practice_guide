# Securing your node machine

### Disable remote password and root login

{% hint style="info" %}
_**Note:** Google Cloud VMs automatically do this for you but go ahead and follow through the steps anyway_
{% endhint %}

Run the following command to open up the SSH server configuration file.

```bash
sudo nano /etc/ssh/sshd_config
```

1. Uncomment `#AuthorizedKeysFile` if it is commented (by removing the `#` in front of it)
2. Change `KbdInteractiveAuthentication yes` to `KbdInteractiveAuthentication no` and uncomment (by removing the `#` in front of it)
3. Change `PasswordAuthentication yes` to `PasswordAuthentication no` and uncomment (by removing the `#` in front of it)
4. Change `#PermitRootLogin prohibit-password` to `PermitRootLogin no` , removing the `#` prefix

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Now we restart the SSH server so it registers the new settings:

```bash
sudo systemctl restart sshd
```

### Configure the firewall rules

The basic rules we will implement are as follows:

1. Deny all incoming traffic by default
2. Allow all outgoing traffic by default
3. Allow incoming traffic via port 22 for SSH access
4. Allow incoming traffic via port 30303 for Nethermind to connect with other nodes
5. Allow incoming traffic via port 9000 for Teku to connect with other nodes

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 30303
sudo ufw allow 9000
```

With these configurations set up, you will have blocked off all but 4 possible openings for potential attackers to enter from.&#x20;

* Ports 30303 and 9000 will be used by Nethermind and Teku. We will create dedicated user accounts with root access disabled for these services
* Port 22 is secured by your SSH private key so its virtually impossible to brute force the access.

#### Turn on the firewall

Now lets turn the firewall on and check our configurations before moving on.

```bash
sudo ufw enable
sudo ufw status numbered
```

You should see something similar to the screenshot below:

<figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

### **Set up brute force protection**

Even though having our SSH key access implemented means an attacker will need 25 million years to try all combinations, lets go ahead and make it even harder for them - by limiting the number of attempts per IP address to 5 tries and blocking them after.

_**Install the software:**_

```bash
sudo apt install -y fail2ban
```

_**Open the configuration file:**_

```bash
sudo nano /etc/fail2ban/jail.d/ssh.local
```

_**Add the following contents to the configuration file:**_

```bash
[sshd]
enabled = true
banaction = ufw
port = <your_chosen_SSH_port>
filter = sshd
logpath = %(sshd_log)s
maxretry = 5
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart fail2ban
```

### Enable automatic security updates

```bash
sudo apt update
sudo apt install -y unattended-upgrades update-notifier-common
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

_**Add the following contents to the configuration file:**_

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart unattended-upgrades
```
