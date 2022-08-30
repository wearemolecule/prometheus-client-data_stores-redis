# Prometheus::Client::DataStores::Redis

This project extracts the Redis data store from [gocardless/prometheus-client-ruby-data-stores-experiments](https://github.com/gocardless/prometheus-client-ruby-data-stores-experiments) and packages it as a gem.
## Thanks to [GoCardless](https://github.com/gocardless)

Thanks to GoCardless for the original experimentation on the various kinds of data stores for prometheus-client.

## Rationale for separating Redis

The original project had other data stores that has their own dependencies. Some of which were quite limiting when it comes to Ruby 3. So we are taking the approach of separating the Redis datastore, which can be run in Ruby 3.


## Redis

This is a store that keeps all its data in Redis.

The basic idea behind using Redis is that what we need to solve the multi-process problem 
is a shared area in memory that we can access in a controlled, safe, concurrent manner, 
and that's precisely what Redis is.

It's important to note that for this to work well, each server would need their own local 
Redis server (unlike the usual configuration where there's a shared Redis server for all 
the App servers). Not only we don't want to share data between servers, this store can
only be performant if Redis is accessed through `localhost`. If we're involving an actual 
network, performance will tank to a probably unusable level.

There are a few more caveats. We recommend you read the extensive notes at the top of the 
[`Redis` class](https://github.com/wearemolecule/prometheus-client-data_stores-redis/blob/main/lib/prometheus/client/data_stores/redis.rb) before running this in production.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'prometheus-client-data_stores-redis'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install prometheus-client-data_stores-redis

## Usage

```rb
  pool = ConnectionPool.new(size: 5, timeout: 5) { Redis.new(host: "localhost", port: 6379) }
  Prometheus::Client.config.data_store = Prometheus::Client::DataStores::Redis.new(connection_pool: pool)
  registry = Prometheus::Client.registry
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/wearemolecule/prometheus-client-data_stores-redis. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/wearemolecule/prometheus-client-data_stores-redis/blob/main/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://github.com/wearemolecule/prometheus-client-data_stores-redis/blob/main/LICENSE.txt).
