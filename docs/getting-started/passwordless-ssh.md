# Passwordless SSH

Typing your Pitt password on every login gets old fast. With SSH keys you
authenticate with a cryptographic key pair instead, so connections happen
without a password prompt. This page covers two related setups:

1. **From your computer to a login node** — skip the password when you `ssh` in.
2. **Between cluster nodes** — passwordless hops from a login node to your
   compute nodes, which is required for multi-node MPI jobs and for SSHing into
   a node running your job.

!!! note "You still need the VPN"
    SSH keys replace the *password*, not the network requirement. The clusters
    are firewalled within PittNet, so you must still be connected to the
    [GlobalProtect VPN](getting-started-step1-account.md) before any of this works.

!!! warning "Protect your private key"
    Your **private** key (e.g. `id_ed25519`) is the secret half of the pair.
    Never share it, email it, or copy it onto shared storage. Only the
    **public** key (`id_ed25519.pub`) is ever installed on a server.

---

## Part 1 — From your computer to the login node

### Step 1: Generate a key pair (on your own computer)

Run this in a terminal on your **local machine**, not on the cluster:

=== "macOS / Linux"

    ```bash
    ssh-keygen -t ed25519 -C "your_pittID@pitt.edu"
    ```

=== "Windows (PowerShell / Terminal)"

    ```powershell
    ssh-keygen -t ed25519 -C "your_pittID@pitt.edu"
    ```

Accept the default file location. When prompted for a passphrase, **setting one
is recommended** — combined with an agent (Step 4) you still only type it once
per session, and it protects the key if your laptop is lost. This creates two
files in `~/.ssh/`:

| File | What it is | Goes where |
| ---- | ---------- | ---------- |
| `id_ed25519` | Private key (secret) | Stays on your computer |
| `id_ed25519.pub` | Public key (shareable) | Installed on the login node |

### Step 2: Install your public key on the login node

This is the one time you'll enter your Pitt password.

=== "macOS / Linux"

    ```bash
    ssh-copy-id <pittID>@h2p.crc.pitt.edu
    ```

=== "Windows (PowerShell)"

    `ssh-copy-id` is not included on Windows, so append the key manually:

    ```powershell
    type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh <pittID>@h2p.crc.pitt.edu "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
    ```

Use `htc.crc.pitt.edu` instead if you work on the HTC cluster. Because your home
directory is shared across the cluster, installing the key once covers every
login node.

### Step 3: Test it

```bash
ssh <pittID>@h2p.crc.pitt.edu
```

You should connect without a Pitt password prompt. (If you set a key passphrase
and haven't loaded an agent yet, you'll be asked for the *passphrase* — that's
expected; Step 4 removes even that.)

### Step 4 (optional): Type your passphrase once per session

An SSH agent holds your unlocked key in memory so you enter the passphrase only
once after logging into your computer:

=== "macOS / Linux"

    ```bash
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519
    ```

    On macOS, add the key to the keychain so it loads automatically:

    ```bash
    ssh-add --apple-use-keychain ~/.ssh/id_ed25519
    ```

=== "Windows (PowerShell)"

    ```powershell
    Start-Service ssh-agent
    ssh-add $env:USERPROFILE\.ssh\id_ed25519
    ```

### Step 5 (optional): Add a shortcut in your SSH config

Put this in `~/.ssh/config` on your computer so `ssh h2p` is all you need. The
`ControlMaster` lines also reuse a single connection, making repeat logins
instant:

```text
Host h2p
    HostName h2p.crc.pitt.edu
    User <pittID>
    ForwardX11 yes
    ControlMaster auto
    ControlPath ~/.ssh/cm-%r@%h:%p
    ControlPersist 10m
```

Now you can simply run:

```bash
ssh h2p
```

!!! tip "PuTTY and MobaXterm users"
    PuTTY uses its own key format: generate keys with **PuTTYgen**, load the
    private key with **Pageant** (its agent), and paste the public key into
    `~/.ssh/authorized_keys` on the login node. MobaXterm can use standard
    OpenSSH keys directly, or import a PuTTY `.ppk`.

---

## Part 2 — Between cluster nodes (login → compute)

Some workflows need one node on the cluster to SSH into another without a
password — most commonly multi-node **MPI** jobs, and manually SSHing into a
compute node that is already running your job. Because your home directory is
shared across all nodes, a single key pair generated **on the login node**
enables this everywhere.

Run these commands **on a login node**:

```bash
# 1. Generate a key pair in your cluster home directory
ssh-keygen -t ed25519

# 2. Authorize that key for logins within the cluster
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys

# 3. Fix permissions (SSH refuses keys that are too open)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Test by SSHing into a compute node you've been allocated (for example, from
inside a running job or an interactive session):

```bash
ssh <compute-node-name>
```

The first hop to each new node asks you to confirm its host key — type `yes`
once and it's remembered.

!!! note "This is separate from Part 1"
    Part 1 keys live on your laptop and let you reach the login node. Part 2 keys
    live in your cluster home directory and let cluster nodes reach each other.
    You can set up either independently.

---

## Troubleshooting

Still being asked for a password?

- Confirm you're connected to the **VPN**.
- Check permissions on the server: `~/.ssh` must be `700`, `~/.ssh/authorized_keys`
  must be `600`. `ssh-copy-id` sets these for you; the manual/Windows path sets
  them explicitly.
- Make sure you used the matching key. Add `-v` to see which key SSH offers:
  `ssh -v <pittID>@h2p.crc.pitt.edu`.

**"Permission denied (publickey)"** usually means the public key isn't installed
for that account, or you used the wrong username — recheck Step 2.

**"Host key verification failed"** after a maintenance window is expected when a
node is rebuilt. Clear the stale entry and reconnect:

```bash
ssh-keygen -R <hostname>
```

See the [FAQ](../faq.md) for a fuller explanation of host-key changes.
