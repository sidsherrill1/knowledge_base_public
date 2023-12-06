# How to use Ansible Vault
The below workflow demonstrates how **ansible-vault** can be used to encrypt a file
of variables, and insert them into ansible files via their referenced keys.

1. Add variables to be encrypted to **group_vars/vault.yml**, prepending each variable
key with "vault_".
    ```bash
    # group_vars/vault.yml
    vault_kali_pw: kali
    ```
2. Copy variable to **group_vars/vars.yml**, removing the "vault_" prefix from the key,
and replacing the value with a reference to the vaulted key
    ```bash
    # group_vars/vars.yml
    kali_pw: "{{ vault_kali_pw }}"
    ```
3. Encrypt/decrypt as needed
    ```bash
    # To encrypt vault.yml run:
    ansible-vault encrypt group_vars/vault.yml
    
    # To decrypt vault.yml run:
    ansible-vault decrypt group_vars/vault.yml
    ```
4. With the above vars.yml and vault.yml defined, encrypted variables can then be
referenced in ansible files as follows:
    ```bash
    # inventory.yml
    virtualmachines:
      vars_files:
        - group_vars/vars.yml
        - group_vars/vault.yml
      hosts:
        kali:
          ansible_host: 192.168.51.130
          ansible_user: kali
          ansible_password: "{{ kali_pw }}"
    ```
5. To run a play that references variables encryped via ansible-vault, pass the
argument `--ask-vault-pass`
    ```bash
    ansible-playbook playbook.yml -i inventory.yaml --ask-vault-pass
    # Runs playbook!
    ```


