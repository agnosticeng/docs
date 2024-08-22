---
description: The core EVM blockchain data collection
---

# EVM Events

## Availability

{% hint style="success" %}
This collection is available for **Ethereum**, **Polygon, Arbitrum, Base** and **BSC**.
{% endhint %}

| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>evm_events_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_events_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_events_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_events_base_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_events_bsc_mainnet_v1</code></mark></p> |



## Mapping rules

The table is wide and sparse.  Each event's input is stored in a column named after its <mark style="color:blue;">`index`</mark> in the input list and its derived <mark style="color:blue;">`type`</mark>.&#x20;

The column's name for a given input is derived as such: `input_`<mark style="color:blue;">`index`</mark>`_value_`<mark style="color:blue;">`type`</mark>

{% hint style="info" %}
We support events with up to **12** inputs.
{% endhint %}

The mapping rules used to derive an input type from an ABI type are specified in the table below.

| ABI Type                         | Derived Type   |
| -------------------------------- | -------------- |
| address                          | address        |
| string                           | string         |
| bytes                            | string         |
| bytes\<M> where 0 < M <= 32      | string         |
| bool                             | uint8          |
| uint8                            | uint8          |
| uint`X` where 8 < `X` <= 32      | uint32         |
| uint`X` where 32 < `X` <= 64     | uint64         |
| uint`X` where 64 < `X` <= 256    | uint256        |
| int8                             | int8           |
| int`X` where 8 < `X` <= 32       | int32          |
| int`X` where 32 < `X` <= 64      | int64          |
| int`X` where 64 < `X` <= 256     | int256         |
| address\[]                       | address\_array |
| string\[]                        | string\_array  |
| bool\[]                          | uint8\_array   |
| uint8\[]                         | uint8\_array   |
| uint`X`\[] where 8 < `X` <= 32   | uint32\_array  |
| uint`X`\[] where 32 < `M` <= 64  | uint64\_array  |
| uint`X`\[] where 64 < `M` <= 256 | uint256\_array |
| int8\[]                          | int8\_array    |
| int`X`\[] where 8 < `X` <= 32    | int32\_array   |
| int`X`\[] where 32 < `X` <= 64   | int64\_array   |
| int`X`\[] where 64 < `X` <= 256  | int256\_array  |

{% hint style="info" %}
Here is how we store the various inputs of <mark style="color:blue;">`Transfer`</mark> event:

<mark style="color:blue;">`Transfer(to address, from address, amount uint256))`</mark>

* **to** value is stored in the column **input\_0\_value\_address**
* **from** value is stored in the column **input\_1\_value\_address**
* **amount** is stored in the column **input\_2\_value\_uint256**
{% endhint %}

## Table Schema

<table data-full-width="false"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (<code>ethereum</code>, <code>arbitrum</code>, <code>polygon</code>, ...).</td></tr><tr><td>chain_network_name</td><td>string</td><td>name of the network (<code>mainnet</code>).</td></tr><tr><td>block_hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>block_number</td><td>uint64</td><td>Block height</td></tr><tr><td>block_index</td><td>uint32</td><td>Index of the event in the block</td></tr><tr><td>transaction_index</td><td>uint32</td><td>Index of the transaction in the block</td></tr><tr><td>transaction_status</td><td>uint32</td><td>Status of the transaction</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was collated</td></tr><tr><td>signature</td><td>string</td><td>Signature of the event as defined per the ABI spec (<code>Deposit(address,uint256)</code>)</td></tr><tr><td>fullsig</td><td>string</td><td>Signature of the event as defined per the ABI spec with the addition of the indexed modifier (<code>Transfer(address indexed,address indexed,uint256)</code>)</td></tr><tr><td>address</td><td>string</td><td>Address of the contract that emitted the event</td></tr><tr><td>removed</td><td>uint8</td><td>Removed field of the log</td></tr><tr><td>log_index</td><td>uint32</td><td>Index of the log in the block</td></tr><tr><td>input_<mark style="color:blue;"><code>index</code></mark>_type</td><td>string</td><td>ABI type of the input at <mark style="color:blue;"><code>index</code></mark></td></tr><tr><td>input_<mark style="color:blue;"><code>index</code></mark>_value<em>_</em><mark style="color:blue;"><code>type</code></mark></td><td></td><td>Content if the input at <mark style="color:blue;"><code>index</code></mark></td></tr></tbody></table>



## Usage

The query below make use of the <mark style="color:blue;">`evm_events_ethereum_mainnet_v1`</mark> table to retrieve the number of transfers and the amount transferred for each day since the beginning of the year, for USDC (0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48).

```sql
select 
    date_trunc('day', timestamp) as t, 
    count(*) as count, 
    sum(input_2_value_uint256) as amount 
from evm_events_ethereum_mainnet_v1 
where 
    address = '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48' 
    and signature = 'Transfer(address,address,uint256)' 
    and timestamp >= '2023-01-01' 
group by t 
order by t desc
```

<figure><img src="../../.gitbook/assets/Screenshot 2023-09-21 at 19.49.14 (1).png" alt=""><figcaption><p>The query executed with psql</p></figcaption></figure>
