---
layout: post
title: How to use Apollo Federation in Elixir
subtitle: Scaling GraphQL in Absinthe
date:       2021-08-11
author:     "Scott"
header-img: "img/coding.jpg"
catalog:    true
tags:
  - coding
  - GraphQL
---


## What’s and Why Apollo Federation

Let get started from a small question, what’s the differences between Federation and Stitching?

> *Schema stitching* was an early option for creating a GraphQL gateway API, and schema stitching  requires a lot of boilerplate code at the gateway level to effectively consolidate the data graph  

> Apollo Federation allowing us to split a large schema in a way that makes sense (for example, by product teams) and then access and extend types from different services as needed  

> What’s more, it does so using only spec-compliant features of GraphQL and automagically generates the gateway API.  

So in short, there are two main components of [Apollo Federation](https://www.apollographql.com/docs/federation/):

* The *implementing* services that each represent part of the overall graph
* A top-level *gateway* API aggregated from the implementing service’s federated schemas

Before jump into the code . There is A few key concepts

##  A few key concepts

* **Entities:** These are types that can be referenced and extended by another service; in other words, they are the connection points of our federated graph.
* **Keys:** We declare an entity by adding the @key directive to a type definition.
* **External type extensions:** We can extend a type defined in a different service and add new fields to it using the extend type syntax. We can then use a combination of the @key and @external directives to connect the original type and the type extension.

- - - -

Ps. The above content is copied from the first chapter of this book: https://8bit.press/book/advanced-graphql , This is a great book about GraphQL, the first chapter is free, highly recommended.

## Setup

All the code you can find here: [scottming/supergraph-demo](https://github.com/scottming/supergraph-demo), and if you don't want to care too much about the details, you can view it by switching branches.

### 1. Init three apps

```bash
$ mkdir supergraph-demo && cd supergraph-demo
$ mix phx.new inventory --no-ecto --no-assets --no-mailer --no-live --no-dashboard --no-gettext --no-html
$ mix phx.new users --no-ecto --no-assets --no-mailer --no-live --no-dashboard --no-gettext --no-html
$ mix phx.new products --no-ecto --no-assets --no-mailer --no-live --no-dashboard --no-gettext --no-html  
```


### 2. Create Schemas

`products/lib/products_web/schema.ex`

```elixir
defmodule ProductsWeb.Schema do
  use Absinthe.Schema

  @products [
    %{
      id: "apollo-federation",
      sku: "federation",
      package: "absinthe_federation",
      variation: "OSS"
    },
    %{
      id: "apollo-studio",
      sku: "studio",
      package: "",
      variation: "platform"
    }
  ]

  query do
    field :all_products, list_of(:product) do
      resolve(fn _, _, _ -> {:ok, @products} end)
    end

    field :product, :product do
      arg(:id, non_null(:id))

      resolve(fn %{id: id}, _ -> {:ok, @products |> Enum.find(&(&1.id == id))} end)
    end
  end

  object :product do
    field(:id, non_null(:id))
    field(:sku, :string)
    field(:package, :string)

    field :variation, :product_variation do
      resolve(fn
        %{variation: variation}, _, _ ->
          {:ok, %{id: variation}}

        %{id: id}, _, _ ->
          {:ok, Enum.find(@products, &(&1.id == id)) |> Map.get(:variation)}

        _, _, _ ->
          {:error, :error}
      end)
    end

    field :dimensions, :product_dimension do
      resolve(fn _, _, _ -> {:ok, %{size: "1", weight: 1}} end)
    end

    field :created_by, :user do
      resolve(fn _, _, _ ->
        {:ok, %{email: "support@apollographql.com", total_products_created: 1337}}
      end)
    end
  end

  object :product_variation do
    field(:id, non_null(:id))
  end

  object :product_dimension do
    field(:size, :string)
    field(:weight, :float)
  end

  object :user do
    field(:email, non_null(:id))

    field(:total_products_created, :integer)
  end
end
```


`users/lib/users_web/schema.ex`

```elixir
defmodule UsersWeb.Schema do
  use Absinthe.Schema

  @users [
    %{
      email: "support@apollographql.com",
      name: "Apollo Studio Support",
      total_products_created: 4,
      __typename: "User"
    }
  ]

  query do
    field(:users, list_of(:user), resolve: fn _, _ -> {:ok, @users} end)
  end

  object :user do
    field(:email, non_null(:id))
    field(:name, :string)
    field(:total_products_created, :integer)
  end
end
```


`inventory/lib/inventory_web/schema.ex`

```elixir
defmodule InventoryWeb.Schema do
  use Absinthe.Schema

  @delivery [
    %{id: "apollo-federation", estimated_delivery: "6/25/2021", fastest_delivery: "6/24/2021"},
    %{id: "apollo-studio", estimated_delivery: "6/25/2021", fastest_delivery: "6/24/2021"}
  ]

  query do
    field(:deliveries, list_of(:delivery_estimates), resolve: fn _, _ -> {:ok, @delivery} end)
  end

  object :product_dimension do
    field(:size, :string)
    field(:weight, :float)
  end

  object :delivery_estimates do
    field(:estimated_delivery, :string)
    field(:fastest_delivery, :string)
  end
end
```

By checking out this [commit](https://github.com/scottming/supergraph-demo/tree/c3e448a3ba5b1587d2b517183edcfec3a8b0a61b), you will find that all three services are available for  queries,  but they are not federated

### 3. Federate them

So, how to federate them by using Absinthe.Federation is easy. We need to 

1. `use Absinthe.Federation.Schema`，The line adds some phases, modifies Absinthe's default pipeline, and creates the `_service` and `_entity` fields for each service.
2. Add the `extends` macro to each services schema file's `root query` because when we create a gateway, the real `RootQueryType` is owned by a gateway, so we should extend it.
3. Declare the entity by `key_fields` macro if that service owns that entity.

You can check out this [commit diff](https://github.com/scottming/supergraph-demo/commit/887f5cc9e482c0d1fd04bc8ac56e8ad3d7b1da61) for more details.

### 4. Final Result

After federation, when you run `yarn start` in `gateway` folder, you will be able to query the federation results:

```graphql
query Query {
  allProducts {
    id
    sku
    createdBy {
      email
      totalProductsCreated
    }
  }
}
```

```json
{
  data: {
    allProducts: [
      {
        id: "apollo-federation",
        sku: "federation",
        createdBy: {
          email: "support@apollographql.com",
          totalProductsCreated: 1337
        }
      },{
        id: "apollo-studio",
        sku: "studio",
        createdBy:{
          email: "support@apollographql.com",
          totalProductsCreated: 1337
        }
      }
    ]
  }
```


![](https://scottming-blog-1253938325.cos.ap-beijing.myqcloud.com/2021-12-19-211219-federation-result.png)

## Deploy Tools

Because we are using the spec of Apollo federation, we can use a range of Apollo tools such as studio and rover-cli tools to manage our deployment schema.

First, you can follow this [tutorial](https://www.apollographql.com/docs/studio/schema/cli-registration/) to config your `rover-cli`; **Rover** is for managing and maintaining graphs with  [Apollo Studio](https://www.apollographql.com/docs/studio/) :

And let’s build the subgraph, create a folder named `subgraphs`, and move the three subgraph services into it.

Then, generate three `subgraph.schema`:

```bash
$ cd subgraphs/users && mix absinthe.federation.schema.sdl --schema UsersWeb.Schema users.graphql
$ cd ../products && mix absinthe.federation.schema.sdl --schema ProductsWeb.Schema products.graphql
$ cd ../inventory && mix absinthe.federation.schema.sdl --schema InventoryWeb.Schema inventory.graphql
```

And create a `supergraph.yaml`

```yaml
subgraphs:
  products:
    routing_url: http://127.0.0.1:4001
    schema:
      file: ./subgraphs/products/products.graphql
  users:
    routing_url: http://127.0.0.1:4002
    schema:
      file: ./subgraphs/users/users.graphql
  inventory:
    routing_url: http://127.0.0.1:4003
    schema:
      file: ./subgraphs/inventory/inventory.graphql
```


then use `rover` compose them to a `supergraph.graph`


```bash
$ rover supergraph compose --config ./supergraph.yaml > supergraph.graphql
```

Finally, publish your schema to your new graph.

```bash
$ rover graph publish <your-new-graphid>@current --schema supergraph.graphql
```

### Use `router` instead of `apollo`

The other day Apollo released their second version of the gateway: [apollographql/router🚀](https://github.com/apollographql/router), and this is written by rust, which in performance, security, or bug rate reduction have been greatly improved.

Although there are only 100 stars now, and they are not available for production, I reckon the second generation is definitely the future, we need a stable gateway so much, and the project without Javascript is really tempting.

Let’s start each Elixir services:

```bash
$ cd <products|reviews|inventory>
$ mix phx.server
```

And start the router gateway

```bash
$ router -s supergraph.graphql
```

Finally, we don't have a single line of Node.js code in our project.

## The benefits

* Decoupling by domain
Federation is a good to split the Monolith project into several services, This makes sense, and it's also handy for service scaling.

* Wonderful development experience
There are many benefits to working inside a small repository , personally I think the biggest benefit is the extremely fast test feedback.

* Safer
No need to limit to a single point node.

## If you adopt federation, what do you need to change?

* Architecture: split the services.
* A little bit of the technology stack change(Rust or Node.js)
* Common Absinthe context Plug and authentication Middleware

### common Absinthe context Plug and authentication Middleware

Because Gateway doesn't do any validation logic for you, and we don't want to, we need to implement a generic absinthe context plug and a common validation middleware


