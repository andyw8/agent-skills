---
name: configure-sqids
description: This skill should be used when the user asks to "configure Sqids", "add Sqids to Rails", "add the sqids gem", "add short URL-safe IDs", "generate YouTube-like IDs", "replace numeric IDs with opaque IDs", "add a Sqidable concern", or "use find_by_sqid" in a Rails application.
---

# Configure Sqids in Rails

[Sqids](https://sqids.org/ruby) generates short, URL-safe IDs from numbers,
similar to YouTube IDs. This skill configures a shared `SqidsWrapper` singleton
and a `Sqidable` concern so models expose opaque IDs in URLs while their
integer primary keys stay intact in the database.

## When to use

- Add Sqids to a Rails app so URLs show short, opaque IDs instead of
  sequential integers.
- Change `to_param` so routes and path helpers automatically use sqids.

## Prerequisites

- A Rails application.

## Setup Steps

### Step 1: Add the gem

```bash
bundle add sqids
```

### Step 2: Create the SqidsWrapper initializer

`config/initializers/sqids.rb`:

```ruby
# Sqids configuration - https://sqids.org/ruby
# Sqids generates short, URL-safe IDs from numbers. Running Sqids.new on every
# call adds overhead, so build a single shared instance here and reuse it.
SqidsWrapper = Sqids.new(min_length: 8)
```

Calling `Sqids.new` on every use adds overhead, so the shared `SqidsWrapper`
singleton is built once here. `min_length: 8` guarantees IDs of at least 8
characters.

### Step 3: Create the Sqidable concern

`app/models/concerns/sqidable.rb`:

```ruby
module Sqidable
  extend ActiveSupport::Concern

  class_methods do
    def find_by_sqid(sqid)
      ids = SqidsWrapper.decode(sqid.to_s)
      ids.empty? ? nil : find_by(id: ids.first)
    end
  end

  def to_param
    SqidsWrapper.encode([id])
  end
end
```

- `to_param` makes Rails automatically use the sqid in generated URLs and
  path helpers.
- `find_by_sqid` decodes the sqid then looks up by id. It returns `nil` for
  invalid input, mirroring `find_by` semantics.

### Step 4: Include Sqidable in models

Add `include Sqidable` to each model that should use sqids:

```ruby
class List < ApplicationRecord
  include Sqidable

  validates :name, presence: true
end
```

### Step 5: Update controllers

Replace `find` (including with `.expect`) with `find_by_sqid` in controllers
that reference these models:

```ruby
@list = Current.user.lists.find_by_sqid(params[:id])
```

## Notes

- Exclude models where changing `to_param` would break behavior. In Jumpstart
  Pro, `User` and `Account` are excluded because changing them would break
  authentication and user-facing links.
- `find_by_sqid` returns `nil` instead of raising for invalid sqids, matching
  `find_by`. The concern does not define a bang (`find_by_sqid!`) variant.
- Adding the concern does not rewrite existing URLs; only responses rendered
  after the change use sqids.

## Verification

```bash
bin/rails runner 'puts SqidsWrapper.encode([123])'
bin/rails runner 'puts SqidsWrapper.decode(SqidsWrapper.encode([123])).inspect'
```

Confirm URL-safe output and a working encode/decode round trip, then run the
app's test suite (e.g. `bin/rails test`). Cover:

- encode/decode round trip and that IDs match `\A[a-zA-Z0-9]+\z`
- `to_param` returns the expected sqid
- `find_by_sqid` finds the record and returns `nil` for invalid input

## References

- sqids-ruby gem: https://github.com/sqids/sqids-ruby
- Docs: https://sqids.org/ruby