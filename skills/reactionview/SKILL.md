---
name: reactionview
description: Use when installing or configuring ReActionView (https://github.com/marcoroth/reactionview) in a Rails app, or when adding the gem, running its install generator, or writing config/initializers/reactionview.rb.
---

# ReActionView

ReActionView is a drop-in ActionView-compatible ERB engine built on
[Herb::Engine](https://github.com/marcoroth/herb). It processes `.html.herb`
templates natively with HTML-aware rendering, and can optionally intercept all
`.html.erb` templates too.

## When to use

- Add ReActionView to a Rails app for HTML validation, better error feedback,
  and a debug mode.
- Enable/configure ERB interception or native `.herb` template support.

## Installation

```bash
bundle add reactionview
bin/rails generate reactionview:install
```

The generator creates `config/initializers/reactionview.rb`.

## Configuration

Enable ERB interception to process all `.html.erb` templates with `Herb::Engine`:

```ruby
# config/initializers/reactionview.rb
ReActionView.configure do |config|
  # Intercept .html.erb templates and process them with Herb::Engine
  config.intercept_erb = true

  # Enable debug mode in development (adds debug attributes to HTML)
  config.debug_mode = Rails.env.development?

  # Path used for editor "open in editor" links (optional, defaults to Rails.root)
  # config.project_path = ENV.fetch('PROJECT_PATH', Rails.root.to_s)

  # Validation mode (:raise, :overlay, or :none) — defaults to :raise in test, :overlay otherwise
  # config.validation_mode = :overlay

  # How to handle templates that come from gems (:fallback, :skip, or :compile), defaults to :fallback
  # config.external_template_mode = :skip
end
```

### Options

- `intercept_erb` (bool, default `false`) — process all `.html.erb` templates
  with Herb. Enabling this is the broad, app-wide change; leave it off to use
  native `.html.herb` templates only.
- `debug_mode` (bool) — adds debug attributes to the rendered HTML in
  development.
- `project_path` (string) — base path for "open in editor" links.
- `validation_mode` (`:raise`, `:overlay`, `:none`) — how to surface HTML
  validation errors. Defaults to `:raise` in test and `:overlay` otherwise.
- `external_template_mode` (`:fallback`, `:skip`, `:compile`) — handling for
  templates shipped by gems. Defaults to `:fallback` to avoid breaking
  third-party templates.
- `transform_visitors` (array) — custom `Herb::Visitor` instances to process
  templates before compilation.

## Two usage modes

1. **Native `.html.herb` templates** — automatically processed with
   `Herb::Engine`. Best for new templates that want explicit Herb enhancements.
2. **Intercept `.html.erb`** — when `intercept_erb = true`, every existing
   `.html.erb` template is compiled by Herb too. Broad but applies enhanced
   features to the whole app.

## Verification

After configuring, confirm the initializer loaded and interception is on:

```bash
bin/rails runner 'puts ReActionView.config.inspect'
# ReActionView::Config ... @intercept_erb=true ...
```

Run the app's test suite (e.g. `bin/rails test`) to confirm existing
`.html.erb` templates still render correctly with interception enabled.

## References

- GitHub: https://github.com/marcoroth/reactionview
- Website/docs: https://reactionview.dev
