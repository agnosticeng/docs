# Liquidity Events

## Availability

{% hint style="success" %}
This collection is available for the **Ethereum, Polygon,  Arbitrum,** and **Base**.
{% endhint %}

| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                                                                     |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>defi_liquidity_events_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>defi_liquidity_events_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>defi_liquidity_events_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>defi_liquidity_events_base_mainnet_v1</code></mark></p> |

## Methodology

The table is built by extracting data from DEXes activity and then normalizing it to fit a unified data model.

We currently support the following DEXes:

* **Uniswap V2**
* **Uniswap V3**
* **Curve**

{% hint style="info" %}
We extract data from any contract compatible with one of the above DEXes ABI.

It means that any DEXes that forked or tried to be compatible at the ABI level with these contracts will be indexed automatically.
{% endhint %}

## Table Schema

<table data-full-width="false"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (<code>ethereum</code>, <code>arbitrum</code>, <code>polygon</code>, ...).</td></tr><tr><td>chain_network_name</td><td>string</td><td>name of the network (<code>mainnet</code>).</td></tr><tr><td>block_hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>block_number</td><td>uint64</td><td>Block height</td></tr><tr><td>transaction_index</td><td>uint64</td><td>The index of the transaction in the block</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was collated</td></tr><tr><td>decoder_name</td><td>string</td><td>The internal name of the decoder used to decode this trade (uniswap_v2_liquidity_event, curve_liquidity_event)</td></tr><tr><td>type</td><td>string</td><td>Either <mark style="color:blue;"><code>mint</code></mark> or <mark style="color:blue;"><code>burn</code></mark></td></tr><tr><td>factory</td><td>string</td><td>The address of the DEX factory contract (if any)</td></tr><tr><td>contract</td><td>string</td><td>The address of the DEX pair/pool</td></tr><tr><td>provider</td><td>string</td><td>The address of the account that provided or withdraw the liquidity</td></tr><tr><td>raw_amounts</td><td>map(string, uint256)</td><td>A map of token amounts provided or withdrawn</td></tr><tr><td>amounts</td><td>map(string, float64)</td><td>A map of token amounts provided or withdrawn. The amount of tokens are divided by <code>pow(10, </code><mark style="color:blue;"><code>decimals</code></mark><code>)</code> where <mark style="color:blue;">decimals</mark> is the number of decimals declared by the token (<strong>USDT</strong> has <strong>6</strong> decimals)</td></tr></tbody></table>

## Usage

The query below makes use of the <mark style="color:blue;">`defi_liquidity_events_ethereum_mainnet_v1`</mark> to chart the daily delta of liquidity for each token of the famous Curve 3Pool (0xbEbc44782C7dB0a1A60Cb6fe97d0b483032FF1C7).

```sql
select 
    date_trunc('day', timestamp) as date,
    sum(if(type = 'mint', amounts['0x6b175474e89094c44da98b954eedeac495271d0f'], -amounts['0x6b175474e89094c44da98b954eedeac495271d0f'])) as delta_dai,
    sum(if(type = 'mint', amounts['0xdac17f958d2ee523a2206206994597c13d831ec7'], -amounts['0xdac17f958d2ee523a2206206994597c13d831ec7'])) as delta_usdt,
    sum(if(type = 'mint', amounts['0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48'], -amounts['0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48'])) as delta_usdc
from defi_liquidity_events_ethereum_mainnet_v1
where contract = '0xbEbc44782C7dB0a1A60Cb6fe97d0b483032FF1C7'
and timestamp >= now() - interval 30 day 
group by date
```

