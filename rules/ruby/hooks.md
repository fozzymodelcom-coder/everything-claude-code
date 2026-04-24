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
# Ruby / Rails Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Ruby and Rails specific content.

## PostToolUse Hooks

Configure in `~/.claude/settings.json`:

- **rubocop -A**: Auto-format `.rb`, `.rake`, `.gemspec` files after edit
- **erb_lint --autocorrect**: Format `.erb` templates after edit
- **brakeman --quiet --no-pager**: Run Brakeman on touched Rails files (see `security.md`)
- **bundle exec rails runner "Rails.application.eager_load!"**: On changes to `config/` or `app/`, catch load-order / autoload regressions early (optional; slow)

## Warnings

- Flag leftover debugger calls in edited files: `binding.pry`, `binding.irb`, `byebug`, `debugger`, `pry-remote`, `puts`-debugging patterns
- Flag `save` / `update` / `destroy` without `!` in test files where failure should raise
- Flag `SecureRandom.hex` / `SecureRandom.uuid` usage embedded in fixtures (non-deterministic tests)
- Flag raw SQL string interpolation in `where` / `find_by_sql` (see `security.md`)

## Bundler

- On `Gemfile` edits, prompt the user to run `bundle install` — do not auto-install (network-bound, should be explicit)
