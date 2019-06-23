# default_cassette_options

The `default_cassette_options` configuration option takes a hash
  that provides defaults for each cassette you use.  Any cassette
  can override the defaults as well as set additional options.

  The `:match_requests_on` option defaults to `[:method, :uri]` when
  it has not been set.

  The `:record` option defaults to `:once` when it has not been set.

## Background ()

_Given_ a file named "vcr_setup.rb" with:

```
require 'vcr'

VCR.configure do |c|
  c.default_cassette_options = { :record => :new_episodes, :erb => true }

  # not important for this example, but must be set to something
  c.hook_into :webmock
  c.cassette_library_dir = 'cassettes'
end
```

## cassettes get default values from configured `default_cassette_options`

_Given_ a file named "default_cassette_options.rb" with:

```
require 'vcr_setup.rb'

VCR.use_cassette('example') do
  puts "Record Mode: #{VCR.current_cassette.record_mode}"
  puts "ERB: #{VCR.current_cassette.erb}"
end
```

_When_ I run `ruby default_cassette_options.rb`

_Then_ the output should contain:

```
Record Mode: new_episodes
ERB: true
```

## `:match_requests_on` defaults to `[:method, :uri]` when it has not been set

_Given_ a file named "default_cassette_options.rb" with:

```
require 'vcr_setup.rb'

VCR.use_cassette('example') do
  puts "Match Requests On: #{VCR.current_cassette.match_requests_on.inspect}"
end
```

_When_ I run `ruby default_cassette_options.rb`

_Then_ the output should contain "Match Requests On: [:method, :uri]".

## `:record` defaults to `:once` when it has not been set

_Given_ a file named "default_record_mode.rb" with:

```
require 'vcr'

VCR.configure do |c|
  # not important for this example, but must be set to something
  c.hook_into :webmock
  c.cassette_library_dir = 'cassettes'
end

VCR.use_cassette('example') do
  puts "Record mode: #{VCR.current_cassette.record_mode.inspect}"
end
```

_When_ I run `ruby default_record_mode.rb`

_Then_ the output should contain "Record mode: :once".

## cassettes can set their own options

_Given_ a file named "default_cassette_options.rb" with:

```
require 'vcr_setup.rb'

VCR.use_cassette('example', :re_record_interval => 10000) do
  puts "Re-record Interval: #{VCR.current_cassette.re_record_interval}"
end
```

_When_ I run `ruby default_cassette_options.rb`

_Then_ the output should contain "Re-record Interval: 10000".

## cassettes can override default options

_Given_ a file named "default_cassette_options.rb" with:

```
require 'vcr_setup.rb'

VCR.use_cassette('example', :record => :none, :erb => false) do
  puts "Record Mode: #{VCR.current_cassette.record_mode}"
  puts "ERB: #{VCR.current_cassette.erb}"
end
```

_When_ I run `ruby default_cassette_options.rb`

_Then_ the output should contain:

```
Record Mode: none
ERB: false
```
