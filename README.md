# deployment-pipeline

Shared reusable GitHub Actions workflows for NPM packages.

## Available Workflows

### test.yml
Runs `npm ci`, `npm run build`, and `npm test` across a matrix of Node.js versions.

**Inputs:**
| Input | Default | Description |
|-------|---------|-------------|
| `node-versions` | `'["18", "20", "22", "24"]'` | JSON array of Node.js versions |

### sonar.yml
Runs tests with coverage and performs a SonarQube scan.

**Inputs:**
| Input | Default | Description |
|-------|---------|-------------|
| `node-version` | `'22'` | Node.js version for the scan |

**Secrets:**
| Secret | Description |
|--------|-------------|
| `SONAR_TOKEN` | SonarQube/SonarCloud authentication token |

### codeql.yml
Runs GitHub CodeQL security analysis.

**Inputs:**
| Input | Default | Description |
|-------|---------|-------------|
| `language` | `'javascript'` | Language to analyze |

### publish-npm.yml
Builds and publishes the package to the NPM registry.

**Inputs:**
| Input | Default | Description |
|-------|---------|-------------|
| `node-version` | `'22'` | Node.js version for publishing |

**Secrets:**
| Secret | Description |
|--------|-------------|
| `NPM_TOKEN` | NPM authentication token |

### semantic-release.yml
Runs semantic-release for automated versioning and publishing.

**Inputs:**
| Input | Default | Description |
|-------|---------|-------------|
| `node-version` | `'24'` | Node.js version for release |

**Secrets:**
| Secret | Description |
|--------|-------------|
| `GH_TOKEN` | GitHub token for creating releases |
| `NPM_TOKEN` | NPM authentication token |

## Usage

Reference these workflows from your repository:

```yaml
jobs:
  test:
    uses: scottobert/deployment-pipeline/.github/workflows/test.yml@main

  sonar:
    needs: test
    uses: scottobert/deployment-pipeline/.github/workflows/sonar.yml@main
    secrets:
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

  release:
    needs: [test, sonar]
    if: github.event_name == 'push'
    uses: scottobert/deployment-pipeline/.github/workflows/semantic-release.yml@main
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```
