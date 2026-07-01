[![StepSecurity Maintained Action](https://raw.githubusercontent.com/step-security/maintained-actions-assets/main/assets/maintained-action-banner.png)](https://docs.stepsecurity.io/actions/stepsecurity-maintained-actions)

# install-aws-cli-action

[![test](https://github.com/step-security/install-aws-cli-action/actions/workflows/test.yaml/badge.svg)](https://github.com/step-security/install-aws-cli-action/actions?query=workflow%3Atest)

Install/Setup AWS CLI on a GitHub Actions Linux host.

After this action, every step is capable of running `aws` CLI, and it's up to you to set AWS credentials in the subsequent steps.

Tested in [step-security/install-aws-cli-action-test](https://github.com/step-security/install-aws-cli-action/actions/workflows/test.yaml)

**TIP**: It's possible to use the [entrypoint.sh](https://github.com/step-security/install-aws-cli-action/blob/main/entrypoint.sh) script as a "bootstrap script to install/setup aws cli on Linux", regardless of GitHub Actions; see [Other Options](https://github.com/step-security/install-aws-cli-action#other-options) for more details.

## Usage

Valid AWS CLI `version` values:

- `1` - latest v1
- `2` - latest v2 (default)
- `1.##.##` - specific v1
- `2.##.##` - specific v2

### Usage

Add one of the following steps to a job in your workflow.

#### Common Usage

```yaml
- id: install-aws-cli
  uses: step-security/install-aws-cli-action@v1
  with:
    version: 2                         # default
    verbose: false                     # default
    arch: amd64                        # allowed values: amd64, x86, x64, arm, arm64
```

#### Full Example

```yaml
- id: install-aws-cli
  uses: step-security/install-aws-cli-action@v1
  with:
    version: 2                         # default
    verbose: false                     # default
    arch: amd64                        # allowed values: amd64, arm64
    bindir: "/usr/local/bin"           # default
    installrootdir: "/usr/local"       # default
    rootdir: ""                        # defaults to "PWD"
    workdir: ""                        # defaults to "PWD/unfor19-awscli"
```

```yaml
name: test-action

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        include:
          - TEST_NAME: "Latest v2"
            AWS_CLI_VERSION: "2"
          - TEST_NAME: "Specific v2"
            AWS_CLI_VERSION: "2.0.30"
          - TEST_NAME: "Latest v1"
            AWS_CLI_VERSION: "1"
          - TEST_NAME: "Specific v1"
            AWS_CLI_VERSION: "1.32.15"
          - TEST_NAME: "No Input"
    name: Test ${{ matrix.TEST_NAME }} ${{ matrix.AWS_CLI_VERSION }}
    steps:
      - name: Test ${{ matrix.TEST_NAME }}
        id: install-aws-cli
        uses: step-security/install-aws-cli-action@v1
        with:
          version: ${{ matrix.AWS_CLI_VERSION }}
      - run: aws --version
        shell: bash
```

## Other options

- Execute locally
  ```bash
  curl -L -o install-aws.sh https://raw.githubusercontent.com/step-security/install-aws-cli-action/main/entrypoint.sh && \
  chmod +x install-aws.sh
  ./install-aws.sh "v2" "amd64"
  rm install-aws.sh  
  ```
- Dockerfile - Add this to your Dockerfile
  ```dockerfile
  # Install AWS CLI
  WORKDIR /tmp/
  RUN curl -L -o install-aws.sh https://raw.githubusercontent.com/step-security/install-aws-cli-action/main/entrypoint.sh && \
      sudo chmod +x install-aws.sh && \
      sudo ./install-aws.sh "v2" "amd64" && \
      sudo rm install-aws.sh
  ```
  **NOTE**: On some Docker images, you might need to add `sudo` in front of each command, like `sudo curl -L ..`, `sudo chmod ..`, etc.

## License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/step-security/install-aws-cli-action/blob/main/LICENSE) file for details
