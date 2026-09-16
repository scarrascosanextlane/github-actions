# GitHub Actions demo: per-project dev -> qa chains

Simplified copy of the deployment mechanism of `cloud-cvc-unleash-web`. Nothing is deployed;
every step echoes.

```
ci.yml                 one matrix job, one entry per project (cvc, uac)
  -> deploy-project.yml   development, then qa (needs: development), per project
       -> deploy.yml         the "steps": bound to environment <stage>-<project>, reads vars.TARGET
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

- **Happy path:** run "Deploy (demo)" from the Actions tab with no inputs. The graph shows
  `Deploy cvc` and `Deploy uac`; each expands into `development -> QA`.
- **Failure isolation:** run it with `fail_dev_of = cvc`. `Deploy cvc / development` fails,
  `Deploy cvc / QA` is skipped, and `Deploy uac` runs both stages untouched.
- **Version pin:** set `version` to any string; it is echoed by every leg.
