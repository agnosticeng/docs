---
description: The core EVM blockchain data collection
---

# Blocks

## Availability

{% hint style="success" %}
This collection is available for **Ethereum**, **Polygon,** **Arbitrum,** and **Base**.
{% endhint %}



| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                        |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>evm_blocks_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_blocks_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_blocks_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_block_base_mainnet_v1</code></mark></p> |



## Table Schema

<table data-full-width="true"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (eg: ethereum, arbitrum, polygon, ...)</td></tr><tr><td>chain_network_name</td><td>string</td><td>Name of the network (eg: mainnet)</td></tr><tr><td>hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>number</td><td>uint64</td><td>Block height</td></tr><tr><td>parent_hash</td><td>string</td><td>Hash of the block's parent</td></tr><tr><td>transactions_root</td><td>string</td><td>Root of the transaction trie of the block</td></tr><tr><td>state_root</td><td>string</td><td>Root of the final state trie of the block</td></tr><tr><td>receipts_root</td><td>string</td><td>Root of the receipts trie of the block</td></tr><tr><td>miner</td><td>string</td><td>Address to whom the mining rewards were sent</td></tr><tr><td>difficulty</td><td>uint256</td><td>Difficulty for this block</td></tr><tr><td>total_difficulty</td><td>uint256</td><td>Total difficulty of the chain until this block</td></tr><tr><td>size</td><td>uint32</td><td>Size of this block in bytes</td></tr><tr><td>extra_data</td><td>string</td><td>Extra data field of the block</td></tr><tr><td>gas_limit</td><td>uint32</td><td>Maximum gas allowed in the block</td></tr><tr><td>gas_used</td><td>uint32</td><td>Total used gas by all transactions in the block</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was formed</td></tr><tr><td>base_fee_per_gas</td><td>uint64</td><td>Base fee per gas consumed in the block</td></tr><tr><td>transaction_count</td><td>uint32</td><td>Number of transactions in the block</td></tr><tr><td>transaction_effective_gas_price</td><td>[]uint64</td><td>Effective gas price for each transaction in the block</td></tr><tr><td>transaction_gas_used</td><td>[]uint32</td><td>Eas used for each transaction in the block</td></tr><tr><td>transaction_status</td><td>[]uint16</td><td>Transaction status for each transaction in the block</td></tr><tr><td>transaction_type</td><td>[]uint16</td><td>Transaction type for each transaction in the block</td></tr></tbody></table>

## Usage

The query below make use of the <mark style="color:blue;">`evm_blocks_ethereum_mainnet_v1`</mark> table to compute the average gas price per hour for the last 24 hours.

```sql
select
    date_trunc('hour', timestamp) as ts,
    avg(arrayAvg(transaction_effective_gas_price)) as avg_gas_price
from
    evm_blocks_ethereum_mainnet_v1
where timestamp >= now() - interval 24 hour
group by ts
order by ts
```

