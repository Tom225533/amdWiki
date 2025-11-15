# CI/CD Configuration

This directory contains GitHub Actions workflows and automation configurations for the amdWiki project.

## Files

### `.github/workflows/ci.yml`

Continuous Integration/Continuous Deployment pipeline that runs on every push and pull request.

**Jobs:**

- ✅ **Test**: Runs Jest test suite on Node.js 18.x, 20.x, 22.x (Ubuntu + Windows)
- ✅ **Lint**: Code quality checks (placeholder for ESLint)
- ✅ **Security**: npm audit and dependency vulnerability scanning
- ✅ **Build**: Verifies application starts successfully

**Coverage Reporting:**

- Uploads coverage to Codecov (requires `CODECOV_TOKEN` secret)
- Archives coverage reports as artifacts (30-day retention)

**Triggers:**

- Push to `main` or `develop` branches
- Pull requests targeting `main` or `develop`

### `.github/dependabot.yml`

Automated dependency updates configuration.

**Features:**

- Weekly dependency updates (Mondays at 09:00)
- Separate PRs for production vs development dependencies
- GitHub Actions version updates
- Automatic labeling and assignment

**Setup Required:**

1. Replace `your-github-username` with your GitHub username
2. Enable Dependabot in repository settings

### `.github/PULL_REQUEST_TEMPLATE.md`

Template for standardized pull request descriptions.

### `.github/ISSUE_TEMPLATE/`

Issue templates for bug reports and feature requests.

## Setup Instructions

### 1. Enable GitHub Actions

GitHub Actions should be enabled by default. Verify in repository settings:

- Go to **Settings** → **Actions** → **General**
- Ensure "Allow all actions and reusable workflows" is selected

### 2. Add Codecov Integration (Optional)

To enable coverage reporting:

1. Create account at [codecov.io](https://codecov.io)
2. Add repository to Codecov
3. Copy the upload token
4. Add to GitHub secrets:
   - Go to **Settings** → **Secrets and variables** → **Actions**
   - Click **New repository secret**
   - Name: `CODECOV_TOKEN`
   - Value: Your Codecov token

### 3. Configure Dependabot

1. Edit `.github/dependabot.yml`
2. Replace `your-github-username` with your actual GitHub username
3. Commit and push changes
4. Dependabot will start creating PRs on the next scheduled run

### 4. Branch Protection Rules (Recommended)

Protect `main` branch with status checks:

1. Go to **Settings** → **Branches** → **Add branch protection rule**
2. Branch name pattern: `main`
3. Enable:
   - ✅ Require a pull request before merging
   - ✅ Require status checks to pass before merging
   - ✅ Require branches to be up to date before merging
4. Select required status checks:
   - `Test on Node.js 22.x - ubuntu-latest`
   - `Build Verification`

## Workflow Details

### Test Matrix

The CI pipeline tests across multiple configurations:

| Node.js Version | OS      | Purpose                           |
| --------------- | ------- | --------------------------------- |
| 18.x            | Ubuntu  | LTS compatibility                 |
| 18.x            | Windows | Windows compatibility             |
| 20.x            | Ubuntu  | Current LTS                       |
| 20.x            | Windows | Windows compatibility             |
| 22.x            | Ubuntu  | Latest stable (coverage uploaded) |
| 22.x            | Windows | Windows compatibility             |

### Artifacts

- **Coverage Report**: Uploaded after Node.js 22.x Ubuntu run
- **Retention**: 30 days
- **Access**: Available in workflow run summary

### Performance

- **Average runtime**: ~5-8 minutes (all jobs in parallel)
- **Caching**: npm dependencies cached for faster builds
- **Parallel execution**: 12 jobs run concurrently

## Troubleshooting

### Test Failures

If tests fail in CI but pass locally:

```bash
# Match CI environment
cross-env NODE_ENV=test npm test

# Check Node.js version
node -v  # Should match CI version

# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install
npm test
```

### Coverage Upload Failures

If Codecov upload fails:

1. Verify `CODECOV_TOKEN` is set correctly
2. Check Codecov service status
3. Re-run workflow (temporary network issues)

### Dependabot PRs Not Created

1. Verify repository is not archived
2. Check Dependabot logs in Insights → Dependency graph → Dependabot
3. Ensure `dependabot.yml` syntax is valid

## Local CI Simulation

To run the same checks locally before pushing:

```bash
# Install dependencies (clean install)
npm ci

# Run tests with coverage
npm test

# Security audit
npm audit

# Check for outdated packages
npm outdated

# Verify application starts
npm start
# Press Ctrl+C after verifying startup
```

## Future Enhancements

- [ ] Add ESLint to `lint` job
- [ ] Add Prettier formatting check
- [ ] Implement deployment workflow (staging/production)
- [ ] Add Docker build and publish
- [ ] Performance benchmarking in CI
- [ ] Visual regression testing with Percy/Chromatic
- [ ] Automated changelog generation

## Badges

Add these badges to your `README.md`:

```markdown
[![CI/CD Pipeline](https://github.com/YOUR_USERNAME/amdWiki/actions/workflows/ci.yml/badge.svg)](https://github.com/YOUR_USERNAME/amdWiki/actions/workflows/ci.yml)
[![codecov](https://codecov.io/gh/YOUR_USERNAME/amdWiki/branch/main/graph/badge.svg)](https://codecov.io/gh/YOUR_USERNAME/amdWiki)
[![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)](https://nodejs.org)
```

Replace `YOUR_USERNAME` with your GitHub username.

---

**Maintained By**: DevOps Team  
**Last Updated**: 2025-01-22  
**Version**: 1.0.0
