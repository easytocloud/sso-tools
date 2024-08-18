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

## Setup and Configuration

1. Ensure you have AWS CLI v2 installed and configured for SSO access.
2. Run the `generate-sso-config` command to create your AWS CLI config file and directory structure:

```bash
generate-sso-config --create-directories
```

This command will:
- Create or update your AWS CLI config file (`~/.aws/config`)
- Generate a directory structure under `~/unified-environment/` (or `~/environment` in cloud9)
- Create `.envrc` files in each account directory

## Usage

After setting up SSO Tools, you have three options for working with AWS CLI:

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

### Daily Workflow

1. Start your AWS SSO session:
   ```bash
   ssostart
   ```
   This will open a browser window for authentication.

2. Navigate to the desired account directory:
   ```bash
   cd ~/unified-environment/my-sso/AccountName
   ```

3. Run AWS CLI commands without specifying a profile:
   ```bash
   aws s3 ls
   ```

## Command-line Options

The `generate-sso-config` command supports several options to customize its behavior:

- `--create-directories`: Creates the directory structure and `.envrc` files
- `--use-ou-structure`: Organizes the directory structure based on your AWS Organization's OU hierarchy
- `--developer-role-name <role>`: Specifies a particular role to use for directory creation
- `--sso-name <name>`: Sets a custom name for the SSO configuration (default is extracted from the SSO start URL)
- `--skip-sso` : Does not add the sso-name in the directory structure. (useful when working with single SSO like in cloud9)
- `--create-repos-md`: Generates a `repos.md` file in each account directory (requires `cclist` tool)

### Examples

1. Generate config with OU structure:
   ```bash
   generate-sso-config --create-directories --use-ou-structure
   ```

2. Generate config for a specific developer role:
   ```bash
   generate-sso-config --create-directories --developer-role-name DevRole
   ```

3. Generate config with a custom SSO name:
   ```bash
   generate-sso-config --create-directories --sso-name my-company-sso
   ```

## How It Works

1. The tool generates an AWS CLI config file with profiles for each role in each account the user can assume.
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
