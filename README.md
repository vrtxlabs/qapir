# About

`Qapir` (pronounced `K-P-R`) is a comprehensive no-code tool for API and Backend Testing.

Akin to a Swiss Army Knife in the world of Backend Testing, it provides:

* Human-readable syntax for test-scenarios
* Integrated reporting
* Support for complex multi-step scenarios
* Customizable retries for tests and test-steps
* Test parameterization
* Testing of REST and GraphQL APIs
* Integrated HTTP-mocking
...And many other useful features that simplify Backend Testing!

As a lightweight binary, `Qapir` functions effectively as a local development tool and as a test runner that easily fits into Continuous Integration pipelines.

It enables users to define test-scenarios in a clear and readable YAML-based syntax, execute individual tests or entire test suites, and generate detailed human-readable reports. All with just a single command!

# Docs
Explore our detailed documentation at https://docs.qapir.io and start testing in just minutes!

# Examples

1. [Writing tests](https://github.com/vrtxlabs/qapir/tree/main/.qapir)

2. Writing a [GitHub Action](https://github.com/vrtxlabs/qapir/blob/main/.github/workflows/qapir-demo.yml) that runs
   tests - see its execution [here](https://github.com/vrtxlabs/qapir/actions/runs/12736436705/job/35496318230#step:4:1)

3. Navigating the HTML report

https://github.com/user-attachments/assets/fbbc812f-146e-46ee-80ae-bb0d67ab900a

# Demo

[This guide](https://docs.qapir.io/quickstart) explains how to execute a series of predefined tests on the provided demo-app. By following
these instructions, you'll quickly experience the look and feel of `qapir`.

# Installation

Step 1: Download an archive with the latest release from this GitHub page - choose the build with the architecture matching
your setup [here](https://docs.qapir.io/installation)

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

# Known limitations

1. `jsonpath` expressions with multiple filters are not supported. As a workaround, filter by one criterion, store the
   value in a variable, and then filter by the next criterion.
2. `jsonpath` expressions that filter based on the absence of a field are not supported at this time, and there is no
   available workaround.
3. `qtl` statements are not validated for correctness yet. Please refer to the examples provided in this repository for
   guidance.
