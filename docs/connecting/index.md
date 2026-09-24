# Connecting to TP Stack

## Accept Your Invitation

You received an email from `tpstackwaldur@gmail.com` with the subject **"TP-Stack Lab Invitation"**.

1. Open the email.
2. Click the invitation link.
3. You land on the TP-Stack portal.
4. Click **"Accept invitation"**.
5. Click **"TP-Stack SSO"** to log in.

If you already have an account, log in. If you're new, click **"Register"** (see below).

!!! note
    The invitation link expires after 7 days. Check your spam/promotions folder if you don't see the email.

## Register Your Account

On the Keycloak login page, click **"Register"** (link below the login form).

Fill in the following:

| Field | What to enter |
| --- | --- |
| Username | Choose a short username (e.g. `jsmith`). This becomes your SSH login — keep it lowercase, no spaces, no special characters. |
| Email | Your real email (must match the invitation) |
| First name | Your first name |
| Last name | Your last name |
| Password | Choose a strong password |

Click **Register**. You'll be redirected to the TP-Stack portal and should see your project.

!!! important
    Remember your username and password — you'll need them for SSH access later.

## Explore the Portal

After logging in you'll see your organisation and project. You can view project members, resource allocations, and cluster status.

- **Portal:** [https://waldur-portal.tp-stack.co.uk](https://waldur-portal.tp-stack.co.uk)
- **Monitoring:** [https://grafana-monitoring.tp-stack.co.uk](https://grafana-monitoring.tp-stack.co.uk)

To log in again later, click **"TP-Stack SSO"** and enter your username and password.

## Set Up SSH Access

Your admin will confirm SSH access within a day of your registration. You need to do three things (all one-time setup).

### Install cloudflared

=== "macOS"

    ```bash
    brew install cloudflared
    ```

=== "Ubuntu/Debian"

    ```bash
    curl -L -o cloudflared.deb \
      https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
    sudo dpkg -i cloudflared.deb
    ```

=== "Windows"

    Download the `.msi` from the [cloudflared releases page](https://github.com/cloudflare/cloudflared/releases/latest).

Verify:

```bash
cloudflared --version
```

### Generate your SSH key

```bash
ssh-keygen -t ed25519 -f ~/.ssh/tpstack -C "your-email@example.com"
```

Press Enter twice (empty passphrase is fine).

This creates two files:

| File | What it is |
| --- | --- |
| `~/.ssh/tpstack` | **PRIVATE key** — never share this! |
| `~/.ssh/tpstack.pub` | **PUBLIC key** — send this to your admin |

Send your public key to the admin:

```bash
cat ~/.ssh/tpstack.pub
```

Copy the output and send it via email or chat.

### Configure SSH

Copy-paste this into your terminal:

```bash
mkdir -p ~/.ssh && cat >> ~/.ssh/config << 'EOF'

Host hpc.tp-stack.co.uk
    ProxyCommand cloudflared access ssh --hostname %h
    IdentityFile ~/.ssh/tpstack
EOF
chmod 600 ~/.ssh/config
```

Done. You'll never need to do this again.

## Connect to the Cluster

Once the admin confirms your SSH key is deployed:

```bash
ssh YOUR_USERNAME@hpc.tp-stack.co.uk
```

First time per 24 hours:

1. Your browser opens automatically.
2. Enter your email address.
3. Check your email for a 6-digit code.
4. Enter the code.
5. SSH connects.

After that, for the next 24 hours, `ssh YOUR_USERNAME@hpc.tp-stack.co.uk` just works — no browser, no codes.

You should see:

```text
[jsmith@slurm-login ~]$
```

Verify everything works:

```bash
whoami        # your username
id            # your UID and groups
pwd           # your home directory
sinfo         # cluster partitions and nodes
```

## Transfer Files

### Upload to the cluster

```bash
scp myfile.csv YOUR_USERNAME@hpc.tp-stack.co.uk:~/
scp -r my-project/ YOUR_USERNAME@hpc.tp-stack.co.uk:~/
```

### Download from the cluster

```bash
scp YOUR_USERNAME@hpc.tp-stack.co.uk:~/results/output.csv ./
scp YOUR_USERNAME@hpc.tp-stack.co.uk:~/results/*.png ./
```

### Upload to shared project space

```bash
scp dataset.csv YOUR_USERNAME@hpc.tp-stack.co.uk:/projects/YOUR_PROJECT/
```

All transfers go through the encrypted tunnel automatically.
