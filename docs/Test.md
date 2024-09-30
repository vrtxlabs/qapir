## Test

A test-file contains a collection of test-steps. Test-steps define 1) an action - http-call, and 2) a set of
validations, e.g. check that expected http-status was received, response contains a header with expected value,
response-payload contains expected data, and so on.

Test-steps are executed sequentially. Every test-step can be retried with a desired interval and retry-limit. If all
attempts of a test-step fail - the test is marked as failed and no further test-steps are executed. That said, same as
individual test-steps, the whole test-scenario can be retried.

In order to perform actions on responses, `qapir` provides a `qtl` syntax (very much resembles `SQL`), which
allows you to:

1. Find data in response-payloads and test-variables via `jsonpath`, then extract it into variables.
2. Perform various types of validation.

The ability to extract data from response-payloads and save it in environment-variables and test-variables
allows `qapir` to pass data from one test-step to others. This in turn allows you to implement complex test-scenarios
that
involve multiple http-calls!

Example of a test-file:

```
name: Test types

test_steps:
  - description: GET /types
    type: http
    url: ${env.url}/types
    method: GET
    expected_http_status: 200
    execute: |
      SELECT
        $.data.number AS ${var.number} AND
        $.data.string AS ${var.string} AND
        $.data.bool AS ${var.bool} AND
        $.data.object AS ${var.object} AND
        $.data.string_array AS ${var.string_array} AND
        $.data.string_array[0] AS ${var.string_array_elem} AND
        $.data.number_array AS ${var.number_array} AND
        $.data.number_array[0] AS ${var.number_array_elem} AND
        $.data.object_array AS ${var.object_array} AND
        $.data.object_array[0] AS ${var.object_array_elem}
      FROM
        ${response.body}
      EXPECT
        ${var.number} -type number AND
        ${var.string} -type string AND
        ${var.bool} -type bool AND
        ${var.object} -type object AND
        ${var.string_array} -type array AND
        ${var.string_array_elem} -type string AND
        ${var.number_array} -type array AND
        ${var.number_array_elem} -type number AND
        ${var.object_array} -type array AND
        ${var.object_array_elem} -type object;
```

### QTL

`qtl` has several reserved keywords that allow you difine logic for data-extraction, variable-assignment and
data-validation. Every `qtl` statement ends with a `;`.

#### Data-extraction

`SELECT` - instructs `qapir` to find data via `jsonpath` in a response-body, response-headers or in a variable.

`AS` - specifies how the data found by `SELECT` should be stored: as a test-variable, or as an environment-variable.

`SELECT_ALL` - same as `SELECT`, but can only be invoked on an array-data for further iterative validation of all
array-elements.

`FROM` - defines data-source, it can be either a response-body, response-headers, or a test/environment-variable.

`AND` - a keyword that allows you to execute multiple data-extractions from the same source.

The following example finds data in response-body by the following `jsonpath` statements: `$.data.number`
and `$.data.string`, then saves found data as `${var.number}` and `${var.string}` test-variables respectively:

```
SELECT 
  $.data.number AS ${var.number} AND
  $.data.string AS ${var.string}
FROM 
  ${response.body};
```

#### Variable-assignment

`SET` - this directive is used to set/update a test/environment-variable outside of `SELECT` statement.

The following example sets two test-variables `${var.string}` and `${var.array}` with values `your_string_value`
and `[1, 2, 3]` respectively:

```
SET
  ${var.string} your_string_value AND
  ${var.array} [1, 2, 3];
```

#### Data-validation

`EXPECT` - performs validation of a single entity.

`EXPECT_ALL` - performs validation of every entity in an array on which this directive is invoked.

`IF_EXISTS` - a directive that instructs `qapir` to perform validation of an entity only if it exists - without this
directive, validation on a null-entity fails.

##### Validation-terms

`-type` - checks whether entity is one of `string, number, bool, array, object`

`-eq` - checks whether entity is equal to another entity

`-neq` - checks whether entity is not equal to another entity

`-lt` - works with numbers only, checks whether entity is less than another entity

