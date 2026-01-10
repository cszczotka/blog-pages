## Assign 'Key Vault Secrets Officer' role to your Microsoft Entra user name using CLI

Run the following command to retrieve the userPrincipalName from your account. This represents who the role will be assigned to.

```sh
userPrincipal=$(az rest --method GET --url https://graph.microsoft.com/v1.0/me \
    --headers 'Content-Type=application/json' \
    --query userPrincipalName --output tsv)
```

Run the following command to retrieve the resource ID of the key vault. The resource ID sets the scope for the role assignment to a specific key vault.

```sh
resourceID=$(az keyvault show --resource-group $resourceGroup \
    --name $keyVaultName --query id --output tsv)
```

Run the following command to create and assign the Key Vault Secrets Officer role.

```sh
az role assignment create --assignee $userPrincipal \
    --role "Key Vault Secrets Officer" \
    --scope $resourceID
```

Now you can add and retrieve a secret with Azure CLI

```sh
az keyvault secret set --vault-name $keyVaultName \
    --name "MySecret" --value "My secret value"
```

```sh
az keyvault secret show --name "MySecret" --vault-name $keyVaultName
```

