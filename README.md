# SSO Tools for AWS CLI

![release workflow](https://github.com/easytocloud/sso-tools/actions/workflows/release.yml/badge.svg)

## Overview

SSO Tools is a utility designed to streamline the management of multiple AWS accounts in environments using AWS Single Sign-On (SSO) with the AWS CLI. While AWS CLI v2 supports SSO, switching between roles in different accounts can be cumbersome. This tool simplifies the process by:

1. Generating a properly configured AWS CLI config file
2. Creating a directory structure that mirrors your AWS Organization
3. Setting up environment files for easy role switching using `direnv`

By leveraging this tool, AWS CLI users can effortlessly switch between roles by simply changing directories, eliminating the need to manually set environment variables or repeatedly specify profiles.

## Installation

SSO Tools can be easily installed using Homebrew:

```bash
brew tap easytocloud/tap
brew install sso-tools
```

This command will install SSO Tools along with its dependencies (`cclist` and `direnv`).

**Note:** The AWS CLI is not automatically installed and should be set up separately if not already present on your system.

## Usage

### Basic Usage

To generate the AWS CLI config and create the directory structure:

```bash
generate-sso-config --create-directories
```

This command will:
- Create or update your AWS CLI config file (`~/.aws/config`)
- Generate a directory structure under `~/unified-environment/`
- Create `.envrc` files in each account directory

### Command-line Options

Run `generate-sso-config --help` to see all available options:

```
Usage: generate-sso-config [OPTIONS]

Options:
  --create-directories        Create a directory for each account the user can assume any role in
  --use-ou-structure          Use the OU structure in the unified environment [see note]
  --developer-role-name NAME  Create .envrc files for the specified role
  --sso-name NAME             Use the specified SSO name instead of the one extracted from the SSO start URL
  --create-repos-md           Run cclist --create-repos-md for each account directory
  --skip-sso-name             Do not use the SSO name in the path
  --unified-root PATH         Use a different root directory for the unified environment
  --help                      Display this help message and exit
```

### Examples

1. Generate config with OU structure:
   ```bash
   generate-sso-config --create-directories --use-ou-structure
   ```
   NOTE: requires Organizations:List* and Organizations:Describe* permission in Organizations main account.

2. Generate config for a specific developer role:
   ```bash
   generate-sso-config --create-directories --developer-role-name DevRole
   ```

3. Generate config with a custom SSO name:
   ```bash
   generate-sso-config --create-directories --sso-name my-company-sso
   ```

## Working with AWS CLI

After running `generate-sso-config`, you have three options for working with AWS CLI:

1. Use the `--profile` option with each AWS CLI command:
   ```bash
   aws s3 ls --profile RoleName@AccountName
   ```

2. Set the `AWS_PROFILE` environment variable:
   ```bash
   export AWS_PROFILE=RoleName@AccountName
   aws s3 ls
   ```

3. Use `direnv` for automatic profile switching (recommended):
   ```bash
   cd ~/unified-environment/my-sso/AccountName
   aws s3 ls  # AWS_PROFILE is automatically set by direnv
   ```

## How It Works

1. The tool generates an AWS CLI config file with profiles for each role in each account.
2. It creates a directory structure under `~/unified-environment/` that mirrors your AWS accounts (and optionally, your OU structure).
3. In each account directory, it creates an `.envrc` file that sets the appropriate `AWS_PROFILE` environment variable.
4. When you navigate into an account directory, `direnv` automatically loads the environment, setting the correct AWS profile.

This setup allows you to switch between AWS accounts and roles simply by changing directories, making it significantly easier to work with multiple AWS accounts in your day-to-day operations.

## Troubleshooting

- Ensure your AWS CLI is properly configured for SSO access.
- If `direnv` is not working, make sure it's correctly installed and hooked into your shell. Run `direnv allow` in each account directory to authorize the `.envrc` files.
- If you encounter permission issues, ensure the script has execute permissions and that you have write access to the necessary directories.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
