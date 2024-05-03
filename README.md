test-ansible-role-molecule-vault
================================

A basic collection with only one role, to show how to configure molecule, so that variables stored in a vault are available when running the tests.

# Explanation

The variable used by the role, is defined in the `group_vars/all` in the `playbooks` directory. The vault part follows [ansible best practices](https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#variables-and-vaults), with a first file `vault_all.yml`, containing the data to secure: `vault_secret_data: "42"`. Then a second file exposes the variable for usage in playbooks (as explained in [Playbooks Best Practises - Variables and Vaults](https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#variables-and-vaults)).


Then all the needed configuration occurs in `molecule.yml`.

```
provisioner:
  name: ansible
  config_options:
    defaults:
      vault_password_file: ${MOLECULE_SCENARIO_DIRECTORY}/../../../../../../vault.pw
  inventory:
    links:
      group_vars: ${MOLECULE_SCENARIO_DIRECTORY}/../../../../../../playbooks/group_vars/
```

In this context `${MOLECULE_SCENARIO_DIRECTORY}` translate to `vvision/tests/roles/example/molecule/default` (from the root of the git repository).

With the `vault_password_file` property, we specify the path to a file containing the password used to decrypt the vault.

With the `group_vars` property, we specify the path to our `group_vars` files containing the variables definition.

To conclude, this configuration allow us to use secrets from an ansible vault in the molecule test process.

# Execution

## Requirements

* Molecule: https://ansible.readthedocs.io/projects/molecule/installation/#requirements

## Install

Clone repository:
`cd test-ansible-role-molecule-vault`

Install virtualenv.
`virtualenv .venv`
`source .venv/bin/activate`

Install molecule:
`python3 -m pip install molecule ansible-core`.

Install molecule podman plugin:
`python3 -m pip install "molecule-plugins[podman]"`.

Or install molecule docker plugin:
`python3 -m pip install "molecule-plugins[docker]"`.


## Execute molecule test

From the root of the repository:
`cd vvision/tests/roles/example`.

Default test will use `podman`, docker available with `-s docker`.

Run the test:
`molecule test`.

## Execute playbook

From the root of the repository:
`cd playbooks`

Install collection (or re-install with `--force` option):
`ansible-galaxy install -r requirements.yml`

Run playbook locally:
`ansible-playbook --connection=local --inventory 127.0.0.1, --vault-password-file ../vault.pw example.yml`

Also possible to use: `DEFAULT_VAULT_PASSWORD_FILE=./vault.pw`

## Vault

View the content of the vault:
`ansible-vault view playbooks/group_vars/all/vault_all.yml`.
Edit the content of the vault:
`ansible-vault edit playbooks/group_vars/all/vault_all.yml`.

(See vault password in the `vault.pw` file).

## End

At the end, exit virtualenv with: `deactivate`.
