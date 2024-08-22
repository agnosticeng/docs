# 👨‍🔬 Create your first chart

## Let's dive deep into those tools

This guide will walk you through the simple steps to create your first chart. You can build your charts with the SQL language over the datasets available in **Agnostic**. This guide will walk you through the steps to harness the full potential of this powerful tool.

{% hint style="warning" %}
**Prerequisites:**&#x20;

Before we begin, please ensure that you are logged into your **Agnostic** account and have the right permissions to create charts within your project.
{% endhint %}

### Step 1: Create a chart resource

1. Once you're logged in, you'll land on the project page where you can manage and view project resources like Charts, GraphQL Endpoints, and Dashboards.
2. To create your first chart, locate the "Create" button on the page and click on it. A dropdown menu will appear – select "Chart."
3. A modal will pop up, prompting you to name your chart. Provide a descriptive name for easy identification. For this example, we will use « USDC Transferred to Coinbase VS Binance »

<figure><img src="../../.gitbook/assets/Create Chart modal.png" alt=""><figcaption><p>Create chart modal</p></figcaption></figure>

Once you've clicked on the "Create" button. You'll be seamlessly redirected to the newly created chart, ready for customization and configuration.

### Step 2: Writing SQL Queries

1. In the Chart interface, you'll see a text editor where you can write SQL queries. Start by composing your query based on the dataset available in Agnostic. You can use standard SQL syntax, including SELECT statements, JOINs, GROUP BY, and more.
2. As you type, you'll benefit from autocomplete suggestions for SQL keywords, tables, and columns from the Agnostic dataset. This feature streamlines the query-writing process and helps prevent typos and errors.
3. After composing your query, execute it using the "Play" button. You'll instantly see the results displayed in a table format, resembling a classic database GUI.

### Step 3: Chart Configuration

1. To create a chart, you need to define how the SQL query results will be visualized. Displaying chart relies on aliases to designate the chart's axes:

<table><thead><tr><th width="207">Axis</th><th>Aliases</th></tr></thead><tbody><tr><td><strong>X-axis</strong></td><td>Use the <code>chart_x</code> alias to define your X values</td></tr><tr><td><strong>Y-axis</strong></td><td>Use the <code>chart_y</code> alias to define your Y values</td></tr><tr><td><strong>Z-axis</strong> <em>(Optional)</em></td><td><p>There are two ways to define the Z-axis</p><ul><li>By using the <code>chart_z</code></li><li>Or by using a label on the Y-axis values like so <code>chart_y_{LABEL}</code><br>Example: <code>chart_y_USDC</code> and <code>chart_y_DAI</code> </li></ul></td></tr></tbody></table>

2. Configure your SQL query to generate results that match the axis aliases you've defined.
3. Then select your chart type by clicking on  <img src="../../.gitbook/assets/sliders-horizontal (1).png" alt="Configuration modal" data-size="original"> (Chart configuration) - select "Line"

<figure><img src="../../.gitbook/assets/Chart configuration modal.png" alt=""><figcaption><p>Chart configuration modal</p></figcaption></figure>

#### A clickbait example 👀

Let's create a chart showing the number of USDC transferred to Coinbase vs. Binance by week from May 2021.

