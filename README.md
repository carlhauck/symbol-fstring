# symbol-fstring

`symbol-fstring` is a Ruby extension that provide access to symbols internal string representations.

## Why?

In Ruby many APIs tend to accept symbols, but they regularly convert them to strings internally. The typical example is
`ActiveSupport::HashWithIndifferentAccess`, but there are plenty more.

The problem with this is that `Symbol#to_s` creates a new string every time it is invoked, and since it often happens
in hotspots, it causes a lot of work for the garbage collector, and causes many identical strings to be kept in memory.

There was [an attempt to make `Symbol#to_s` return its internal fstring for Ruby 2.7](https://bugs.ruby-lang.org/issues/16150),
but unfortunately it got reverted. Instead, [Ruby 3.0 should have `Symbol#name`](https://github.com/ruby/ruby/commit/eb67c603ca7e435181684857e650b4633fda5bb6).

This gem backports the `Symbol#name` method for older rubies, and optionally allows `Symbol#to_s` to be replaced with `Symbol#name`.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'symbol-fstring'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install symbol-fstring

## Usage

`FString` can be used in two ways. 

### `Symbol.name`

By default this gem backports the Ruby 3.0 `Symbol#name` method.

### `FString.patch_symbol!`

If your application and your dependencies are compatible with it, you can change `Symbol#to_s` behavior globally with:

```ruby
FString.patch_symbol!
```

Or you can also add it this way in your Gemfile:

```ruby
gem 'symbol-fstring', require: 'fstring/all'
```

### Benchmark

From `benchmark/symbol-to_s`

```
  Symbol#to_s (orig)     11.748M (± 1.7%) i/s -     58.786M in   5.005571s
 Symbol#name (patch)     18.067M (± 1.9%) i/s -     90.510M in   5.011549s
```

But there's also a reduced pressure on the garbage collector.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/Shopify/symbol-fstring.

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
