# Setup Secure Shell (SSH)

This Composite Action will setup and start the SSH (Secure Shell) Agent, and optionally configure GitHub known hosts, custom known hosts, or one private key.

## Usage

Use this Action in one of your Workflow steps.

```yaml
- name: Setup SSH
  uses: godaddy-wordpress/setup-secure-shell@v1
```

### With Inputs

This Action comes with three inputs, and only the GitHub known hosts are added by default.

#### Disable GitHub Known Hosts

To disable adding GitHub known hosts, simply set the `add-gh-known-hosts` input to `no`:

```yaml
- name: Setup SSH
  uses: godaddy-wordpress/setup-secure-shell@v1
  with:
    add-gh-known-hosts: no
```

#### Add Custom Known Hosts

Adding custom known hosts must include the literal-strip `|-` block scalar style, and each host must be added on a separate line.

```yaml
- name: Setup SSH
  uses: godaddy-wordpress/setup-secure-shell@v1
  with:
    ssh-known-hosts: |-
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0SdG6UOoqKLsabgH5C9okWi0dh2l9GKJl
```

#### Add a Private Key

For security reasons, adding a private key is accomplished by first setting the key to an environment variable for the step, then referencing the variable in the input.

```yaml
- name: Setup SSH
  uses: godaddy-wordpress/setup-secure-shell@v1
  env:
    KEY_NAME: ${{ secrets.ssh-private-key }}
  with:
    ssh-private-key: $KEY_NAME
```

#### All Inputs Together

```yaml
- name: Setup SSH
  uses: godaddy-wordpress/setup-secure-shell@v1
  env:
    KEY_NAME: ${{ secrets.ssh-private-key }}
  with:
    add-gh-known-hosts: no
    ssh-private-key: $KEY_NAME
    ssh-known-hosts: |-
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0SdG6UOoqKLsabgH5C9okWi0dh2l9GKJl
```

## Note on Private Keys

The default behavior of SSH is to read from the `id_rsa` and `id_rsa.pub` keys. While there is an ability to use `.ssh/config` to configure different keys to use for different hosts, that is beyond the scope of this Action. If you have a project that requires multiple keys, there are a couple ways you can approach this problem:

### Change Keys (easy)

Often times you may need multiple keys, but not necessarily at the same time. This can be accomplished by first deleting current the SSH key (assuming you added one), then adding the new key:

```yaml
- name: Change key
  env:
    OTHER_KEY_NAME: ${{ secrets.other-ssh-private-key }}
  run: |
    ssh-add -D
    ssh-add - <<< "$OTHER_KEY_NAME"
```

This will keep the SSH default behavior, and threat this new key as the `id_rsa` key pair.

### Add Configuration (complex)

If your project requires using multiple keys simultaneously, then you are likely going to have to add an SSH configuration file (`.ssh/config`) and route the hosts to the correct identity files. As this is beyond the scope of this Action, please refer to your best online source for creating a custom configuration file.

---

Copyright © 2022  [GoDaddy Operating Company, LLC](https://godaddy.com) &nbsp;&middot;&nbsp; All Rights Reserved &nbsp;&middot;&nbsp; [License](LICENSE)

[![GoDaddy Engineering](https://raw.githubusercontent.com/godaddy-wordpress/.github/master/assets/godaddy-oss-readme-banner.webp)](https://www.godaddy.com/engineering/)
