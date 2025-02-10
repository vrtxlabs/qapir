# Demo

## Prerequisites
Please, refer to [Installation](../README.md) before proceeding with the demo.

## Step 1: Start the demo-app
```
qapir dempo-app
```

## Step 2: Clone this repository and `cd` to `.qapir`
Execute all following commands in a separate terminal-session.
```
cd .qapir
```

## Step 3: Run an example test-suite
Execute all tests defined in `.qapir/suites/test_suite.yml` using `staging` environment variables from `.qapir/environments/staging.yml`.
```
qapir run suite -f test_suite.yml -e staging.yml
```

## Step 4: Review test execution report
After the tests are executed, open a generated `html` test-report in `.qapir/reports` directory.

## What is the demo-app?

Together with a test-runner, `qapir` is supplied with a simple REST-API service that is intended to serve as a
playground for
the new users.

The demo-app exposes the following API-endpoints:

1. `GET http://localhost:7070/status?value={desired_http_status}`, where `value` is any http-status: 200, 404, 503, etc.
   Responds with an empty body and desired http-status.


2. `POST http://localhost:7070/jobs` that accepts payload `{"name": "your_job_name_string"}`. It registers a new job and
   gives it status `in_progress`. After 10 seconds a started job changes status to `done`.


3. `GET http://localhost:7070/jobs?name={your_job_name}`, where `name` is your job-name. Responds with a list of jobs
   that have a matching name.


4. `DELETE http://localhost:7070/jobs?name={your_job_name}`, where `name` is your job-name. Deletes all jobs with a
   given name and responds with http-status 204.


5. `GET http://localhost:7070/types` responds with a payload that contains entities of various types.
