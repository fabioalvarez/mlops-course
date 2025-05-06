# Guide to Setting Up and Troubleshooting SSH Keys for Git (GitHub/Bitbucket)
**Last Updated:** May 5, 2025

Secure Shell (SSH) provides a secure way to interact with Git repositories without needing to enter your username and password each time. This guide covers setting up SSH keys for the first time and troubleshooting common issues, including managing multiple accounts.

## Part 1: First-Time SSH Key Setup

Follow these steps if you haven't set up SSH keys for your Git provider before.

### Step 1: Check for Existing SSH Keys

First, check if you already have SSH keys on your system. Common key filenames include `id_rsa`, `id_ed25519`, `id_dsa`.

```bash
# List files in your .ssh directory
ls -al ~/.ssh
```

If you see existing `.pub` files (like `id_ed25519.pub` or `id_rsa.pub`), you might already have a key pair you can use. If you want to use an existing key, skip to Step 4. If not, or if the directory doesn't exist, proceed to Step 2.

### Step 2: Generate a New SSH Key Pair

It's recommended to use the Ed25519 algorithm, which is secure and performant.

```bash
# Replace "your_email@example.com" with the email associated with your Git account
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- **File location:** When prompted to *Enter file in which to save the key*, press Enter to accept the default location (e.g., `~/.ssh/id_ed25519`).
- **Passphrase:** You will be asked to *Enter passphrase*. Using a passphrase adds an extra layer of security. If you set one, you'll need to enter it when using the key (though the SSH agent can manage this). You can also leave it empty for no passphrase.

This command creates two files in your `~/.ssh` directory:
- `id_ed25519` (Your private key — **Keep this safe and secret!**)
- `id_ed25519.pub` (Your public key — This is what you upload to GitHub/Bitbucket)

### Step 3: Add Your SSH Key to the SSH Agent (Optional, Recommended)

The SSH agent manages your keys and passphrases, so you don't have to enter your passphrase repeatedly.

```bash
# Start the ssh-agent in the background
eval "$(ssh-agent -s)"

# Add your SSH private key to the agent
# If you used a different filename, replace id_ed25519
ssh-add ~/.ssh/id_ed25519
```

If you set a passphrase in Step 2, you will be prompted to enter it here.

> **Note for macOS users:** Newer macOS versions often integrate with the Keychain. You might use  
> `ssh-add --apple-use-keychain ~/.ssh/id_ed25519` or find it's added automatically after first use if you saved the passphrase to the Keychain.

### Step 4: Add Your Public SSH Key to Your GitHub/Bitbucket Account

You need to provide your public key to your Git hosting service.

Copy your public key to your clipboard. The `cat` command displays it in the terminal:

```bash
cat ~/.ssh/id_ed25519.pub
```

Select and copy the entire output, starting with `ssh-ed25519 ...` and ending with your email address.

**Add the key to your Git provider:**

#### GitHub:

- Log in to GitHub.
- Click your profile picture (top-right) → **Settings**.
- In the left sidebar, click **"SSH and GPG keys"**.
- Click **"New SSH key"** or **"Add SSH key"**.
- Give it a descriptive **"Title"** (e.g., "Personal Laptop").
- Paste your copied public key into the **"Key"** field.
- Click **"Add SSH key"**.

#### Bitbucket:

- Log in to Bitbucket.
- Click your profile picture (bottom-left) → **Personal settings**.
- Under **"Security"** in the left sidebar, click **"SSH keys"**.
- Click **"Add key"**.
- Give it a **"Label"** (e.g., "Personal Laptop").
- Paste your copied public key into the **"Key"** field.
- Click **"Add key"**.

### Step 5: Test Your SSH Connection

Verify that you can connect successfully.

**For GitHub:**
```bash
ssh -T git@github.com
```

**For Bitbucket:**
```bash
ssh -T git@bitbucket.org
```

You might see a warning like:

> The authenticity of host 'github.com (IP ADDRESS)' can't be established....Are you sure you want to continue connecting (yes/no/[fingerprint])?

Type `yes` and press Enter. This adds the host's key to your `~/.ssh/known_hosts` file.

If successful, you'll see a message like:

- **GitHub:** `Hi username! You've successfully authenticated, but GitHub does not provide shell access.`
- **Bitbucket:** `logged in as username.`

You are now set up to use SSH with your Git provider! You can clone, pull, and push repositories using SSH URLs (e.g., `git@github.com:username/repo.git`).

---

## Part 2: Managing Multiple SSH Keys/Accounts

This is relevant if you need separate identities (e.g., work vs. personal) for different Git repositories or providers.

### Step 1: Generate Additional Key Pairs (If Needed)

If you don't already have a separate key pair for your other identity, generate one, making sure to specify a different filename:

```bash
# Example for a personal key
ssh-keygen -t ed25519 -C "your_personal_email@example.com" -f ~/.ssh/id_ed25519_personal

# Example for a work key
ssh-keygen -t ed25519 -C "your_work_email@example.com" -f ~/.ssh/id_ed25519_work
```

Remember to use strong passphrases.

### Step 2: Add Public Keys to Respective Accounts

