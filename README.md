# vault-backup-script
As you know there are different types of backend storage for hashicorp Vault deployment. the most used kinds are file and integrated raft storage. when the backend storage is file (hostpath persistent volume in a kubernetes cluster) we cannot use the default backup method of vault "vault operator raft snapshot save mysnapshot.snap" to take backup because Vault API backend doesnt support such storage type. in this scenario we have to take a backup from /vault/data location to have backup for later use. here is the script.


#!/bin/bash

# Stop Vault server
vault operator step-down
# use the below line if Vault is deployed as a service in the OS
vault server -config=/etc/vault/config.hcl -dev -dev-listen-address="127.0.0.1:8201" &
# use the below line if Vault is deployed as a pod in a Kubernetes Cluster.
vault server -config=/vault/data/extraconfig-from-values.hcl -dev -dev-listen-address="127.0.0.1:8201" &
sleep 10 # Wait for Vault to start

# Take a backup of the Vault data directory
backup_dir="/path/to/backup/directory"
vault_data_dir="/path/to/vault/data/directory"
timestamp=$(date +%Y%m%d_%H%M%S)
backup_file="${backup_dir}/vault_backup_${timestamp}.tar.gz"
tar -czf "$backup_file" -C "$vault_data_dir" .

# Restart Vault server
vault operator seal
