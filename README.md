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
inventory.yml
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


# Show a prettified version of facts

```bash
jq . < .cache/ansible/ansible-vmware.cache > tmp/ansible-vmware.js
```

# Prepare to generate a mapping from guest to UUID

```bash
jq '[._meta.hostvars[] | { name: .name, uuid: .config.uuid }]' < .cache/ansible/ansible-vmware.cache > tmp/name2uuid.js
```
