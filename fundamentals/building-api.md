# 👷 Building API

## Let's deliver dynamic content through GraphQL API

Our solution uses GraphQL in conjunction with our SQL engine to provide dynamic content based on dynamic requests. This guide will walk you through the process, ensuring you can deliver precisely what you need with ease and efficiency.

{% hint style="warning" %}
**Prerequisites:**&#x20;

Before we begin, please ensure that you are logged into your **Agnostic** account and have the right permissions to create GraphQL API within your project.
{% endhint %}

### Two Ways of Creating a GraphQL API

#### 1. From Scratch

* Once you're logged in, you'll land on the project page where you can manage and view project resources like Charts, GraphQL API, and Dashboards.
* To create your GraphQL API, locate the "Create" button on the page and click on it. A dropdown menu will appear – select "GraphQL API"
* A modal will pop up, prompting you to name your query. Provide a descriptive name for easy identification. The name of must be unique by project. As we will see later, a GraphQL Schema is generated by project

Once you've clicked on the "Create" button. You'll be seamlessly redirected to the newly created GraphQL API.

#### 2. From an Existing Chart

* Go to the chart you want to use as the basis for your API.
* You will be immediately redirected the newly created GraphQL API page. This method automatically uses the query from the chart, saving you from copying and pasting.
* Some adjustments are required to make it works.

### How it Works

Similar to charts, the data delivered by your API will be labeled using SQL aliases. This helps to structure the data for GraphQL queries effectively.

* **Fields:** To define fields in your GraphQL schema, use the alias format `gql_field_string_<field_name>`.
  * Example: `SELECT column_name AS gql_field_string_fieldName FROM table_name;`
* **Arguments:** To define arguments in your GraphQL schema, use the alias format `gql_arg_string_<arg_name>`.
  * Example: `SELECT column_name AS gql_field_string_fieldName FROM table_name WHERE column_name = gql_arg_string_argumentName;`

As you are editing, you will see a preview of the generated schema.

<figure><img src="../.gitbook/assets/Screenshot 2024-06-03 at 20.00.08.png" alt=""><figcaption><p>Daily count calls GraphQL API</p></figcaption></figure>

Once you've created your schema as it pleases you, you can test it by clicking on the tab "GraphQL query". On one side, you will have the GraphQL Editor where you can edit your GraphQL query on the other side you'll have the result in JSON format. Don't forget to save once you've finished to adjust the fields and arguments for your GraphQL API.

### How to Use your GraphQL API

To make full use of the GraphQL API you've created with **Agnostic**, follow the steps outlined below.

#### Endpoint URL

The URL for the GraphQL proxy is:

```
https://graphql.eu-west-1.agnostic.engineering/graphql
```

#### API Token

To authenticate your requests, you'll need an API Token. This token can be passed either through query parameters or headers.

* Query Parameters:&#x20;

```
https://graphql.eu-west-1.agnostic.engineering/graphql?Authorization=<token>
```

* Headers:

```
Authorization: <token>
```

#### Schema Generation

Schemas are generated based on the project you are working on. The project is automatically detected by the API Token you use, ensuring that your queries are correctly aligned with your project's schema

#### Example Usage

```bash
curl -X POST 'https://graphql.eu-west-1.agnostic.engineering/graphql' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: <token>' \
  -d '{"query": "{ count_daily_calls { date count } }"}'
```



Using the GraphQL API in Agnostic is a powerful way to interact with your project's data. By correctly using your API Token, you can securely query data within your project's schema. Enjoy the flexibility and efficiency of GraphQL with **Agnostic**!