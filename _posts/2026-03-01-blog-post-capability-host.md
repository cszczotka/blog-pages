## Creation capability host for Azure AI Foundry account.

Capability host is required to run hosted agents in AI Foundry project. Hosted agents require an account-level capability host with public hosting enabled (private VNET has not supported yet)

Creation capability hosts from azure cli:

```bash
az rest --method put \
  --url "https://management.azure.com/subscriptions/[SUBSCRIPTIONID]/resourceGroups/[RESOURCEGROUPNAME]/providers/Microsoft.CognitiveServices/accounts/[ACCOUNTNAME]/capabilityHosts/accountcaphost?api-version=2025-10-01-preview" \
  --headers "Content-Type=application/json" \
  --body "{
    \"properties\": {
      \"capabilityHostKind\": \"Agents\",
      \"enablePublicHostingEnvironment\": true
    }   
  }"

```

Checking existing capability hosts from azure cli

```bash
  az rest --method get --url "https://management.azure.com/subscriptions/[SUBSCRIPTIONID]/resourceGroups/[RESOURCEGROUPNAME]/providers/Microsoft.CognitiveServices/accounts/[ACCOUNTNAME]/capabilityHosts/accountcaphost?api-version=2025-10-01-preview"

```
