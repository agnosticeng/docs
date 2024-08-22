# Balances

## Availability

{% hint style="success" %}
This table is available for the **Ethereum, Polygon, Arbitrum** and **Base**.
{% endhint %}

| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>token_balances_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>token_balances_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>token_balances_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>token_balances_base_mainnet_v1</code></mark></p> |

## Methodology

The table is built by the following process:

1. Identify all <mark style="color:blue;">`Transfer`</mark> events (shared by <mark style="color:blue;">`ERC-20`</mark> and <mark style="color:blue;">`ERC-721`</mark>) emitted in a block
2. Call the <mark style="color:blue;">`balanceOf`</mark> method for each pair of (token, wallet) extracted from these events

{% hint style="info" %}
Some smart contracts have very inefficient implementations of the <mark style="color:blue;">`balanceOf`</mark> method. To ensure the timely execution of our indexer, we decided to put a hard cap of gas usage for every <mark style="color:blue;">`balanceOf`</mark> call we make. The current value is **2000000 gas**.
{% endhint %}

## Table Schema

<table data-full-width="false"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (<code>ethereum</code>, <code>arbitrum</code>, <code>polygon</code>, ...).</td></tr><tr><td>chain_network_name</td><td>string</td><td>name of the network (<code>mainnet</code>).</td></tr><tr><td>block_hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>block_number</td><td>uint64</td><td>Block height</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was collated</td></tr><tr><td>wallet_address</td><td>string</td><td>The address of the wallet</td></tr><tr><td>token_address</td><td>string</td><td>The address of the token</td></tr><tr><td>value</td><td>uint256</td><td>The amount of token held</td></tr></tbody></table>

## Usage

The below query makes use of the <mark style="color:blue;">`token_balances_ethereum_mainnet_v1`</mark> to list all the latest non-zero balances for every ERC-20 and ERC-721 token for the Binance 14 (0x28C6c06298d514Db089934071355E5743bf21d60) wallet.

```sql
select 
    token_address,
    argMax(value, block_number) as balance
from token_balances_ethereum_mainnet_v1
where wallet_address = '0x28C6c06298d514Db089934071355E5743bf21d60'
and value > 0
group by token_address
order by balance desc
```