```sql
select
  'Coinbase' as chart_z,
  date_trunc('week', timestamp) as chart_x,
  sum(input_2_value_uint256) as chart_y
from 
  evm_events_ethereum_mainnet
where
  timestamp >= '2021-05-01' and
  address = '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48' and -- USDC token Address
  signature = 'Transfer(address,address,uint256)' and 
  input_1_value_address in (
  	'0x71660c4005BA85c37ccec55d0C4493E66Fe775d3', -- Coinbase 1
  	'0x503828976D22510aad0201ac7EC88293211D23Da', -- Coinbase 2
  	'0xddfAbCdc4D8FfC6d5beaf154f18B778f892A0740', -- Coinbase 3
  	'0x3cD751E6b0078Be393132286c442345e5DC49699', -- Coinbase 4
  	'0xb5d85CBf7cB3EE0D56b3bB207D5Fc4B82f43F511', -- Coinbase 5
  	'0xeB2629a2734e272Bcc07BDA959863f316F4bD4Cf', -- Coinbase 6
  	'0x02466E547BFDAb679fC49e96bBfc62B9747D997C', -- Coinbase 8
  	'0xA9D1e08C7793af67e9d92fe308d5697FB81d3E43', -- Coinbase 10
  	'0x77696bb39917C91A0c3908D577d5e322095425cA', -- Coinbase 11
  	'0x7c195D981AbFdC3DDecd2ca0Fed0958430488e34', -- Coinbase 12
  	'0x95A9bd206aE52C4BA8EecFc93d18EACDd41C88CC', -- Coinbase 13
  	'0xb739D0895772DBB71A89A3754A160269068f0D45'  -- Coinbase 14
  ) 
group by
  chart_x
order by
  chart_x asc

UNION ALL

select
  'Binance' as chart_z,
  date_trunc('week', timestamp) as chart_x,
  sum(input_2_value_uint256) as chart_y
from 
  evm_events_ethereum_mainnet
where
  timestamp >= '2021-05-01' and
  address = '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48' and -- USDC token Address
  signature = 'Transfer(address,address,uint256)' and 
  input_1_value_address in (
  	'0x3f5CE5FBFe3E9af3971dD833D26bA9b5C936f0bE', -- Binance
  	'0xD551234Ae421e3BCBA99A0Da6d736074f22192FF', -- Binance 2
  	'0x564286362092D8e7936f0549571a803B203aAceD', -- Binance 3
  	'0x0681d8Db095565FE8A346fA0277bFfdE9C0eDBBF', -- Binance 4
  	'0xfE9e8709d3215310075d67E3ed32A380CCf451C8', -- Binance 5
  	'0x4E9ce36E442e55EcD9025B9a6E0D88485d628A67', -- Binance 6
  	'0xBE0eB53F46cd790Cd13851d5EFf43D12404d33E8', -- Binance 7
  	'0xF977814e90dA44bFA03b6295A0616a897441aceC', -- Binance 8
  	'0x001866Ae5B3de6cAa5a51543FD9fB64f524F5478', -- Binance 9
  	'0x85b931A32a0725Be14285B66f1a22178c672d69B', -- Binance 10
  	'0x708396f17127c42383E3b9014072679b2F60B82f', -- Binance 11
  	'0xE0F0CfDe7Ee664943906f17F7f14342E76A5CeC7', -- Binance 12
  	'0x8f22f2063d253846b53609231ed80fa571bc0c8f', -- Binance 13
  	'0x28C6c06298d514Db089934071355E5743bf21d60', -- Binance 14
  	'0x21a31Ee1afC51d94C2eFcCAa2092aD1028285549', -- Binance 15
  	'0xDFd5293D8e347dFe59E90eFd55b2956a1343963d'  -- Binance 16
  )
group by
  chart_x
order by
  chart_x asc
```

<figure><img src="../../.gitbook/assets/USDC Tranferred (CoinbaseBinance).png" alt=""><figcaption><p>Chart - USDC Transferred (Coinbase/Binance)</p></figcaption></figure>

{% hint style="info" %}
Watch the result from the public chart [USDC Transferred (Coinbase/Binance)](https://app.agnostic.engineering/s/c/7yxFnTYgnAT).
{% endhint %}



### Step 4: Previewing and Saving

1. Once your SQL query and chart aliases are defined, click the "Play" button (on the top right corner of the IDE). **Agnostic** will process your query and generate a chart based on the specified aliases.
2. Review the generated chart to ensure it accurately represents the data and visualization you intended to create.
3. If everything looks good, you can save the chart within your Agnostic project by clicking the "Save" button.



Congratulations! You've successfully created your first chart with Agnostic. You can now view, edit, and share your chart with your team or stakeholders or even with the world by clicking on the button "Share". This tool empowers you to leverage your SQL skills to craft customized visualizations from your blockchain data. Explore the possibilities, refine your queries, and unlock actionable insights with this advanced data visualization tool.



{% hint style="success" %}
Visualize your blockchain data with Agnostic's tools and explore different chart types and configurations for valuable insights.
{% endhint %}

