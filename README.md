# Get private variables

```bash
infra-data.yml
```

Or maybe, if `vmware_inventory.py` setup break things

```bash
infra-data.yml -i inventory/local
```

Presently mainly use to define `vcenter_hostname` and
`vcenter_username`

# Get secret private variables

```bash
infra-secret.yml
```

Presently mainly use to define `vcenter_password` from a *vault* file

# Configure vmware_inventory.py

```bash
inventory.yml -i inventory/local
```

- make link to `vmware_inventory.py` in `inventory` dir using
  dedicated ressource name
- copy `vmware_inventory.ini` in `inventory` dir using dedicated
  ressource name
- configure named *ini* file using imported *data* and *secret* vars

# Fill ansible fact cache

```bash
refresh_inventory.yml -i inventory/local 
```

- Currently use default `cache_max_age` of 3600.

# Alternative way to call vmware_inventory

Using [passwordstore.org](https://www.passwordstore.org/) password
manager instead of ansible vault.

```bash
env VMWARE_PASSWORD=$(pass oxa/vcenter) ansible local -m meta -a noop
```

# Show a prettified version of facts

```bash
jq . < .cache/ansible/ansible-vmware.cache > tmp/ansible-vmware.js
```

# Prepare to generate a mapping from guest to UUID

```bash
jq='[._meta.hostvars[] | { name: .name, uuid: .config.uuid }]'
jq "$jq" < .cache/ansible/ansible-vmware.cache > tmp/vmname2uuid.js
```

# The final jq script

```bash
jq='[._meta.hostvars[] | { (.name): .config.uuid }] | reduce .[] as $i ({}; .+ $i) | { vmname2uuid: . }';
jq='reduce ._meta.hostvars[] as $i ({}; .+ { ($i.name): $i.config.uuid }) | { vmname2uuid: . }';
jq "$jq" < .cache/ansible/ansible-vmware.cache > tmp/vmname2uuid.js
```
