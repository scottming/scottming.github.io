---
layout: post
title: 关于 joi
subtitle: 一个用 Elixir 写成的验证库
date:       2021-01-06
author:     "Scott"
header-img: "img/joi.png"
catalog:    true
tags:
  - Tools
---


## 背景

去年在公司做一个基于 CQRS 的项目时，需要用到验证库，因为 CQRS 强调读写分离，而且不是普通数据库层的读写分离，所以写那端显然不适合再继续使用 Ecto.Changeset 的那种验证了。

在探索和使用了 Vex，Skooma 等库后，发现他们都不能达成我的期望，Vex 是类 `Rails ActiveModel Validations` 型的验证，在自定义一些模块的时候，不够灵活，在 format error 的时候则需要调用两次验证，而 Skooma 则是过于灵活，各种类型的 `schema` 都能定义，但这种灵活也带来了一些改写时候的复杂度，比如我要支持国际化 format error 时就需要改动大量的底层代码，这些我都无法忍受。

所以在开发 [scottming/joi](https://github.com/scottming/joi) 时，我有以下这些目标：


1. 支持大部分 nodejs 下原生 joi 所支持的类型
2. 支持嵌套的验证
3. 能轻易的支持错误的国际化
4. 便于扩展


## 下载

```elixir
def deps do
  [
    {:joi, "~> 0.1.4"},
  ]
end
```


## 使用

Joi 主要使用 `Joi.validate` 函数根据定义的 Schema 来验证数据，如果数据是正确的，那么函数返回 `{:ok, data}`，返回的数据将根据 schema 进行强制转换。


如果数据和模型不匹配，那么会返回一个这样的数据结构：

```
{:error, [%{field: field, message: message, type: type, constraint: constraint}]}
```

如果某个 field 没有定义，那么关于这个字段，`Joi.validate` 啥也不做。


以下是一个综合的例子：


```elixir
schema = %{
  id: [:string, uuid: true],
  username: [:string, min_length: 6],
  pin: [:number, min: 1000, max: 9999],
  new_user: [:boolean, truthy: ["1"], falsy: ["0"], required: false],
  account_ids: [:list, type: :number, max_length: 3],
  remember_me: [:boolean, required: false]
}

data = %{id: "c8ce4d74-fab8-44fc-90c2-736b8d27aa30", username: "user@123", pin: 1234, new_user: "1", account_ids: [1, 3, 9]}

Joi.validate(data, schema)
# {:ok,
# %{
#   account_ids: [1, 3, 9],
#   id: "c8ce4d74-fab8-44fc-90c2-736b8d27aa30",
#   new_user: "1",
#   pin: 1234,
#   username: "user@123"
# }}

Joi.validate(%{}, schema)
# {:error,
#  [
#    %{
#      constraint: true,
#      field: :username,
#      message: "username is required",
#      type: "string.required"
#    },
#    %{
#      constraint: true,
#      field: :pin,
#      message: "pin is required",
#      type: "number.required"
#    },
#    %{
#      constraint: true,
#      field: :id,
#      message: "id is required",
#      type: "string.required"
#    },
#    %{
#      constraint: true,
#      field: :account_ids,
#      message: "account_ids is required",
#      type: "list.required"
#    }
#  ]}
```


### Schema 的定义

```elixir
schema = %{
  id: [:string, uuid: true],
  username: [:string, min_length: 6],
  pin: [:number, min: 1000, max: 9999],
  new_user: [:boolean, truthy: ["1"], falsy: ["0"], required: false],
  account_ids: [:list, type: :number, max_length: 3],
  remember_me: [:boolean, required: false]
}
```

Schema 首先是个 map，然后分为两部分，一部分是 field，另外一部分则关于这个 field 具体的定义，拿 `id` 为例，`:id` 是个键，注意这是个原子，当然你如果需要验证的是 `string` 的键的话，那你需要写成 `"id" => [:string, uuid: true]`， 而 `[:string, uuid: true]` 则是具体的验证，也分为两部分，第一部分是 type，第二部分则是一个 keywords list。


## 目前支持的类型

* [x] boolean
* [x] date
* [x] datetime
* [x] list
* [x] number
* [x] string
* [ ] map


## 自定义函数


自定义函数非常简单，你只需要定义一个函数，接收 `field` 和 `data` 这两个参数的输入，然后输出跟默认验证函数保持一致，另外把这个函数加在 schema 那个 field 的验证部分，并以 `:f` 作为 key 即可。

```elixir
func = fn field, data -> 
  case data[field] == 1 do
    true -> {:ok, data}
    false -> {
      :error, 
      %{type: "custom", field: field, message: "does not match the custom function", constraint: "custom"}
    }
  end
end

schema = %{id: [:number, f: func]}
data = %{id: 2}
Joi.validate(data, schema)
# {:error,
# [
#   %{
#     constraint: "custom",
#     field: :id,
#     message: "does not match the custom function",
#     type: "custom"
#   }
# ]}
```


