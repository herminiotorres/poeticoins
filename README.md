# Poeticoins

## Gettting trades with Gun

IEx iteraction:
```elixir
iex -S mix
Erlang/OTP 23 [erts-11.1.4] [source] [64-bit] [smp:12:12] [ds:12:12:10] [async-threads:1] [hipe]

Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
iex[1]> {:ok, conn} = :gun.open('ws-feed.pro.coinbase.com', 443, %{protocols: [:http]})
{:ok, #PID<0.365.0>}
iex[2]> flush
{:gun_up, #PID<0.365.0>, :http}
{:gun_down, #PID<0.365.0>, :http, :closed, [], []}
:ok

iex[3]> :gun.ws_upgrade(conn, "/")
#Reference<0.1578025923.2389966849.183325>
iex[4]> flush
{:gun_upgrade, #PID<0.365.0>, #Reference<0.1578025923.2389966849.183325>,
 ["websocket"],
 [
   {"date", "Thu, 24 Dec 2020 18:54:32 GMT"},
   {"connection", "upgrade"},
   {"set-cookie",
    "__cfduid=d6543b5a40bbc76f7a3756b5dd7dc2aba1608836072; expires=Sat, 23-Jan-21 18:54:32 GMT; path=/; domain=.pro.coinbase.com; HttpOnly; SameSite=Lax"},
   {"upgrade", "websocket"},
   {"sec-websocket-accept", "I3wJrFPlZxfRrkNKfsob+a4S+ZM="},
   {"cf-cache-status", "DYNAMIC"},
   {"cf-request-id", "0737b35a5e0000f6ab961ad000000001"},
   {"expect-ct",
    "max-age=604800, report-uri=\"https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct\""},
   {"server", "cloudflare"},
   {"cf-ray", "606c87d2acd2f6ab-GRU"}
 ]}
:ok

iex[5]> subscription_msg = %{
...[5]>   type: "subscribe",
...[5]>   product_ids: ["BTC-USD"],
...[5]>   channels: ["ticker"]
...[5]> }
%{channels: ["ticker"], product_ids: ["BTC-USD"], type: "subscribe"}
iex[6]> Jason.encode!(subscription_msg)
"{\"channels\":[\"ticker\"],\"product_ids\":[\"BTC-USD\"],\"type\":\"subscribe\"}"
iex[13]> :gun.ws_send(conn, {:text, Jason.encode!(subscription_msg)})
:ok
iex[14]> flush
{:gun_ws, #PID<0.365.0>, #Reference<0.1578025923.2389966849.184071>,
 {:text,
  "{\"type\":\"subscriptions\",\"channels\":[{\"name\":\"ticker\",\"product_ids\":[\"BTC-USD\"]}]}"}}
{:gun_ws, #PID<0.365.0>, #Reference<0.1578025923.2389966849.184071>,
 {:text,
  "{\"type\":\"ticker\",\"sequence\":18995554396,\"product_id\":\"BTC-USD\",\"price\":\"23344.62\",\"open_24h\":\"23603.61\",\"volume_24h\":\"19784.18073089\",\"low_24h\":\"22600\",\"high_24h\":\"23620.68\",\"volume_30d\":\"582433.73684855\",\"best_bid\":\"23344.62\",\"best_ask\":\"23346.55\",\"side\":\"sell\",\"time\":\"2020-12-24T19:01:02.941167Z\",\"trade_id\":115723286,\"last_size\":\"0.00542442\"}"}}
```

* Gun - Guide User: https://ninenines.eu/docs/en/gun/2.0/guide/
* Coinbase - Websocket Feed: https://docs.pro.coinbase.com/#websocket-feed
* Coinbase - Subscribe Message: https://docs.pro.coinbase.com/#subscribe
* Coinbase - Ticker Channel: https://docs.pro.coinbase.com/#the-ticker-channel
