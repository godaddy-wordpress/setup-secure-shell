# Setup SSH (Composite Action)

Github Action for getting `ssh-agent` up and running, including pre-configuration of known_hosts for `github.com`.

## Usage

Use this in one of your Workflows steps.

```
- name: Setup SSH
  uses: gdcorp-wordpress/setup-ssh@main
```

Then, in another step, add a private key before running commands:

```
- name: Add key
  run: |
    ssh-add - <<< "${{ secrets.ssh-private-key }}"
```

Lastly, when using multple keys, besure to delete before adding the new key:

```
- name: Change key
  run: |
	ssh-add -D
    ssh-add - <<< "${{ secrets.ssh-private-key-2 }}"
```
