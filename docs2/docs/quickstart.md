---
slug: /quickstart
sidebar_position: 3
---

# Quickstart

## Prerequisites
Please, refer to [Installation](./installation) before proceeding with the Quickstart.

## Step 1: Start the demo-app
```
qapir dempo-app
```

## Step 2: Clone the repository with demo-tests and `cd` to `.qapir` directory
Execute all following commands in a separate terminal-session.
```
git clone https://github.com/vrtxlabs/qapir.git
cd qapir/.qapir
```

## Step 3: Run an example test-suite
Execute all tests defined in `.qapir/suites/test_suite.yml` using `staging` environment variables from `.qapir/environments/staging.yml`.
```
qapir run suite -f test_suite.yml -e staging.yml
```

## Step 4: Review test execution report
After the tests are executed, open a generated `html` test-report in `.qapir/reports` directory.