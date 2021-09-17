Transit Encryption
==================

- Documentation <https://www.vaultproject.io/docs/secrets/transit>  
- Tutorial <https://learn.hashicorp.com/tutorials/vault/eaas-transit>  
- Test API <https://www.vaultproject.io/api/secret/transit>
- Java Example <https://learn.hashicorp.com/tutorials/vault/eaas-spring-demo?in=vault/app-integration>
- Python Example 1 <https://arctiq.ca/2020/01/10/using-vaults-encryption-as-a-service-secrets-engine-transit/>
- Python Example 2 <https://www.hashicorp.com/resources/encryption-as-a-service-with-vault-s-transit-secret-engine>
- Terraform <https://registry.terraform.io/providers/hashicorp/vault/latest/docs/data-sources/transit_encrypt>


Before following the steps below ensure that the vault client on the machine is set up and configured with the VAULT_ADDR, VAULT_TOKEN environment variables. The following steps are performed by Admins hence the VAULT_TOKEN should either be a root token or one with admin privelages.

For details on the steps below please review the documentation above.

The Vault UI can be used to test the transit encryption capabilities of Vault.

### Step 1: Enable Secret Engine ###

```
>vault secrets enable transit
```

### Step 2: Enable Encryption Key Ring ###

```
>vault write -f transit/keys/<key_name>
```


### Step 3: Setup Policy ###
Create a policy to allow user to access the transit engine
```
vault policy write transit_policy -<<EOF
path "transit/encrypt/<key_name>" {
   capabilities = [ "update" ]
}
path "transit/decrypt/<key_name>" {
   capabilities = [ "update" ]
}
EOF
```

if using a policy file use the command below - make sure you store the policy in a file.

```
>vault policy write policy-name policy-file.hcl
````



### Step 4: Assign policy to Auth Role / Token ###
Option 1 -  Create token with policy
```
>vault token create -policy=transit_policy
```

Option 2 -  Create user with policy
```
>vault write auth/userpass/users/prakash \
    password="s3cr3t!" \
    policies="dev-readonly,transit_policy"
```


### Step 5: Encrypt Data ###
Using Vault Client

```
>VAULT_TOKEN=<client_token> vault write transit/encrypt/<key_name> \
    plaintext=$(base64 <<< "4111 1111 1111 1111")
```

Using Curl
```
>base64 <<< "4111 1111 1111 1111"
NDExMSAxMTExIDExMTEgMTExMQo=


>curl --header "X-Vault-Token: <client_token>" \
       --request POST \
       --data '{"plaintext": "NDExMSAxMTExIDExMTEgMTExMQo="}' \
       https://127.0.0.1:8200/v1/transit/encrypt/orders | jq

```


### Step 6: Decrypt Data ###
Using Vault Client
```
>VAULT_TOKEN=<client_token> vault write transit/decrypt/orders \
    ciphertext="vault:v1:cZNHVx+sxdMErXRSuDa1q/pz49fXTn1PScKfhf+PIZPvy8xKfkytpwKcbC0fF2U="
    

>base64 --decode <<< "Y3JlZGl0LWNhcmQtbnVtYmVyCg=="
```

Using Curl

```
>curl --header "X-Vault-Token: <client_token>" \
   --request POST \
   --data '{"ciphertext": "Yvault:v1:/9hdQutaWpZR72s3+VSCLK1JNhV1wKM49hYVjh7R...snip..."}' \
   https://127.0.0.1:8200/v1/transit/decrypt/orders | jq
   
>base64 --decode <<< "Y3JlZGl0LWNhcmQtbnVtYmVyCg=="
4111 1111 1111 1111

```

For key rotation and other features please visit the docs above.
