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

### CoinbaseClient

Connection stablished
```elixir
iex -S mix
Erlang/OTP 23 [erts-11.1.4] [source] [64-bit] [smp:12:12] [ds:12:12:10] [async-threads:1] [hipe]

Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
iex[1]>  Poeticoins.Exchanges.CoinbaseClient.start_link ["BTC-USD"]
{:ok, #PID<0.369.0>}
unhandled message: %{
  "channels" => [%{"name" => "ticker", "product_ids" => ["BTC-USD"]}],
  "type" => "subscriptions"
}
ticker: %{
  "best_ask" => "23410.72",
  "best_bid" => "23401.40",
  "high_24h" => "23501.08",
  "last_size" => "0.04048114",
  "low_24h" => "22600",
  "open_24h" => "23483.23",
  "price" => "23410.72",
  "product_id" => "BTC-USD",
  "sequence" => 18997474102,
  "side" => "buy",
  "time" => "2020-12-24T20:28:09.149599Z",
  "trade_id" => 115730226,
  "type" => "ticker",
  "volume_24h" => "18585.64139884",
  "volume_30d" => "582835.19271350"
}
```

Connection loss
```elixir
iex -S mix
Erlang/OTP 23 [erts-11.1.4] [source] [64-bit] [smp:12:12] [ds:12:12:10] [async-threads:1] [hipe]

Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
iex[1]> {:ok, conn} = :gun.open('ws-feed.pro.coinbase.com', 443, %{protocols: [:http]})
{:ok, #PID<0.369.0>}
iex[2]> flush
{:gun_up, #PID<0.369.0>, :http}
{:gun_down, #PID<0.369.0>, :http, :closed, [], []}
:ok
iex[3]> :gun.ws_upgrade(conn, "/")
#Reference<0.3449930143.794820615.96767>
iex[4]> flush
{:gun_down, #PID<0.369.0>, :http, :closed, [], []}
{:gun_up, #PID<0.369.0>, :http}
{:gun_upgrade, #PID<0.369.0>, #Reference<0.3449930143.794820615.96767>,
 ["websocket"],
 [
   {"date", "Thu, 24 Dec 2020 21:02:57 GMT"},
   {"connection", "upgrade"},
   {"set-cookie",
    "__cfduid=d8e69eb94b263c55fa0267a2621f5b4d91608843777; expires=Sat, 23-Jan-21 21:02:57 GMT; path=/; domain=.pro.coinbase.com; HttpOnly; SameSite=Lax"},
   {"upgrade", "websocket"},
   {"sec-websocket-accept", "aUAGwBQoMgEraHwBMSGs+wvxjiQ="},
   {"cf-cache-status", "DYNAMIC"},
   {"cf-request-id", "073828ec9e0000f66b7c8ec000000001"},
   {"expect-ct",
    "max-age=604800, report-uri=\"https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct\""},
   {"server", "cloudflare"},
   {"cf-ray", "606d440d69d6f66b-GRU"}
 ]}
:ok
```

```elixir
iex -S mix
Erlang/OTP 23 [erts-11.1.4] [source] [64-bit] [smp:12:12] [ds:12:12:10] [async-threads:1] [hipe]

Compiling 1 file (.ex)
warning: function subscribe/1 is unused
  lib/exchanges/coinbase_client.ex:57

warning: function subscription_frames/1 is unused
  lib/exchanges/coinbase_client.ex:63

Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
iex[1]> Poeticoins.Exchanges.CoinbaseClient.start_link(["BTC-USD"])
{:ok, #PID<0.381.0>}
iex[2]> [error] GenServer #PID<0.381.0> terminating
** (FunctionClauseError) no function clause matching in Poeticoins.Exchanges.CoinbaseClient.handle_info/2
    (poeticoins 0.1.0) lib/exchanges/coinbase_client.ex:29: Poeticoins.Exchanges.CoinbaseClient.handle_info({:gun_down, #PID<0.382.0>, :ws, :closed, [], []}, %{conn: #PID<0.382.0>, currency_pairs: ["BTC-USD"]})
    (stdlib 3.14) gen_server.erl:689: :gen_server.try_dispatch/4
    (stdlib 3.14) gen_server.erl:765: :gen_server.handle_msg/6
    (stdlib 3.14) proc_lib.erl:226: :proc_lib.init_p_do_apply/3
Last message: {:gun_down, #PID<0.382.0>, :ws, :closed, [], []}
State: %{conn: #PID<0.382.0>, currency_pairs: ["BTC-USD"]}
** (EXIT from #PID<0.373.0>) shell process exited with reason: an exception was raised:
    ** (FunctionClauseError) no function clause matching in Poeticoins.Exchanges.CoinbaseClient.handle_info/2
        (poeticoins 0.1.0) lib/exchanges/coinbase_client.ex:29: Poeticoins.Exchanges.CoinbaseClient.handle_info({:gun_down, #PID<0.382.0>, :ws, :closed, [], []}, %{conn: #PID<0.382.0>, currency_pairs: ["BTC-USD"]})
        (stdlib 3.14) gen_server.erl:689: :gen_server.try_dispatch/4
        (stdlib 3.14) gen_server.erl:765: :gen_server.handle_msg/6
        (stdlib 3.14) proc_lib.erl:226: :proc_lib.init_p_do_apply/3

Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
```

Connection process
```elixir
iex -S mix
Erlang/OTP 23 [erts-11.1.4] [source] [64-bit] [smp:12:12] [ds:12:12:10] [async-threads:1] [hipe]

Compiling 1 file (.ex)
Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
iex[1]> {:ok, conn} = :gun.open('invalid-server', 443, %{protocols: [:http]})
{:ok, #PID<0.382.0>}
iex[2]> Process.alive?(conn)
true
iex[3]> Process.alive?(conn)
false
iex[4]> {:ok, conn} = :gun.open('invalid-server', 443, %{protocols: [:http]})
{:ok, #PID<0.393.0>}
iex[5]> Process.alive?(conn)
true
iex[6]> Process.monitor(conn)
#Reference<0.924939318.3753902081.115760>
iex[7]> Process.alive?(conn)
false
iex[8]> flush
{:DOWN, #Reference<0.924939318.3753902081.115760>, :process, #PID<0.393.0>,
 {:shutdown, :nxdomain}}
:ok
```
