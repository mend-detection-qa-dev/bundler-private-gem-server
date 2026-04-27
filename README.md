# bundler-private-gem-server

## Feature exercised

Tests a Gemfile that declares two `source` blocks — the default `https://rubygems.org` and a fictional private gem server at `https://gems.example.com` — verifying that Mend correctly parses non-rubygems.org source declarations without failing resolution of the public gems.

## Probe metadata

| Field | Value |
|---|---|
| Pattern | multiple-sources / private-gem-server |
| Agent | generic-bundler |
| Repository | mend-detection-qa/bundler-private-gem-server |
| Ruby | >= 3.2 |
| Generated | 2026-04-27 |

## Gemfile structure

- **Primary source**: `https://rubygems.org` (default, ungrouped)
  - `rack ~> 3.0`
  - `sinatra ~> 4.0`
- **Secondary source**: `https://gems.example.com` (private server, source-scoped block)
  - `acme-internal-auth ~> 1.0` — fictional internal gem, not resolvable from rubygems.org

## Lockfile notes

The `Gemfile.lock` records only the gems that resolve from rubygems.org. `acme-internal-auth` appears in the `DEPENDENCIES` section with the `!` suffix (indicating source-block scoping) but has no `GEM` spec entry because the private server is unreachable. This is intentional — the probe is manifest-only for the private gem.

## Expected dependency tree

The following gems must appear in Mend's detected dependency tree:

| Gem | Version | Source | Direct | Children |
|---|---|---|---|---|
| rack | 3.0.10 | rubygems.org | yes | none |
| sinatra | 4.0.0 | rubygems.org | yes | mustermann, rack-protection, tilt |
| mustermann | 3.0.0 | rubygems.org | no (via sinatra) | ruby2_keywords |
| ruby2_keywords | 0.0.5 | rubygems.org | no (via mustermann) | none |
| rack-protection | 4.0.0 | rubygems.org | no (via sinatra) | none |
| tilt | 2.4.0 | rubygems.org | no (via sinatra) | none |

`acme-internal-auth` is NOT expected in the dependency tree (unresolvable private gem).

## Key detection questions

1. Does Mend parse the Gemfile without erroring on the second `source` block?
2. Does Mend correctly read the lockfile and report all six rubygems.org-resolved gems?
3. Does Mend omit `acme-internal-auth` (absent from lockfile GEM specs) rather than surfacing a phantom entry?
4. Does the `!` suffix on `acme-internal-auth` in `DEPENDENCIES` cause any unexpected behaviour?
