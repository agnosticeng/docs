---
description: Can we use Agnostic as a backend for Uniswap analytics?
---

# Definition

## The example

> Can we display a price curve of ETH/USDC projected from swap happening on-chain over time to thousands of users with the freshest data?

It shouldn't be an issue with a well-designed protocol like Uniswap and Agnostic powerful live aggregation and serving capabilities.

## The Uniswap V3 data model

Fortunately, Uniswap publishes many valuable Events, allowing us to track the protocol activity with our \`evm\_event\_\*\` data collection.\
\
Let's explore the [documentation](https://docs.uniswap.org/contracts/v3/reference/core/interfaces/pool/IUniswapV3PoolEvents) and find the Swap event signature; it's the one we are looking for here.

```solidity
event Swap(
  address sender,
  address recipient,
  int256 amount0,
  int256 amount1,
  uint160 sqrtPriceX96,
  uint128 liquidity,
  int24 tick
)
```

In this version of Uniswap, we have very interesting values: `sqrtPriceX96` and `tick`. In the previous versions, we had to calculate the price from the amounts directly. Let's try to look at the `tick`.



So, with this explanation from the documentation:

| Name                                    | Type                                   | Description                                                                              |
| --------------------------------------- | -------------------------------------- | ---------------------------------------------------------------------------------------- |
| `sender`                                | address                                | The address that initiated the swap call, and that received the callback                 |
| `recipient`                             | address                                | The address that received the output of the swap                                         |
| `amount0`                               | int256                                 | The delta of the token0 balance of the pool                                              |
| `amount1`                               | int256                                 | The delta of the token1 balance of the pool                                              |
| `sqrtPriceX96`                          | uint160                                | The sqrt(price) of the pool after the swap, as a Q64.96                                  |
| `liquidity`                             | uint128                                | The liquidity of the pool after the swap                                                 |
| <mark style="color:blue;">`tick`</mark> | <mark style="color:blue;">int24</mark> | <mark style="color:blue;">The log base 1.0001 of price of the pool after the swap</mark> |

We should be able to get the price with this simple formula:

```sql
pow(1.0001, tick)
```





