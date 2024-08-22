# HTTP

## Connection details

<table><thead><tr><th width="374">Field</th><th>Value</th></tr></thead><tbody><tr><td><strong>host</strong></td><td><p><code>sql.eu-west-1.agnostic.engineering</code></p><p><code>sql.us-east-1.agnostic.engineering</code></p></td></tr><tr><td><strong>port</strong></td><td><code>443</code></td></tr><tr><td><strong>access token</strong></td><td><code>Either as </code><mark style="color:blue;"><code>Authorization</code></mark><code> header or as </code><mark style="color:blue;"><code>token</code></mark><code> query parameter.</code></td></tr></tbody></table>

Here is examples with `curl`:

```
curl 'https://sql.eu-west-1.agnostic.engineering/query?authorization=GZRewjgxJ1jDatk84FtrVmg7kLeHine3VyPkJcQzf1s9' --data 'select max(block_number) from evm_events_ethereum_mainnet_v1'
```

