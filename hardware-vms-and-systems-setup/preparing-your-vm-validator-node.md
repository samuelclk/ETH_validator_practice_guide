# Preparing your VM - Validator node

## Create a new VM

Go to your google cloud console and create a new VM.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Choose the following settings:

1. **Name:** Choose your own name or leave it as the default setting
2. **Region:** Choose your preferred region but it is recommended to diversify away from the popular regions (e.g. US, EU) to minimise the risk of <mark style="color:red;">correlated downtime.</mark> I am going with Singapore in this example.
3. **Zone:** Choose any
4. **Machine configuration:** E2
5. **Machine type:** e2-standard-8, 16GB memory

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

&#x20;Under **Boot disk**, click on the **"change"** button.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Select `Ubuntu` for the operating system, `Ubuntu 22.04 LTS x86` for the version, `Balanced persistent disk` for the boot disk type, and `250 GB` for the size of the storage.

<figure><img src="../.gitbook/assets/Screenshot 2024-05-30 at 3.58.01 PM.png" alt=""><figcaption></figcaption></figure>

Check the first 2 boxes under the **"Firewall"** section.

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

Once you are done, click on the **"Create"** button at the bottom of the screen.

**Expected output:** You should see your VM instance coming online after loading for a few seconds

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

&#x20;Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Expected output:** Wait for the new window to load and your Ubuntu terminal will appear.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

You are now logged in to your validator node server and can begin [installing & configuring your validator clients](broken-reference)!
