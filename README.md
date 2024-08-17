# sso-tools

![release workflow](https://github.com/easytocloud/sso-tools/actions/workflows/release.yml/badge.svg)

AWS SSO tools for managing and organizing AWS configurations across multiple accounts and roles.

## generate-sso-config

`generate-sso-config` is a script that generates AWS SSO configuration and optionally creates a directory structure with .envrc files.

### Usage

```
generate-sso-config [OPTIONS]
```

### Options

- `--create-directories`: Create directories and .envrc files for each account
- `--developer-role-name NAME`: Specify a particular role name for which to create .envrc files
- `--sso-name NAME`: Override the SSO name extracted from the SSO start URL
- `--create-repos-md`: Run 'cclist --create-repos-md' in each account directory (requires --create-directories)
- `--help`: Display this help message and exit

### Examples

```
generate-sso-config
generate-sso-config --create-directories
generate-sso-config --create-directories --developer-role-name AdminRole
generate-sso-config --create-directories --sso-name my-org --create-repos-md
```

### Functionality

The script will:
1. Configure AWS SSO if not already set up
2. Generate AWS config profiles for all accounts and roles
3. Optionally create directories and .envrc files under ${HOME}/sso-unified/
4. Optionally run 'cclist --create-repos-md' in each account directory

### Environment Variables

- `AWS_CONFIG_FILE`: Path to AWS config file (default: ${HOME}/.aws/config)
- `SSO_REGION`: SSO region (default: eu-west-1)

## Install / Configure

Run `generate-sso-config` to create an ~/.aws/config for all your roles. Use the appropriate options based on your needs.

## Daily Use

1. Use `ssostart` to start a login-browser and authenticate your terminal session.
2. Set `AWS_PROFILE` to one of the profiles in your ~/.aws/config or use the --profile option on the CLI command line.

## Additional Tools

Check out our AWS profile organizer for more advanced profile management capabilities.

For more information, please refer to the documentation.
