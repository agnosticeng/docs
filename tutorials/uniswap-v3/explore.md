# Explore

To explore the Agnostic data ocean, you can use a wide range of tools from `psql` to `Grafana` and, of course, the integrated data visualization tool from Agnostic.

<figure><img src="../../.gitbook/assets/Screenshot 2023-09-15 at 12.01.38.png" alt=""><figcaption><p>Token price from Uniswap events</p></figcaption></figure>

Let's break down the query with a few comments:

```sql
SELECT
  date_trunc('hour', timestamp) as chart_x, -- we trunc timestamp to hour
  1 / (
    pow (1.0001, avg(input_6_value_int32)) -- we apply our formula to the sixth parameter of the signature: the tick
    / pow (10, 18 - 6) -- we consider each token decimals here
  ) as chart_y 
FROM
  evm_events_ethereum_mainnet -- the event data collection for the Ethereum mainnet
WHERE
  address = '0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640' -- the address of the ETH/USDC pool
  and signature = 'Swap(address,address,int256,int256,uint160,uint128,int24)' -- filter the right signature
  and chart_x >= '2023-08-01'
GROUP BY
  chart_x
ORDER BY
  chart_x ASC
```





