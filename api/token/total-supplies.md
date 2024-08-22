# Total Supplies

## Availability

{% hint style="success" %}
This table is available for the **Ethereum, Polygon,  Arbitrum** and **Base**.
{% endhint %}

| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>token_total_supplies_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>token_total_supplies_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>token_total_supplies_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>token_total_supplies_base_mainnet_v1</code></mark></p> |

## Methodology

The table is built by the following process:

1. Identify all <mark style="color:blue;">`Transfer`</mark> events (shared by <mark style="color:blue;">`ERC-20`</mark> and <mark style="color:blue;">`ERC-721`</mark>) emitted in a block
2. Call the <mark style="color:blue;">`totalSupply`</mark> method for each token extracted from these events

{% hint style="info" %}
Some smart contracts have very inefficient implementations of the <mark style="color:blue;">`totalSupply`</mark> method. To ensure the timely execution of our indexer, we decided to put a hard cap of gas usage for every <mark style="color:blue;">`totalSupply`</mark> call we make. The current value is **2000000 gas**.
{% endhint %}

## Table Schema

<table data-full-width="false"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (<code>ethereum</code>, <code>arbitrum</code>, <code>polygon</code>, ...).</td></tr><tr><td>chain_network_name</td><td>string</td><td>name of the network (<code>mainnet</code>).</td></tr><tr><td>block_hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>block_number</td><td>uint64</td><td>Block height</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was collated</td></tr><tr><td>token_address</td><td>string</td><td>The address of the token</td></tr><tr><td>value</td><td>uint256</td><td>The total supply of the token</td></tr></tbody></table>

## Usage

The below query makes use of the <mark style="color:blue;">`token_total_supplies_ethereum_mainnet_v1`</mark> to get the maximum total supply of USDC (0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) per day for the last 30 days.

```sql
select 
	date_trunc('day', timestamp) as date,
	argMax(value, block_number)
from token_total_supplies_ethereum_mainnet_v1
where token_address = '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48'
and timestamp >= now() - interval 30 day
group by date
order by date desc
```
