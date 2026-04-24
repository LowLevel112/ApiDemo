# ApiDemo - API Testing with Postman + Newman

This project is a comprehensive API testing setup using Postman collection + Newman + GitHub Actions.

Public API used: https://dummyjson.com (no API key required).

## Folder structure

`	ext
ApiDemo/
├── collections/
│   └── reqres_collection.json    # API collection with tests
├── environment/
│   ├── reqres_environment.json    # Default environment
│   ├── dev_environment.json      # Development environment
│   ├── staging_environment.json  # Staging environment
│   └── prod_environment.json     # Production environment
├── reports/
│   ├── .gitkeep
│   └── junit.xml                  # Test results
├── .github/
│   └── workflows/
│       └── newman.yml             # CI/CD pipeline
├── package.json                   # NPM scripts
└── README.md
`

## Endpoints covered

### Positive Tests
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /users?limit=5 | Get list of users |
| POST | /auth/login | Login to get access token |
| GET | /auth/me | Get current user (requires token) |
| PUT | /users/2 | Update user |
| DELETE | /users/2 | Delete user |

### Negative Tests
| Method | Endpoint | Expected Status |
|--------|----------|-----------------|
| POST | /auth/login (invalid credentials) | 400 |
| GET | /users?limit=abc (invalid limit) | 400 |
| GET | /users/999999 (not found) | 404 |

## Test validations

Each request includes:
- ✅ Status code validation
- ✅ Response time < 2s
- ✅ Content-Type header check
- ✅ Response schema validation
- ✅ Token management (pm.environment.set)

## Install and run locally

### Option 1: Using NPM (recommended)

1. Install Node.js (18+ recommended)
2. Install dependencies:

`ash
npm install
`

3. Run tests:

`ash
# Basic test
npm test

# CI mode (with --bail flag, stops on first failure)
npm run test:ci

# With specific environment
npm run test:dev
npm run test:staging
npm run test:prod
`

### Option 2: Using Newman directly

`ash
# Install Newman globally
npm install -g newman

# Run tests
newman run collections/reqres_collection.json -e environment/reqres_environment.json --reporters junit,cli

# CI mode
newman run collections/reqres_collection.json -e environment/reqres_environment.json --bail --reporters junit --reporter-junit-export reports/junit.xml
`

## NPM Scripts

| Script | Description |
|--------|-------------|
| 
pm test | Run basic tests with CLI output |
| 
pm run test:ci | Run tests with --bail flag, export JUnit report |
| 
pm run test:dev | Run tests with dev environment |
| 
pm run test:staging | Run tests with staging environment |
| 
pm run test:prod | Run tests with prod environment |
| 
pm run test:report | Run tests with HTML and JUnit reports |

## GitHub Actions

Workflow file: .github/workflows/newman.yml

### Triggers
- Push to main branch
- Pull request to main branch
- Manual dispatch (select environment)

### Matrix Testing
The workflow runs tests against all environments (dev, staging, prod) in parallel.

### Outputs
- JUnit XML reports: 
eports/junit-{environment}.xml
- Artifacts uploaded for each environment

## Test Results

`
┌─────────────────────────┬────────────────────┬────────────────────┐
│                         │           executed │             failed │
├─────────────────────────┼────────────────────┼────────────────────┤
│              iterations │                  1 │                  0 │
├─────────────────────────┼────────────────────┼────────────────────┤
│                requests │                  8 │                  0 │
├─────────────────────────┼────────────────────┼────────────────────┤
│            test-scripts │                   8│                  0 │
├─────────────────────────┼────────────────────┼────────────────────┤
│      assertions         │                  26│                  0 │
├─────────────────────────┴────────────────────┴────────────────────┤
│ total run duration: ~4s                                           │
│ average response time: ~360ms                                     │
└───────────────────────────────────────────────────────────────────┘
`

## Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| aseUrl | API base URL | https://dummyjson.com |
| loginUsername | Username for login | emilys |
| loginPassword | Password for login | emilyspass |
| uthToken | Token from login (auto-set) | *(set dynamically)* |
| environmentName | Environment name | dev/staging/prod |
| expectedResponseTime | Max response time (ms) | 2000 |

## Best Practices

1. **Always use --bail flag in CI** to fail fast on errors
2. **Separate environments** for dev/staging/prod
3. **Include negative tests** to validate error handling
4. **Check response time** to detect performance issues
5. **Validate Content-Type** to ensure proper API responses
6. **Use environment variables** for configuration management
