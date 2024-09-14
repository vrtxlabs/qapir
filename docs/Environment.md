# Environments

An environment file is used to store environment-specific attributes, such as a Base URL or IDs.
Any environment variable defined in an environment file is available to all tests. 
Tests can also add new environment variables and update existing ones at runtime. 
Note that `qapir` does not save new or updated environment variables back to the environment files; changes are only effective during the test run.

## Defining a new `qapir` environment
```
environment_variables:
    staging_url: "http://127.0.0.1:7070"
    string_variable: "string_value"
    number_variable: 123
    bool_variable: true
```

## Accessing `qapir` environment variables
```
${env.staging_url}
${env.string_variable}
${env.number_variable}
${env.bool_variable}
```
