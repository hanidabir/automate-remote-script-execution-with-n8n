## Overview

1. Generate SSH key on the n8n server  
2. Add the public key to the target server  
3. Test SSH connection from n8n  
4. Create SSH Credential in n8n  
5. Import and configure the workflow  
6. Test and activate automation  
7. Troubleshooting and best practices  

---

## Requirements

Before starting, you need:

- An **n8n server** (self-hosted or cloud)
- SSH access to your remote server (preferably with key authentication)
- The script or command you want to execute
- The workflow file: [`Remote Script Runner.json`](../Remote%20Script%20Runner.json)

---

## 1 — Generate SSH Key on the n8n Server

On the machine running n8n, generate an SSH key pair:

```bash
# Recommended: ed25519 if supported
ssh-keygen -t ed25519 -C "n8n-ssh-key" -f ~/.ssh/n8n_ssh_key
# Leave passphrase empty by pressing Enter twice
````

This creates two files:

* `~/.ssh/n8n_ssh_key` → private key
* `~/.ssh/n8n_ssh_key.pub` → public key

---

## 2 — Add Public Key to the Target Server

On the **target server** (where the script will run):

```bash
mkdir -p /root/.ssh
chmod 700 /root/.ssh
# Copy the public key from n8n (.pub file)
echo "PASTE_PUBLIC_KEY_CONTENT_HERE" >> /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys
```

Then test SSH access from n8n:

```bash
ssh -i ~/.ssh/n8n_ssh_key root@DESTINATION_SERVER_IP 'echo ok'
```

If everything is configured properly, it should output `ok` without asking for a password.

---

## 3 — Create SSH Credential in n8n

1. Go to **n8n → Credentials → New Credential**

2. Search for **SSH** and select **SSH (Private Key)**

3. Fill in the fields as follows:

   * **Name:** `remote-server-ssh`
   * **Host:** IP or domain of the target server (e.g., `example.com`)
   * **Username:** `root` (or another SSH user)
   * **Port:** `22`
   * **Private Key:** Paste the content of `~/.ssh/n8n_ssh_key`

4. Click **Save** and test the connection — you should see *Connected successfully* ✅

---

## 4 — Import the Workflow into n8n

1. In the n8n dashboard, click **Import Workflow**
2. Choose the file [`Remote Script Runner.json`](../Remote%20Script%20Runner.json)
3. The imported workflow includes three main nodes:

| Node                   | Description                                           |
| ---------------------- | ----------------------------------------------------- |
| 🕒 **Scheduler**       | Defines when the workflow should run                  |
| 💻 **Executer (SSH)**  | Runs the command or script on the remote server       |
| 🧮 **Exporter (Code)** | Parses and structures the output (JSON or plain text) |

---

## 5 — Configure the Nodes

### Scheduler Node

Set the automatic execution time.
Example (monthly run):

* **Mode:** Every Month
* **Day of Month:** `1`
* **Hour:** `3`
* **Minute:** `0`

(You can also set it to run daily, hourly, etc.)

---

### Executer (SSH) Node

* Set **Credential** to `remote-server-ssh`
* In **Command**, enter the command or script path, for example:

```bash
bash /root/my_script.sh
```

or any other command, e.g.:

```bash
docker system prune -af
```

---

### Exporter (Code) Node

If your script outputs JSON (like `{"status":"ok"}`),
this node will parse it into structured data for downstream use.
Otherwise, it simply passes through the plain output text.

---

## 6 — Test and Activate

1. Click **Execute Workflow** to test.
2. Check the **Executer (SSH)** node output — it should display your script’s output.
3. If everything looks good, toggle **Active = ON** at the top.

From now on, **n8n will automatically execute your script** on schedule 🚀

---

## Troubleshooting

* If you see `Permission denied` in the SSH node, check `.ssh` directory permissions.
* If the **Code** node shows no output, ensure your script prints valid JSON.
* To manually test connectivity from within the n8n host:

  ```bash
  ssh -i /home/node/.ssh/n8n_ssh_key root@domain.com 'echo test'
  ```
