# Set up SSH

1. Generate SSH key

```bash
ssh-keygen -t ed25519 -C "user@email.com"
```

2. Start SSH agent

```bash
eval "$(ssh-agent -s)"
```

3. Add local key

```bash
ssh-add ~/.ssh/id_ed25519
```

4. Check keys and get public key

```bash
cat ~/.ssh/id_ed25519*
```

5. Add key to GitHub

  - [https://github.com/settings/keys](https://github.com/settings/keys)

6. Check SSH connection

```bash
ssh -T git@github.com
```

