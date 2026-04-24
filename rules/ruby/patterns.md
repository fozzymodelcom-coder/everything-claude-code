---
paths:
  - "**/*.rb"
  - "**/*.rake"
  - "**/*.erb"
  - "**/Gemfile"
  - "**/Gemfile.lock"
  - "**/*.gemspec"
  - "**/config.ru"
  - "**/Rakefile"
---
# Ruby / Rails Patterns

> This file extends [common/patterns.md](../common/patterns.md) with Ruby and Rails specific content.

## Service Objects (POROs)

Extract multi-step business logic out of controllers and models:

```ruby
# app/services/users/register.rb
module Users
  class Register
    def self.call(...) = new(...).call

    def initialize(email:, password:)
      @email = email
      @password = password
    end

    def call
      user = User.create!(email: @email, password: @password)
      WelcomeMailer.with(user:).welcome.deliver_later
      user
    end
  end
end
```

Keep one public entry point (`call`) and raise on failure — let the caller decide how to translate exceptions.

## Query Objects & Scopes

Prefer named scopes for reusable queries; extract complex multi-join queries into query objects:

```ruby
class User < ApplicationRecord
  scope :active, -> { where(archived_at: nil) }
  scope :recent, ->(days = 30) { where(created_at: days.days.ago..) }
end
```

## Form Objects

For multi-model forms or complex validation, use a form object backed by `ActiveModel::Model`:

```ruby
class SignupForm
  include ActiveModel::Model
  attr_accessor :email, :password, :company_name
  validates :email, :company_name, presence: true
end
```

## Concerns

Use sparingly — concerns should factor out *genuinely shared* behavior, not be a dumping ground. If a concern is only included in one class, inline it.

## Background Jobs

- Always use `deliver_later` / `perform_later` for anything touching the network (mail, webhooks, third-party APIs)
- Jobs must be idempotent — assume they can be retried
- Pass **IDs, not records** as job arguments (ActiveJob serializes GlobalIDs, but records can become stale)

## ActiveRecord Callbacks

- Avoid `after_save` / `after_commit` hooks that trigger side effects (emails, jobs, external calls) — prefer explicit service objects
- `before_validation` and `before_save` for normalization (stripping, downcasing) are fine

## Naming

- Model files under `app/models/`, plural table names (`users`), singular model names (`User`)
- Controllers are plural (`UsersController`), actions are the seven RESTful verbs
- Avoid "Manager", "Helper", "Util" — name by what the object *does*
