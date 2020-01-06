# SmartCore::Container &middot; [![Gem Version](https://badge.fury.io/rb/smart_container.svg)](https://badge.fury.io/rb/smart_container) [![Build Status](https://travis-ci.org/smart-rb/smart_container.svg?branch=master)](https://travis-ci.org/smart-rb/smart_container)

Thread-safe semanticaly-defined IoC/DI Container.

---

## Installation

```ruby
gem 'smart_container'
```

```shell
bundle install
# --- or ---
gem install smart_container
```

```ruby
require 'smart_core/container'
```

---

## Synopsis (demo)

- creation:

```ruby
class Container < SmartCore::Container
  namespace(:database) do # support for namespaces
    register(:resolver, memoize: true) { SomeDatabaseResolver.new } # dependency registration

    namespace(:cache) do # support for nested naespaces
      register(:memcached, memoize: true) { MemcachedClient.new }
      register(:redis, memoize: true) { RedisClient.new }
    end
  end

  # root dependencies
  register(:logger, memoize: true) { Logger.new(STDOUT) }

  # dependencies are not memoized by default (memoize: false)
  register(:random) { rand(1000) }
end
```

- instantiation and resolving:

```ruby
container = Container.new # create container instance
```

```ruby
container['database.resolver'] # => #<SomeDatabaseResolver:0x00007f0f0f1d6332>
container['database.cache.redis'] # => #<RedisClient:0x00007f0f0f1d0158>
container['logger'] # => #<Logger:0x00007f5f0f2f0158>

container.resolve('logger') # #resolve(path) is an alias for #[](path)

# non-memoized dependency
container['random'] # => 352
container['random'] # => 57

# trying to resolve a namespace as dependency
container['database'] # => SmartCore::Container::ResolvingError

# but you can fetch any depenendency type (internal containers and values) via #fetch
container.fetch('database') # => SmartCore::Container (nested container)
container.fetch('database.resolver') # => #<SomeDatabaseResolver:0x00007f0f0f1d6332>
```

- keys:

```ruby
# get dependnecy keys (only dependencies)
container.keys
# => result:
[
  'database.resolver',
  'database.cache.memcached',
  'database.cache.redis',
  'logger',
  'random'
]
```
```ruby
# get all keys (namespaces and dependencies)
container.keys(all_variants: true)
# => result:
[
  'database', # namespace
  'database.resolver',
  'database.cache', # namespace
  'database.cache.memcached',
  'database.cache.redis',
  'logger',
  'random'
]
```

---

## Contributing

- Fork it ( https://github.com/smart-rb/smart_container/fork )
- Create your feature branch (`git checkout -b feature/my-new-feature`)
- Commit your changes (`git commit -am '[feature_context] Add some feature'`)
- Push to the branch (`git push origin feature/my-new-feature`)
- Create new Pull Request

## License

Released under MIT License.

## Authors

[Rustam Ibragimov](https://github.com/0exp)
