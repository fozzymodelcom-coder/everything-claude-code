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
# Ruby / Rails Testing

> This file extends [common/testing.md](../common/testing.md) with Ruby and Rails specific content.

## Framework

Use whichever framework the project already uses — do not mix:

- **RSpec** (via `rspec-rails`) — dominant in the community, expressive matchers
- **Minitest** — the Rails default, leaner, ships with Ruby

If starting fresh, RSpec is the more common community choice for Rails apps.

## Test Layout

### RSpec
```
spec/
├── models/
├── requests/      # preferred over controller specs
├── system/        # full-stack browser tests (Capybara)
├── services/
├── jobs/
├── mailers/
└── support/       # shared helpers, included via spec_helper
```

### Minitest
```
test/
├── models/
├── controllers/
├── integration/
├── system/
└── test_helper.rb
```

## Fixtures vs Factories

- **FactoryBot** for flexible, composable test data (`create(:user, :admin)`)
- Rails **fixtures** are fast and good for reference data but clunky for complex relationships
- Prefer factories for most tests; reserve fixtures for seed/reference data

## System Tests

- Use **Capybara** with a headless driver (cuprite for Chrome DevTools Protocol, or selenium with headless Chrome)
- Avoid `sleep` — use Capybara's built-in waiting matchers (`have_text`, `have_css`)
- Reset state between tests (DatabaseCleaner for non-transactional drivers; transactional fixtures otherwise)

## Coverage

- **SimpleCov** with a minimum threshold in `spec_helper.rb` / `test_helper.rb`:
  ```ruby
  SimpleCov.start 'rails' do
    minimum_coverage 80
  end
  ```

## Speed

- Parallelize: `parallel_tests` (RSpec) or `rails test -p` (Minitest)
- Use `bootsnap` (default in new Rails apps) and `spring` (optional)
- Keep the unit / request / system test pyramid — system tests are slow, write few but meaningful

## Test Doubles

- Prefer real objects over mocks when cheap; mock only at external boundaries (HTTP, mail, payments)
- **WebMock** + **VCR** for HTTP stubbing
- Avoid `allow_any_instance_of` — it's a code smell pointing at tight coupling
