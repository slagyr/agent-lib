---
name: multimethod-seams
description: >
  Decouple a Clojure package with multimethods: core is the interface,
  each :impl is a namespace, load by convention, no lying defaults,
  split substitution sets into separate namespaces. Use when adding a
  backend or :impl, writing cond/case on impl, reviewing a system.*
  package with adapters, or the user mentions OCP, LSP, ISP, DIP,
  defmulti, multimethod seam, or "new backend".
---

# Multimethod Seams

Multimethods are the lightest way to decouple when dispatch is configuration
(`:impl`). They are typically the first measure. Protocols (and deftype) come
later, when you need an instance you can hold and pass. Factory multimethods
that construct those instances stay in the [clojure](../clojure/SKILL.md) skill.

This is a package shape — not a `cond` in one namespace. High-level code
depends on the multimethods, not on `smtp` or `ses` (DIP). Adding an impl
does not edit core (OCP). Each impl of a method honors the same contract
(LSP). Clients that only send do not depend on harness methods (ISP). Impl
state stays in the impl (SRP).

Examples below use outbound email. The same shape applies to any
config-dispatched package.

## When

- New `:impl` / backend
- `cond` / `case` on impl
- A `system.*` (or similar) package growing a second adapter
- Core holds impl state, `:default` no-ops, or harness APIs (`last-message`) next to `send!`

## Layout

```
email/
  core.clj        ; public API + defmultis
  capture.clj     ; harness inbox, if any (tests, reset, demo)
  console.clj     ; one namespace per impl
  memory.clj
  file.clj
  smtp.clj
```

Callers of `send!` require `email.core`.
Callers of `last-message` require `email.capture`.

## Interface lives in one place

Group every `defmulti` together. They are the interface.

```clojure
(defn impl
  [& _]
  (or (:impl (cfg)) :unknown))

(defmulti -load impl)
(defmulti -deliver impl)
```

`impl` is `[& _]` so it can dispatch 0-arg and 1-arg methods.

`-` prefix = impl hook. Public wrappers on core:

```clojure
(defn load!
  []
  (require (symbol (str "myapp.email." (name (impl)))))
  (-load))

(defn send!
  [msg]
  (load!)
  (-deliver (assoc msg :from (from-address))))
```

Load by convention: `require` of `myapp.email.<impl>`. Do not use `requiring-resolve`.

## Every required hook is implemented

If every impl must do it, there is no `:default`. Missing method is the error.

`-load` is required. Each impl implements it and logs when finished (`:email/loaded :impl …`).

Do not write `:unknown` methods. Missing `:impl` stays `:unknown`; `require` of `myapp.email.unknown` fails loudly.

`:default` is only for a truly optional hook. A no-op default on a method some clients depend on is a lie.

## State stays in the impl

Atoms, file seqs, SMTP connections, capture dirs — in the impl namespace, not core.

Core does not know `:dir`. The file impl reads `(:dir (cfg))` itself.

## Two substitution sets, two namespaces

If some impls cannot honor a method, it does not belong on the send interface.

Inbox (`clear!`, `last-message`, `all-messages`) lives on `email.capture`, dispatched on the same `impl`. Only capture impls (`:memory`, `:file`) implement it. Send-only impls (`:console`, `:smtp`) have no methods — asking is a programmer error.

Do not put file-only helpers (`capture-dir`, `clear-file-captures!`) on core.

## Impls log themselves

Core does not log the send. The impl has the message id, filename, or console dump.

## Config is inventory

`:impl`, From, SMTP host live in config. Only the secret is in env.

`send!` clients do not pass `:from` when From is configured inventory. Core overwrites it from the profile.

## Anti-patterns

- `cond` / `case` on impl in core
- `sent*` (or any impl atom) in core
- `:default` that returns nil / `[]` so every impl "has" an inbox
- Custom `:unknown` throw methods
- `requiring-resolve` as a cycle-breaker
- Harness API (`last-message`) on the production send namespace
