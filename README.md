# vault-backup-script
As you know there are different types of backend storage for hashicorp Vault deployment. the most used kinds are file and integrated raft storage. when the backend storage is file (hostpath persistent volume in a kubernetes cluster) we cannot use the default backup method of vault "vault operator raft snapshot save mysnapshot.snap" to take backup because Vault API backend doesnt support such storage type. in this scenario we have to take a backup from /vault/data location to have backup for later use. here is the script.