Add the corresponding public key (`.pub` file) to the settings of the correct GitHub/Bitbucket account, just like in Part 1, Step 4.  
Ensure `~/.ssh/id_ed25519_personal.pub` goes to your personal account, and `~/.ssh/id_ed25519_work.pub` goes to your work account.

### Step 3: Configure Your SSH Config File (`~/.ssh/config`)

This file tells SSH which key to use for which connection.

Open or create the file:

```bash
nano ~/.ssh/config
```

Define different `Host` entries (aliases) for each identity/account. Use `IdentityFile` to point to the correct private key.

Example `~/.ssh/config`:

```text
# Personal GitHub account
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes

# Work GitHub account (using default github.com host)
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes

# Example for Bitbucket work account
Host bitbucket.org-work
  HostName bitbucket.org
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
```

- `Host github.com-personal`: A custom alias you choose.
- `HostName`: The actual server address.
- `IdentityFile`: Path to the private key file for this host alias.
- `IdentitiesOnly yes`: **Crucial!** Prevents SSH from trying default keys if the specified one fails.

Save the file (`Ctrl+X`, `Y`, `Enter` in nano).  
Set correct permissions:

```bash
chmod 600 ~/.ssh/config
```

### Step 4: Update Git Remote URLs

For repositories that should use a non-default identity (e.g., your personal repos), update their remote URLs to use the corresponding alias from your `~/.ssh/config`.

```bash
# Navigate to your local repository
cd path/to/your/repo

# Check the current remote
git remote -v

# Update the URL using the alias (replace username and repo.git)
git remote set-url origin git@github.com-personal:username/repo.git

# Example for work Bitbucket repo
# git remote set-url origin git@bitbucket.org-work:username/repo.git

# Verify the change
git remote -v
```

### Step 5: Test Each Configuration

Test each host alias defined in your `~/.ssh/config`:

```bash
ssh -T git@github.com-personal      # Should greet you as your personal GitHub username
ssh -T git@github.com               # Should greet you as your work GitHub username
ssh -T git@bitbucket.org-work       # Should log you in as your work Bitbucket username
```

---

## Part 3: Common Troubleshooting

If you encounter errors, check these common causes and solutions.

### Error: `Permission denied (publickey)` or `fatal: Could not read from remote repository.`

This is the most common error.

**Causes:**
- **Public key not added:** Your public key (`.pub`) hasn't been added to your GitHub/Bitbucket account settings. (See Part 1, Step 4).
- **Wrong key used:** SSH is presenting the wrong key (especially if you have multiple keys).  
  Ensure your `~/.ssh/config` is set up correctly (Part 2, Step 3).  
  Ensure your repository's remote URL uses the correct host alias (Part 2, Step 4).  
  Verify which key SSH is trying to use with:

  ```bash
  ssh -vT git@hostname
  ```

  Look for lines mentioning `Offering public key` or `Identities`.

- **Key not loaded in agent:** If your key has a passphrase, the SSH agent might not have it loaded.

  ```bash
  ssh-add -l
  ```

  If missing, add it with:

  ```bash
  ssh-add ~/.ssh/your_private_key
  ```

- **Incorrect file permissions:** SSH is strict about permissions.

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/id_*           # Private keys
chmod 644 ~/.ssh/*.pub          # Public keys
chmod 644 ~/.ssh/known_hosts
```

### Error: `Host key verification failed.`

This means the host key presented by the server has changed since the last time you connected.

- **Legitimate change:** Server keys sometimes get updated.
- **Potential security risk:** Could indicate a man-in-the-middle attack (less likely but possible).

**Solution:** Remove the old key:

```bash
ssh-keygen -R hostname
```

Then try connecting again:

```bash
ssh -T git@hostname
```

You’ll be prompted to accept the new key. Verify the fingerprint if possible before accepting.

### Error: `Agent admitted failure to sign using the key.`

The SSH agent is running but cannot use the key.

- **Key not added:** Use:

  ```bash
  ssh-add ~/.ssh/your_private_key
  ```

- **Passphrase required:** Running `ssh-add` should prompt for it.

### Error: Config file syntax errors (e.g., `Bad configuration option`, `keyword extra arguments`)

Means your `~/.ssh/config` file has formatting errors.

- **Check syntax:** Each directive should be on its own line.
- **Check indentation:** Indent lines under `Host` consistently (2 or 4 spaces).
- **Check comments:** Comments should start with `#`. Avoid placing configuration on the same line as a comment.

Refer to correct structure in Part 2, Step 3.

### Error: Wrong user identified (`Permission denied to wrong_user`)

SSH connects but authenticates you as the wrong user.

**Cause:** SSH used a key associated with the wrong account.

**Solution:**
- Verify your `~/.ssh/config` (Part 2, Step 3).
- Verify your Git remote URL uses the correct alias:

  ```bash
  git remote -v
  ```

- Test using the specific alias:

  ```bash
  ssh -T git@your-host-alias
  ```

---

## General Tips

- **Verbose Output:** Use `-v` for detailed info:

  ```bash
  ssh -vT git@hostname
  ```

  Add more `v`s (`-vv`, `-vvv`) for extra detail.

- **Check SSH Agent:**

  ```bash
  ssh-add -l
  ```
