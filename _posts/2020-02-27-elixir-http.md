---
layout: post
title: First Taste on Elixir
date: 2020-02-27
summary: My team is planning on migrating all Ruby code to Elixir. Today is my first touch on this functional programming language
categories: Elixir
---

## Task:
Make an API call to get data for courses in Curator 1 and then migrate them to Curator 2

## Challenge:
I need to process a map with a bunch of lists inside. There are values that are empty(not allowed), keys that have inconsistent names, and nested loops in functional programming. I also need to pipe `HTTPoison response` all around and manipulate with it.

## Overview
`Elixir` is wildly different from OOP languaes like Java or Python. For a beginner like me, I have to quickly get used to the immutability property of Elixir. It can cause some trouble at the beginning but once I get the hang of it, I believe it will be a joy to use the language (at least it is what people say).

## Make HTTP Request
Using an HTTP client `HTTPoison`. Note that the response is encoded, so I have to decode the `response.body` to make it as JSON.
```elixir
HTTPoison.get!(url, header, hackney: [basic_auth: {username, password}, pool: :default], recv_timeout: 500000)
|> Map.get(:body)
|> Jason.decode!()
# hackney is the http library. The above is to make an HTTP request with basic auth. Basic auth is not part of header. Pool is for it to open as many connections as needed
```
## Interact with Database
```elixir
item = Curator.Repo.get_by(Curator.Catalog.CatalogItem, code: course["code"], organization: course["organization"])
```
Use `Repo` to interact with database.

## Inconsistent Key name, empty values
So in the nested map, there is a key called `change_log` on our end, but the new version wants `changelog`. We have to make the key consistent. Also, the value is not allowed to be empty 
```elixir
version_params = if version_params["change_log"] == "" do
  Map.put(version_params, "changelog", "empty")
else
  Map.put(version_params, "changelog", version_params["change_log"])
end 
```

## Missing Short/Long Description
Some versions are missing short description or long description. In that case we use `get_and_update` to map the empty string or nil value to some real value
```elixir
|> Map.get_and_update("long_description", fn long_description ->
      case long_description do
        nil -> {long_description, "Long Description"}
        "" -> {long_description, "Long Description"}
        _ -> {long_description, long_description}
      end
    end)
```
The reason I can have the `"long_description"` here is that the `course` variable is piped all the way from the beginning to here. We can therefore refer to one specific key-value pair

## Some useful functional
* `IO.inspect(error, label: "Error Here: #{course["name"]}")`
Useful for debugging
* `Enum.each()` is used to write for loop or nested loop
* `elem(1)` many times a function returns a tuple. The tuple would look like this: `{:ok, something}`. The `something` can be of any type. We want to get the actual thing by calling `elem(1)`