`-le` - works with numbers only, checks whether entity is less than or equal another entity

`-gt` - works with numbers only, checks whether entity greater than another entity

`-ge` - works with numbers only, checks whether entity greater than or equal another entity

`-len` - works with strings and arrays, checks whether length of one entity is `<`, `<=`, `>`, `>=` or equal to length of
another entity

`-in` - checks whether entity is part of an array

`-nin` - checks whether entity is not part of an array

`-contains` - works with strings and arrays, checks whether a string contains a given substring, or if an array contains
a given element

`-ncontains` - works with strings and arrays, checks whether a string does not contain a given substring, or if an array
does not contain a given element

`-exists` - checks whether entity exists

`-nexists` - checks whether entity does not exist

#### Array-validation

`qapir` has a directive that simplifies validation of arrays - `EXPECT_ALL`, which works in pair with `SELECT_ALL`

Imagine you need to validate whether all elements of a json-array contain expected values.

Given the following json with job-objects:

```
{
  "data": [
    {
      "name": "job_1",
      "status": "done"
    },
    {
      "name": "job_2",
      "status": "in_progress"
    }
  ]
}
```

Check that every job-object has fields `name` and `status`, that both fields are of type `string`,
and `status` is one of [`in_progress`, `done`]

Here's how to write such statement:

```
SELECT
  $.data AS ${var.data}
FROM
  ${response.body};
  
SELECT_ALL
  $.name AS ${var.iterable_job_name} AND
  $.status AS ${var.iterable_job_status}
FROM
  ${var.data}
EXPECT_ALL
  ${var.iterable_job_name} -type string AND
  ${var.iterable_job_status} -type string AND
  ${var.iterable_job_status} -in [in_progress, done];
```

First, we need to get the array with `SELECT` and save it as `${var.data}`. This step is needed
because `SELECT_ALL` can only be called on an array.

Next, when directive `SELECT_ALL` is executed, `qapir` under the hood creates 2 test-variables with
name `iterable_job_name` and 2 test-variables with name `iterable_job_status`, since there are 2 job-objects.

Finally, `EXPECT_ALL` runs validations on every instance of `iterable_job_name` and `iterable_job_status`.

Alternatively, instead of `EXPECT_ALL` we could use `EXPECT` term and reference every instance of `iterable_job_name`
and `iterable_job_status` by appending `#{index}` to their variable-names.

