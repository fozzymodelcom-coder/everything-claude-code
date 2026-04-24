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
# Ruby / Rails Coding Style

> This file extends [common/coding-style.md](../common/coding-style.md) with Ruby and Rails specific content.

## Standards

- Follow the **[Ruby Style Guide](https://rubystyle.guide/)** and **[Rails Style Guide](https://rails.rubystyle.guide/)**
- 2-space indentation, no tabs
- `snake_case` for methods, variables, files; `CamelCase` for classes/modules; `SCREAMING_SNAKE_CASE` for constants
- Predicate methods end in `?`; mutating / "bang" methods end in `!`
- Prefer double-quoted strings; use single quotes only when interpolation is not needed and the team prefers them
- Prefer symbols over strings for hash keys and identifiers

## Formatting

- **RuboCop** is the canonical linter/formatter (auto-fix with `rubocop -A`)
- Use the `rubocop-rails`, `rubocop-performance`, and `rubocop-rspec` extensions on Rails + RSpec projects
- **erb_lint** for `.erb` view templates
- Commit a `.rubocop.yml` that inherits from `rubocop-rails-omakase` or the project's chosen base

## Rails Conventions

- **Convention over Configuration** — don't rename or relocate Rails defaults without a strong reason
- Fat models are preferred to fat controllers, but prefer extracted **service objects / POROs** to bloated models (see `patterns.md`)
- Keep controller actions to the seven RESTful defaults; add nested resources instead of custom actions when possible
- Use **strong parameters** in every controller (`params.require(:x).permit(...)`)

## Method Size

- Aim for Sandi Metz's rules as a default: methods ≤ 5 lines, classes ≤ 100 lines, 4 parameters max per method

## Reference

The [Rails guides](https://guides.rubyonrails.org) are the authoritative source for framework conventions; defer to them over older blog posts.
