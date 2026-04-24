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
# Ruby / Rails Security

> This file extends [common/security.md](../common/security.md) with Ruby and Rails specific content.

## Secret Management

- **Rails encrypted credentials** are the default for app secrets:
  ```bash
  EDITOR="code --wait" bin/rails credentials:edit --environment production
  ```
- Read with `Rails.application.credentials.dig(:stripe, :secret_key)`
- Never commit `config/master.key` or `config/credentials/*.key` — they must stay out of git
- Use ENV vars for deploy-time config (database URLs, feature flags); use credentials for secrets

## SQL Injection

Always use parameterized queries. Never interpolate user input into SQL strings:

```ruby
# BAD — SQL injection
User.where("email = '#{params[:email]}'")

# GOOD — parameterized
User.where(email: params[:email])
User.where("email = ?", params[:email])
```

The same applies to `find_by_sql`, `order`, and `group` — passing untrusted input to `order` is a common bypass.

## Mass Assignment

- Always use **strong parameters** in controllers: `params.require(:user).permit(:email, :name)`
- Never pass `params` directly to `create` / `update`

## CSRF / Auth

- Keep `protect_from_forgery with: :exception` in `ApplicationController` (default since Rails 5)
- For JSON APIs, either skip CSRF with token-based auth or use `null_session`
- Use `has_secure_password` (bcrypt) for password storage — never roll your own

## Security Scanning

- **Brakeman** for static analysis of Rails-specific vulnerabilities:
  ```bash
  brakeman --quiet --no-pager
  ```
- **bundler-audit** for known CVEs in dependencies:
  ```bash
  bundle exec bundle-audit check --update
  ```
- Run both in CI; block merges on new high-severity findings

## Deserialization

- Never `YAML.load` / `Marshal.load` untrusted input — use `YAML.safe_load` with an explicit permitted-classes list
- `JSON.parse` is safe for untrusted input (won't instantiate arbitrary classes)
