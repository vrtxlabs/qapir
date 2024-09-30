# About

`qapir` is a comprehensive **no-code** framework designed for API testing, providing a user-friendly syntax, integrated
reporting, support for complex multi-step scenarios, customizable retries, and test parameterization, among other
features.
As a lightweight binary, `qapir` functions effectively as both a development tool and a test runner, easily integrating
into Continuous Integration pipelines.

Users can define scenarios using `YAML` format, execute individual tests or entire test suites, and generate detailed,
human-readable reports—all with a single command.

We value feedback from our users. If you find `qapir` useful, please consider giving us a **GitHub Star**. If you
encounter any issues, please log them via GitHub. For feature requests or suggestions for improvements, we encourage you
to share your input through
our [Google Form](https://docs.google.com/forms/d/1JoBi2MCf6uaN3SZoxpX3oxIrDDeNE10cT5-5K4V3oko)

# Examples

1. [Writing tests](https://github.com/vrtxlabs/qapir/tree/main/.qapir)

2. Writing a [GitHub Action](https://github.com/vrtxlabs/qapir/blob/main/.github/workflows/qapir-demo.yml) that runs
   tests - see its execution [here](https://github.com/vrtxlabs/qapir/actions/runs/11105631023/job/30852225922#step:4:8)

3. Navigating the HTML report

https://github.com/user-attachments/assets/fbbc812f-146e-46ee-80ae-bb0d67ab900a



# Demo

[This guide](docs/Demo.md) explains how to execute a series of predefined tests on the provided demo-app. By following
these instructions, you'll quickly experience the look and feel of `qapir`.

# Installation

Step 1: Download an archive with the latest release from this GitHub page - choose the build with the architecture matching
your setup [here](https://github.com/vrtxlabs/qapir/releases/latest)

**Linux amd64**

   ```
   wget https://github.com/vrtxlabs/qapir/releases/latest/download/qapir-linux-amd64.tgz
   ```

**Mac arm64**

   ```
   curl -LO https://github.com/vrtxlabs/qapir/releases/latest/download/qapir-darwin-arm64.tgz
   ```

Step 2: Unzip the archive

**Linux amd64**

   ```
   sudo tar -zxvf qapir-linux-amd64.tgz -C /opt/
   ```

**Mac arm64**

```
   sudo tar -zxvf qapir-darwin-arm64.tgz -C /opt/
```

Step 3: Create a symlink to make `qapir` globally-available

   ```
   sudo ln -s /opt/qapir /usr/local/bin/qapir
   ```

Step 4: (Optional) Delete the archive

**Linux amd64**

```
   rm -rf qapir-linux-amd64.tgz
```

**Mac arm64**

```
   rm -rf qapir-darwin-arm64.tgz
``` 

# Getting started with `qapir`

### Step 1: Initialize `.qapir` workspace in your project

```
cd /path/to/your/project
qapir init
```

### Step 2: Configure Environments

The `qapir` environment refers to a collection of environment variables utilized in tests.
The files that store environment variables are located in the `.qapir/environments` directory.
How to define `qapir` environment files is described [here](docs/Environment.md).

### Step 3: Define Tests

The `qapir` test is a collection of test steps, each defined by a set of actions and validators.
`qapir` uses a specialized language called `qtl` that provides a variety of test validators.
`qapir` also allows for defining and modifying test variables at runtime, which can be used within the scope of a test
run.
To learn more about test definition and `qtl`, please proceed [here](docs/Test.md)

#### Group Tests

A collection of `qapir` tests grouped together is called a test suite.
To learn more about test suites, please proceed [here](docs/Test.md/#suite).

### Step 4: Run Tests

#### `cd` into `.qapir` directory of your project
```
cd .qapir
```

#### Run a test suite

```
qapir run suite -f {path-to-suite-yml} -e {path-to-environment-yml}
```

#### Run an individual test

```
qapir run test -f {path-to-test-yml} -e {path-to-environment-yml}
```

### Step 5: Review Test Reports in Browser

After every run, `qapir` will generate a one-file html report with all executed tests.
Reports are saved in `.qapir/reports` directory as `${timestamp}.html` files.

# Known limitations

1. `jsonpath` expressions with multiple filters are not supported. As a workaround, filter by one criterion, store the
   value in a variable, and then filter by the next criterion.
2. `jsonpath` expressions that filter based on the absence of a field are not supported at this time, and there is no
   available workaround.
3. `qtl` statements are not validated for correctness yet. Please refer to the examples provided in this repository for
   guidance.
