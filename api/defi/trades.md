# Trades

## Availability

{% hint style="success" %}
This collection is available for the **Ethereum, Polygon,  Arbitrum,** and **Base**.
{% endhint %}

| Points-of-Presence | Tables                                                                                                                                                                                                                                                                                                                             |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eu-west-1          | <p><mark style="color:blue;"><code>defi_trades_ethereum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>defi_trades_polygon_mainnet_v1</code></mark><br><mark style="color:blue;"><code>defi_trades_arbitrum_mainnet_v1</code></mark><br><mark style="color:blue;"><code>defi_trades_base_mainnet_v1</code></mark></p> |

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

<table data-full-width="false"><thead><tr><th>Column Name</th><th>Column Type</th><th>Description</th></tr></thead><tbody><tr><td>chain_name</td><td>string</td><td>Name of the chain (<code>ethereum</code>, <code>arbitrum</code>, <code>polygon</code>, ...).</td></tr><tr><td>chain_network_name</td><td>string</td><td>name of the network (<code>mainnet</code>).</td></tr><tr><td>block_hash</td><td>string</td><td>Block hash encoded as binary string</td></tr><tr><td>block_number</td><td>uint64</td><td>Block height</td></tr><tr><td>transaction_index</td><td>uint64</td><td>The index of the transaction in the block</td></tr><tr><td>timestamp</td><td>datetime</td><td>UNIX timestamp for when the block was collated</td></tr><tr><td>decoder_name</td><td>string</td><td>The internal name of the decoder used to decode this trade (uniswap_v2_trade, curve_trade)</td></tr><tr><td>factory</td><td>string</td><td>The address of the DEX factory contract (if any)</td></tr><tr><td>contract</td><td>string</td><td>The address of the DEX pair/pool the actually executed the trade</td></tr><tr><td>sender</td><td>string</td><td>The address of the account the called the contract</td></tr><tr><td>receiver</td><td>string</td><td>The address of the account that received the swapped amount</td></tr><tr><td>origin</td><td>string</td><td>The address of the EOA that triggered the transaction</td></tr><tr><td>token_sold_address</td><td>string</td><td>The address of the sold token</td></tr><tr><td>token_sold_symbol</td><td>string</td><td>The symbol of the sold token</td></tr><tr><td>token_sold_raw_amount</td><td>uint256</td><td>The amount of token sold</td></tr><tr><td>token_sold_amount</td><td>float64</td><td>The amount of token sold divided by <code>pow(10, </code><mark style="color:blue;"><code>decimals</code></mark><code>)</code> where <mark style="color:blue;">decimals</mark> is the number of decimals declared by the token (<strong>USDT</strong> has <strong>6</strong> decimals)</td></tr><tr><td>token_bought_address</td><td>string</td><td>The address of the bought token</td></tr><tr><td>token_bought_symbol</td><td>string</td><td>The symbol of the bought token</td></tr><tr><td>token_bought_raw_amount</td><td>uint256</td><td>The amount of token bought</td></tr><tr><td>token_bought_amount</td><td>float64</td><td>The amount of token bought divided by <code>pow(10, </code><mark style="color:blue;"><code>decimals</code></mark><code>)</code> where <mark style="color:blue;">decimals</mark> is the number of decimals declared by the token (<strong>USDT</strong> has <strong>6</strong> decimals)</td></tr><tr><td>price</td><td>float64</td><td>The price of the trade, computed by doing: <mark style="color:blue;"><code>token_bought_amount / token_sold_mount</code></mark></td></tr></tbody></table>

## Usage

The query below makes use of the <mark style="color:blue;">`defi_trades_ethereum_mainnet_v1`</mark> table to get the necessary data to display the well-known candlestick chart for the price of USDC (0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) on the Uniswap V3 USDC/ETH Pool (0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640) for the last 30 days.

```sql
select 
  date_trunc('day', timestamp) as date,
  argMin(price, timestamp) as open,
  argMax(price, timestamp) as close,
  min(price) as _min,
  max(price) as _max
from defi_trades_ethereum_mainnet_v1
where contract = '0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640'
and token_sold_address = '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48'
and timestamp >= now() - interval 30 day
group by date
order by date desc 
```