Although this statement is longer and riskier to use (we might not always know array's length), it is also valid:

```
SELECT
  $.data AS ${var.data}
FROM
  ${response.body};
  
SELECT_ALL
  $.name AS ${var.iterable_job_name} AND
  $.status AS ${var.iterable_job_status}
FROM
  ${var.data}
EXPECT
  ${var.iterable_job_name#0} -type string AND
  ${var.iterable_job_name#1} -type string AND
  ${var.iterable_job_status#0} -type string AND
  ${var.iterable_job_status#1} -type string AND
  ${var.iterable_job_status#0} -in [in_progress, done];
  ${var.iterable_job_status#1} -in [in_progress, done];
```

#### Optional fields

There are cases where a field is expected to be missing in one case, and present in another. Continuing with the
previous example, let's imagine that our jobs-response looks like this:

```
{
  "data": [
    {
      "name": "job_1",
      "status": "done",
      "comment": "some random comment"
    },
    {
      "name": "job_2",
      "status": "in_progress"
    }
  ]
}
```

Notice that one job has field `comment`, while the other one does not. This might be the case with the API you are
testing, and `qapir` has a directive `IF_EXISTS` that instructs the test to only validate `comment` if it exists.

Here's a complete statement that would achieve this:

```
SELECT
  $.data AS ${var.data}
FROM
  ${response.body};
  
SELECT_ALL
  $.name AS ${var.iterable_job_name} AND
  $.status AS ${var.iterable_job_status} AND
  $.comment AS ${var.iterable_job_comment}
FROM
  ${var.data}
EXPECT_ALL
  ${var.iterable_job_name} -type string AND
  ${var.iterable_job_status} -type string AND
  ${var.iterable_job_status} -in [in_progress, done] AND
  IF_EXISTS ${var.iterable_job_comment} -type string;
```

### Test-data generation

Tests that require dynamically-generated test-data can benefit from `qapir`'s ability to generate the following kinds of data:

1. Timestamp of type `number`, unix-milliseconds
2. UUID v4 of type `string`

To generate a timestamp or a uuid, use `${gen.timestamp}` or `${gen.uuid}` respectively.

### Test-parameters

In order to parametrize tests, simply add the following lines to your test-scenario:

```
test_parameters:
  name:
    - some_job_name
    - ${gen.timestamp}
```

Then reference test-parameters in your scenario via `${param.name}`.

Example:

```
name: Test name

test_parameters:
  name:
    - some_job_name
    - ${gen.timestamp}

test_steps:
  - description: Start a new job
    type: http
    url: ${env.url}/jobs
    method: POST
    expected_http_status: 200
    payload: |
      {
        "name": "${param.name}"
      }
    execute: |
      SELECT
        $.data.name AS ${var.job_name} AND
        $.data.status AS ${var.job_status}
      FROM
        ${response.body}
      EXPECT
        ${var.job_name} -eq ${param.name} AND
        ${var.job_status} -eq in_progress;
```

Note that test-parameters is a map, where key is a string and value is an array. It is required that all keys point to
arrays of the similar lengths.

### Test-parallelism

When you run a test-suite, `qapir` divides all tests into two groups: parallel and sequential.

Sequential tests are executed in strict order, based on how they appear in the suite-file.

Parallel tests all run in their own threads.

Here's how you mark a test as parallel:

```
name: Test name

parallel: true

test_steps:
  - description: Start a new job
    type: http
    url: ${env.url}/jobs
    method: POST
    expected_http_status: 200
    payload: |
      {
        "name": "test"
      }
    execute: |
      SELECT
        $.data.name AS ${var.job_name} AND
        $.data.status AS ${var.job_status}
      FROM
        ${response.body}
      EXPECT
        ${var.job_name} -eq test AND
        ${var.job_status} -eq in_progress;
```

By default all tests are sequential.

Note, all instances of a parallel test with parameters are executed sequentially.

### Retries

`qapir` can retry both a test-step, and a test-scenario until it succeeds. Success means that every data-extraction and
data-validation has passed.

Test retries - total count of attempts is 3 (1 main attempt + 2 retries).

```
name: Test name

retries: 2

test_steps:
  - description: Start a new job
    type: http
    url: ${env.url}/jobs
    method: POST
    expected_http_status: 200
    payload: |
      {
        "name": "test"
      }
    execute: |
      SELECT
        $.data.name AS ${var.job_name} AND
        $.data.status AS ${var.job_status}
      FROM
        ${response.body}
      EXPECT
        ${var.job_name} -eq test AND
        ${var.job_status} -eq in_progress;
```

Test-step retries - total count of attempts is 3 (1 main attempt + 2 retries) with an interval of 5 seconds.

```
name: Test name

test_steps:
  - description: Start a new job
    retries: 2
    interval: 5s
    type: http
    url: ${env.url}/jobs
    method: POST
    expected_http_status: 200
    payload: |
      {
        "name": "test"
      }
    execute: |
      SELECT
        $.data.name AS ${var.job_name} AND
        $.data.status AS ${var.job_status}
      FROM
        ${response.body}
      EXPECT
        ${var.job_name} -eq test AND
        ${var.job_status} -eq in_progress;
```

## Suite

A test-suite is simply a collection of tests. In `qapir`, a suite-file contains relative paths of test-files.

Example of a suite-file:

```
test_files:
  - type_tests/types_test.yml
  - type_tests/numbers_test.yml
  - type_tests/strings_test.yml
  - type_tests/bools_test.yml
  - type_tests/arrays_test.yml
  - optional_fields_test.yml
  - jsonpath_test.yml
  - headers_test.yml
  - http_status_test.yml
  - multi_step_test.yml
```
