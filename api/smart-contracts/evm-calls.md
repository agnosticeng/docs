---
description: The core EVM blockchain data collection
---

# EVM Calls

## Availability

{% hint style="success" %}
This collection is available for the **Ethereum**, **Polygon,**  **Arbitrum** and **Base**.
{% endhint %}

| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                     |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>evm_calls_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_calls_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_calls_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>evm_calls_base_mainnet_v1</code></mark></p> |



## Mapping rules

The table is wide and sparse.  Each call's input and output is stored in a column named after its <mark style="color:blue;">`index`</mark> in the input list and its derived <mark style="color:blue;">`type`</mark>.&#x20;

The column's name for a given input is derived as such: `input_`<mark style="color:blue;">`index`</mark>`_value_`<mark style="color:blue;">`type.`</mark>

The column's name for a given output is derived as such: `output_`<mark style="color:blue;">`index`</mark>`_value_`<mark style="color:blue;">`type`</mark>

{% hint style="info" %}
We support calls with up to **12** inputs and **3** outputs.
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
Here is how we store the various inputs and outputs of the  <mark style="color:blue;">transfer</mark> function:

<mark style="color:blue;">`transfer(recipient address, amount uint256)(ok bool)`</mark>

* **recipient** value is stored in the column **input\_0\_value\_address**
* **amount** value is stored in the column **input\_1\_value\_uint256**
* **ok** value is stored in the column **output\_0\_value\_uint8**
{% endhint %}

## Table Schema

<table data-full-width="false"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (<code>ethereum</code>, <code>arbitrum</code>, <code>polygon</code>, ...).</td></tr><tr><td>chain_network_name</td><td>string</td><td>name of the network (<code>mainnet</code>).</td></tr><tr><td>block_hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>block_number</td><td>uint64</td><td>Block height</td></tr><tr><td>block_index</td><td>uint32</td><td>Index of the call in the block</td></tr><tr><td>transaction_index</td><td>uint32</td><td>Index of the transaction in the block</td></tr><tr><td>transaction_status</td><td>uint32</td><td>Status of the transaction</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was collated</td></tr><tr><td>signature</td><td>string</td><td>Signature of the event as defined per the ABI spec (<code>transfer(address,uint256)</code>)</td></tr><tr><td>fullsig</td><td>string</td><td>Signature of the event as defined per the ABI spec with the addition of the indexed modifier (<code>transfer(address,uint256)(bool)</code>)</td></tr><tr><td>from</td><td>string</td><td>Address of the caller</td></tr><tr><td>gas</td><td>uint32</td><td>Number of gas used during the call</td></tr><tr><td>value</td><td>uint256</td><td>Amount of native token sent to the contract</td></tr><tr><td>call_type</td><td>string</td><td>The type of method used (<strong>call</strong>, <strong>delegatecall</strong>, <strong>staticcall</strong>, ...)</td></tr><tr><td>subcalls</td><td>uint32</td><td>The number of traces below this one in the call stack</td></tr><tr><td>call_address</td><td>uint32[]</td><td>The position of this trace in the call stack</td></tr><tr><td>error</td><td>string</td><td>The error returned during the execution of the method, if any</td></tr><tr><td>input_<mark style="color:blue;"><code>index</code></mark>_value<em>_</em><mark style="color:blue;"><code>type</code></mark></td><td></td><td>Content of the input at <mark style="color:blue;"><code>index</code></mark></td></tr><tr><td>output_<mark style="color:blue;"><code>index</code></mark>_type</td><td>string</td><td>ABI type of the output at <mark style="color:blue;"><code>index</code></mark></td></tr><tr><td>output_<mark style="color:blue;"><code>index</code></mark>_value_type</td><td></td><td>Content of the output at <mark style="color:blue;"><code>index</code></mark></td></tr></tbody></table>

## Usage

The below query make use of the <mark style="color:blue;">`evm_events_ethereum_mainnet_v1`</mark> table to report on gas usage per each method of the ShibaInu-ETH Uniswap V2 pair.

We compute the total gas used and average gas used for each method called since genesis.

The result is sorted by decreasing total gas used value.

```sql
select 
    signature,
    sum(gas) as total_gas,
    avg(gas) as avg_gas,
    count(*) as total_calls,
    countIf(error <> '') as total_errors
from evm_calls_ethereum_mainnet
where to = '0x811beEd0119b4AfCE20D2583EB608C6F7AF1954f'
group by signature
order by total_gas DESC
```
