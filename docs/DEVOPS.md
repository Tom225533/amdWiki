# DevOps Infrastructure Documentation

**amdWiki** - DevOps Tools, Processes, and Automation

---

## Table of Contents

1. [Overview](#overview)
2. [Development Tools](#development-tools)
3. [Testing Infrastructure](#testing-infrastructure)
4. [Build & Deployment](#build--deployment)
5. [Monitoring & Logging](#monitoring--logging)
6. [Security & Validation](#security--validation)
7. [CI/CD Pipeline](#cicd-pipeline)
8. [Code Quality](#code-quality)
9. [Dependency Management](#dependency-management)
10. [Documentation](#documentation)
11. [Recommended Integrations](#recommended-integrations)

---

## Overview

amdWiki follows a **modular manager pattern** with comprehensive DevOps tooling for automated testing, cross-platform compatibility, logging, and security validation. This document inventories all existing DevOps infrastructure and proposes future integrations.

**Project Stats:**

- **Lines of Code**: ~991,000
- **Language**: Node.js (JavaScript)
- **Framework**: Express v5.1.0
- **Runtime**: Node.js v22.17.0
- **Architecture**: File-based wiki with manager pattern

---

## Development Tools

### Node.js & npm

- **Version**: Node.js v22.17.0 LTS
- **Package Manager**: npm v10.9.2
- **Purpose**: JavaScript runtime and dependency management
- **Configuration**: `package.json` with 50+ dependencies

### cross-env

- **Version**: 10.1.0
- **Purpose**: Cross-platform environment variable management
- **Usage**: Ensures Windows/Linux/macOS compatibility for `NODE_ENV`
- **Example**:
  ```json
  "test": "cross-env NODE_ENV=test jest --coverage"
  ```

### nodemon (Development)

- **Version**: 3.1.9
- **Purpose**: Automatic server restart on file changes
- **Usage**: Development mode with live reload
- **Command**: `npm run dev`

---

## Testing Infrastructure

### Jest

- **Version**: 30.1.3
- **Purpose**: Unit, integration, and snapshot testing
- **Coverage**: 40%+ code coverage target
- **Configuration** (`package.json`):
  ```json
  "jest": {
    "testEnvironment": "node",
    "collectCoverageFrom": ["src/**/*.js"],
    "coveragePathIgnorePatterns": ["/node_modules/", "/src/tests/", "/src/legacy/"],
    "setupFilesAfterEnv": ["./src/tests/setup.js"]
  }
  ```

### Supertest

- **Version**: 7.1.4
- **Purpose**: HTTP integration testing for Express routes
- **Usage**: Simulates HTTP requests without running server
- **Example**:
  ```javascript
  await request(app)
    .post("/images/upload")
    .attach("image", buffer, "test.jpg")
    .expect(200);
  ```

### Testing Strategy

- **Unit Tests**: Manager logic isolation
- **Integration Tests**: Route handlers with mocked managers
- **Snapshot Tests**: Plugin output verification
- **Test Doubles**: Mocks, stubs, and fakes for external dependencies

### Test Scripts

```bash
npm test              # Run full test suite with coverage
npm run test:watch    # Run tests in watch mode
npm run test:coverage # Generate HTML coverage report
```

### Coverage Reporting

- **Format**: LCOV (HTML + info)
- **Output**: `/coverage/lcov-report/index.html`
- **Threshold**: 40% minimum (targeting 60%+)

---

## Build & Deployment

### npm Scripts

| Script          | Command                                       | Purpose           |
| --------------- | --------------------------------------------- | ----------------- |
| `start`         | `node app.js`                                 | Production server |
| `dev`           | `nodemon app.js`                              | Development mode  |
| `test`          | `cross-env NODE_ENV=test jest --coverage`     | Run tests         |
| `test:watch`    | `cross-env NODE_ENV=test jest --watch`        | Watch mode        |
| `version:show`  | `node -p "require('./package.json').version"` | Show version      |
| `version:patch` | `npm version patch`                           | Increment patch   |
| `version:minor` | `npm version minor`                           | Increment minor   |
| `version:major` | `npm version major`                           | Increment major   |

### Environment Configuration

- **Development**: `config/app-development-config.json`
- **Production**: `config/app-production-config.json`
- **Test**: `config/app-test-config.json`
- **Custom**: `config/app-custom-config.json` (overrides)

### Deployment Process

1. **Version bump**: `npm run version:patch`
2. **Run tests**: `npm test`
3. **Build check**: `npm start` (verify server starts)
4. **Deploy**: Copy to production server, restart service

---

## Monitoring & Logging

### Winston Logger

- **Version**: 3.17.0
- **Purpose**: Structured logging with levels and transports
- **Configuration**: Centralized via `ConfigurationManager`
- **Log Levels**: error, warn, info, http, verbose, debug
- **Transports**:
  - **Console**: Development/debugging
  - **File**: Production persistence (`logs/` directory)

### Log Management

```javascript
const logger = require("./utils/logger");
logger.info("User logged in", { userId: user.id });
logger.error("Upload failed", { error: err.message });
```

### Application Metrics

- **Uptime Plugin**: Tracks server uptime
- **Session Plugin**: Monitors active sessions
- **Total Pages Plugin**: Counts wiki pages

---

## Security & Validation

### Multer (File Upload)

- **Version**: 2.0.2
- **Purpose**: Secure file upload handling
- **Validation**:
  - MIME type checking (image/jpeg, image/png, image/gif, image/webp)
  - File size limit: 5MB
  - Filename sanitization (removes special characters)
  - Authentication requirement

### bcrypt (Password Hashing)

- **Version**: 5.1.1
- **Purpose**: Secure password storage
- **Algorithm**: bcrypt with salt rounds

### express-session

- **Version**: 1.18.2
- **Purpose**: Secure session management
- **Storage**: In-memory (production should use Redis/database)

### Security Headers

- **helmet**: (Not yet implemented - recommended)
- **CORS**: Manual configuration in routes

### Input Validation

- **Filename sanitization**: Regex-based cleaning
- **Path traversal prevention**: No `../` in paths
- **XSS protection**: EJS auto-escaping

---

## CI/CD Pipeline

### GitHub Actions (Current Implementation)

- **Workflow**: `.github/workflows/ci.yml`
- **Triggers**: Push to `main`, Pull Requests
- **Jobs**:
  1. **Test**: Run Jest suite on multiple Node versions
  2. **Coverage**: Upload to Codecov (if configured)
  3. **Lint**: (Not yet implemented - recommended)

### Continuous Integration

- **Node.js Matrix**: Test on v18.x, v20.x, v22.x
- **OS Matrix**: Ubuntu, Windows (optional: macOS)
- **Test Coverage**: Automatically generated and reported
- **Artifacts**: Coverage reports uploaded for review

### Deployment (Manual)

- **No automated deployment**: Production deployment is manual
- **Recommendation**: Add GitHub Actions deployment workflow for staging/production

---

## Code Quality

### ESLint (Recommended - Not Yet Configured)

- **Purpose**: JavaScript linting and style enforcement
- **Config**: Would use Airbnb or Standard style guide
- **Integration**: Run in CI/CD pipeline

### Prettier (Recommended - Not Yet Configured)

- **Purpose**: Code formatting automation
- **Integration**: Pre-commit hooks with Husky

### JSDoc

- **Version**: jsdoc.json configuration present
- **Purpose**: API documentation generation
- **Command**: `npx jsdoc -c jsdoc.json` (if configured)

### Code Metrics

- **CLOC Report**: `cloc-report.csv` tracks lines of code
- **Coverage Reports**: Jest HTML reports in `/coverage`

---

## Dependency Management

### Package Management

- **npm**: Primary package manager
- **Lockfile**: `package-lock.json` ensures reproducible builds
- **Update Strategy**: Manual review before updates

### Dependency Audit

```bash
npm audit              # Check for vulnerabilities
npm audit fix          # Auto-fix vulnerabilities
npm outdated           # Check for outdated packages
```

### Key Dependencies

| Package   | Version | Purpose            |
| --------- | ------- | ------------------ |
| express   | 5.1.0   | Web framework      |
| ejs       | 3.1.10  | Template engine    |
| showdown  | 2.1.0   | Markdown rendering |
| multer    | 2.0.2   | File uploads       |
| bcrypt    | 5.1.1   | Password hashing   |
| winston   | 3.17.0  | Logging            |
| jest      | 30.1.3  | Testing            |
| supertest | 7.1.4   | HTTP testing       |

### Dependabot (Recommended)

- **Purpose**: Automated dependency updates
- **Configuration**: `.github/dependabot.yml` (not yet configured)

---

## Documentation

### Documentation Tools

- **Markdown**: Primary documentation format
- **JSDoc**: API documentation (partial)
- **README.md**: Project overview
- **CONTRIBUTING.md**: Development guidelines

### Documentation Structure

```
docs/
├── architecture/       # System design
├── development/        # Developer guides
├── planning/           # Project roadmap
├── api/                # API documentation
├── managers/           # Manager-specific docs
├── CHANGELOG.md        # Version history
├── DEVOPS.md           # This file
└── README.md           # Docs overview
```

---

## Recommended Integrations

### High Priority

#### 1. Codecov Integration

- **Purpose**: Visual coverage tracking and PR reports
- **Setup**: Add `CODECOV_TOKEN` to GitHub Secrets
- **Benefit**: Coverage trends and PR diffs

#### 2. Dependabot

- **Purpose**: Automated dependency updates
- **Setup**: Create `.github/dependabot.yml`
- **Benefit**: Security patches and updates

#### 3. ESLint + Prettier

- **Purpose**: Code quality and consistency
- **Setup**: Install packages, add `.eslintrc.json`
- **Benefit**: Catch errors before runtime

### Medium Priority

#### 4. Husky (Git Hooks)

- **Purpose**: Pre-commit/pre-push validation
- **Setup**: `npx husky-init`
- **Benefit**: Prevent broken commits

#### 5. Docker

- **Purpose**: Containerized deployment
- **Setup**: Create `Dockerfile` and `docker-compose.yml`
- **Benefit**: Consistent environments

#### 6. Performance Monitoring

- **Purpose**: Identify bottlenecks
- **Tools**: `clinic.js`, `autocannon`, `0x`
- **Benefit**: Optimize slow routes

### Low Priority

#### 7. Sentry (Error Tracking)

- **Purpose**: Production error monitoring
- **Setup**: Add `@sentry/node` package
- **Benefit**: Real-time error alerts

#### 8. Prometheus + Grafana

- **Purpose**: Metrics visualization
- **Setup**: Add `prom-client` package
- **Benefit**: Detailed performance dashboards

---

## Workflow Examples

### Local Development

```bash
# 1. Install dependencies
npm install

# 2. Run in development mode (auto-reload)
npm run dev

# 3. Run tests in watch mode
npm run test:watch

# 4. Check coverage
npm run test:coverage
open coverage/lcov-report/index.html
```

### Pre-Release Checklist

```bash
# 1. Run all tests
npm test

# 2. Check for vulnerabilities
npm audit

# 3. Update version
npm run version:patch

# 4. Verify build
npm start
# Test manually at http://localhost:3000

# 5. Commit and push
git add .
git commit -m "Release v1.2.3"
git push origin main
```

### CI/CD Workflow (GitHub Actions)

```yaml
# Automated on every push/PR:
1. Checkout code
2. Setup Node.js (v18, v20, v22)
3. Install dependencies (npm ci)
4. Run tests (npm test)
5. Upload coverage to Codecov
6. Report status to PR
```

---

## Performance Optimization

### Current Bottlenecks (Identified)

1. **MarkupParser regex**: Complex patterns slow on large pages
2. **File I/O**: Synchronous operations in tight loops
3. **Search indexing**: Lunr.js rebuilds on every page save

### Optimization Strategy

1. **Profiling**: Use `node --prof` or `clinic.js`
2. **Caching**: Implement Redis for parsed content
3. **Async/Await**: Convert sync operations to async
4. **Lazy Loading**: Defer non-critical manager initialization

---

## Conclusion

amdWiki has a **solid DevOps foundation** with comprehensive testing, cross-platform compatibility, structured logging, and security validation. The project is well-positioned to add **CI/CD automation** (GitHub Actions), **coverage tracking** (Codecov), and **dependency management** (Dependabot) for a complete modern DevOps stack.

**Next Steps:**

1. ✅ Implement GitHub Actions CI/CD pipeline
2. ⏳ Add Codecov integration
3. ⏳ Configure Dependabot
4. ⏳ Add ESLint/Prettier for code quality
5. ⏳ Performance profiling and optimization

---

**Document Maintained By**: DevOps Team  
**Last Updated**: 2025-01-22  
**Version**: 1.0.0
