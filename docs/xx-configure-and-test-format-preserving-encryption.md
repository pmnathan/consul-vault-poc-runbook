Format Preserving Encryption
============================

- Documentation <https://www.vaultproject.io/docs/secrets/transform>  
- Tutorial <https://learn.hashicorp.com/tutorials/vault/transform>  
- API <https://www.vaultproject.io/api-docs/secret/transform>
- Code Samples <https://www.hashicorp.com/blog/encrypting-data-while-preserving-formatting-with-the-vault-enterprise-transform-s>  
- Demo <https://github.com/nicholasjackson/demo-vault/tree/master/transform>

Before following the steps below ensure that the vault client on the machine is set up and configured with the VAULT_ADDR, VAULT_TOKEN environment variables. The following steps are performed by Admins hence the VAULT_TOKEN should either be a root token or one with admin privileges.


### Step 1: Enable Secret Engine ###

```
>vault secrets enable transform
```

### Step 2: Create a named role (could be field in database) ###

```
>vault write transform/role/payments transformations=ccn-fpe
```


### Step 3: Create Transformation ###

```
>vault write transform/transformation/ccn-fpe \
  type=fpe \
  template=ccn \
  tweak_source=internal \
  allowed_roles=payments
```

### Step 4: Setup new policy / update existing policy ###
Create a policy to allow user to access the transform engine, then assign role to user
```
vault policy write transform_policy -<<EOF
# To request data encoding using any of the roles
# Specify the role name in the path to narrow down the scope
path "transform/encode/*" {
   capabilities = [ "update" ]
}

# To request data decoding using any of the roles
# Specify the role name in the path to narrow down the scope
path "transform/decode/*" {
   capabilities = [ "update" ]
}
EOF
```


### Step 5: Encode Data ###

```
>vault write transform/encode/payments value=1111-2222-3333-4444
```

### Step 6: Decode Data ###

```
>vault write transform/decode/payments value=9300-3376-4943-8903
```


### Misc Step 7: List Transformations ###
```
>vault list transform/transformations/fpe
```


### Misc Step 8: Describe Transform ###
```
>vault read transform/transformations/fpe/ccn-fpe
```