<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/secrets-manager/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# Secrets Manager CloudFormation Blueprint

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint provisions a SecretsManager Secret. It is useful to be able to test out Secrets Manager and then be able clean up all the resources after testing.

* Several [AWS::SecretsManager::Secret](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html) properties are configurable with [Parameters](https://lono.cloud/docs/configs/params/). Additionally, properties that require further customization are configurable with [Variables](https://lono.cloud/docs/configs/shared-variables/).

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/secrets-manager values
3. Deploy

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "secrets-manager", git: "git@github.com:boltopspro/secrets-manager.git"
```

## Configure

First you want to configure the [configs](https://lono.cloud/docs/core/configs/) files. Use [lono seed](https://lono.cloud/reference/lono-seed/) to configure starter values quickly.

    lono seed secrets-manager

The generated files in `config/secrets-manager` folder look something like this:

    configs/secrets-manager/
    ├── params
    │   └── development.txt
    └── variables
        └── development.rb

Here's an example of the params file:

configs/secrets-manager/params/development.txt:

    # Parameter Group: AWS::SecretsManager::Secret
    # Description=
    # KmsKeyId=
    # Name=
    # SecretString=

AWS recommends generating a random value for the secret and not hardcoding it with the `SecretString` property.  We achieve that with the `@generate_secret_string` variable. Here's an example:

configs/secrets-manager/variables/development.rb:

```ruby
@generate_secret_string = {
  SecretStringTemplate: '{"username":"test-user"}',
  GenerateStringKey: "password",
  PasswordLength: 30,
  ExcludeCharacters: '"@/\\'
}
```

## Deploy

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy. Example:

    lono cfn deploy secrets-manager --blueprint secrets-manager --sure

## Configure Details

### Stack Name Convention

By leveraging the lono Stack Name and [CLI conventions](https://lono.cloud/docs/conventions/cli/), we can organize the configs files in a way that matches the stack name. Example:

    lono cfn deploy secret-1 --blueprint secrets-manager
    lono cfn deploy secret-2 --blueprint secrets-manager

Will use the corresponding config files:

    configs/secrets-manager/development/secret-1.txt
    configs/secrets-manager/development/secret-2.txt

### Using SecretString Instead

If you elect to use `SecretString` instead, you must set `@generate_secret_string = nil`.  Example:

configs/secrets-manager/params/development.txt:

    # Parameter Group: AWS::SecretsManager::Secret
    SecretString=secret-value

configs/secrets-manager/variables/development.rb:

```ruby
@generate_secret_string = nil
```
