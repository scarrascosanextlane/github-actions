# GitHub Actions demo: per-project dev -> qa chains

Simplified copy of the deployment mechanism of `cloud-cvc-unleash-web`. Nothing is deployed;
every step echoes.

```
ci.yml         one pair of jobs per project: dev-<p> -> qa-<p> (needs), no matrix
  -> deploy.yml   the "steps": bound to environment <stage>-<project>, reads vars.TARGET
```

## Setup

Create four GitHub environments, each with one variable `TARGET` (any string):

| Environment | TARGET example |
|-------------|----------------|
| `dev-cvc`   | `cluster-cvc-dev` |
| `qa-cvc`    | `cluster-cvc-qa` |
| `dev-uac`   | `cluster-uac-dev` |
| `qa-uac`    | `cluster-uac-qa` |

A missing environment or variable does not fail: GitHub resolves `vars.TARGET` to an empty
string and the Render step prints `<missing: ...>`.

## Try it

- **Happy path:** run "Deploy (demo)" from the Actions tab with no inputs. The graph shows the
  dev legs in the left column and the QA legs in the right one, each linked to its own project:
  `dev · cvc -> qa · cvc` and `dev · uac -> qa · uac`.
- **Failure isolation:** run it with `fail_dev_of = cvc`. `dev · cvc` fails, `qa · cvc` is
  skipped, and the `uac` pair runs untouched.

Registering a project means copying one dev/qa job pair in `ci.yml` and creating its two
environments. The trade-off against a matrix is that the graph shows every leg as its own node
with its own edge, at the cost of one block per project.
- **Version pin:** set `version` to any string; it is echoed by every leg.
