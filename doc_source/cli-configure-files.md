--------

--------

# Configuration and credential file settings<a name="cli-configure-files"></a>

You can save your frequently used configuration settings and credentials in files that are maintained by the AWS CLI\. 

The files are divided into `profiles`\. By default, the AWS CLI uses the settings found in the profile named `default`\. To use alternate settings, you can create and reference additional profiles\. For more information on named profiles, see [Named profiles for the AWS CLI](cli-configure-profiles.md)\.

You can override an individual setting by either setting one of the supported environment variables, or by using a command line parameter\. For more information on configuration setting precedence, see [Configuration settings and precedence](cli-configure-quickstart.md#cli-configure-quickstart-precedence)\.

**Topics**
+ [Where are configuration settings stored?](#cli-configure-files-where)
+ [Set and view configuration settings](#cli-configure-files-methods)
+ [Supported `config` file settings](#cli-configure-files-settings)

## Where are configuration settings stored?<a name="cli-configure-files-where"></a>

The AWS CLI stores sensitive credential information that you specify with `aws configure` in a local file named `credentials`, in a folder named `.aws` in your home directory\. The less sensitive configuration options that you specify with `aws configure` are stored in a local file named `config`, also stored in the `.aws` folder in your home directory\. 

**Storing credentials in the config file**  
You can keep all of your profile settings in a single file as the AWS CLI can read credentials from the `config` file\. If there are credentials in both files for a profile sharing the same name, the keys in the credentials file take precedence\.   
These files are also used by the various language software development kits \(SDKs\)\. If you use one of the SDKs in addition to the AWS CLI, confirm if the credentials should be stored in their own file\.

Where you find your home directory location varies based on the operating system, but is referred to using the environment variables `%UserProfile%` in Windows and `$HOME` or `~` \(tilde\) in Unix\-based systems\. You can specify a non\-default location for the files by setting the `AWS_CONFIG_FILE` and `AWS_SHARED_CREDENTIALS_FILE` environment variables to another local path\. See [Environment variables to configure the AWS CLI](cli-configure-envvars.md) for details\. 

For example, the files generated by the AWS CLI for a default profile configured with `aws configure` looks similar to the following\.

**`~/.aws/credentials`**

```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

**`~/.aws/config`**

```
[default]
region=us-west-2
output=json
```

For file examples with multiple named profiles, see [Named profiles for the AWS CLI](cli-configure-profiles.md)\.

When you use a shared profile that specifies an AWS Identity and Access Management \(IAM\) role, the AWS CLI calls the AWS STS `AssumeRole` operation to retrieve temporary credentials\. These credentials are then stored \(in `~/.aws/cli/cache`\)\. Subsequent AWS CLI commands use the cached temporary credentials until they expire, and at that point the AWS CLI automatically refreshes the credentials\.

## Set and view configuration settings<a name="cli-configure-files-methods"></a>

There are several ways to view and set your configuration settings in the files\.

**`Credentials` and `config` file**  
View and edit your settings by directly editing the `config` and `credentials` files in a text editor\. For more information see [Where are configuration settings stored?](#cli-configure-files-where)  
To remove a setting, delete the corresponding setting in your `config` and `credentials` files\.

**`[aws configure](https://docs.aws.amazon.com/cli/latest/reference/configure/index.html)`**  
Run this command to quickly set and view your credentials, Region, and output format\. The following example shows sample values\.  

```
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json
```
For more information see [Quick configuration with `aws configure`](cli-configure-quickstart.md#cli-configure-quickstart-config)

**`[aws configure set](https://docs.aws.amazon.com/cli/latest/reference/configure/set.html)`**  
You can set any credentials or configuration settings using `aws configure set`\. Specify the profile that you want to view or modify with the `--profile` setting\.   
For example, the following command sets the `region` in the profile named `integ`\.  

```
$ aws configure set region us-west-2 --profile integ
```
To remove a setting, use an empty string as the value, or manually delete the setting in your `config` and `credentials` files in a text editor\.  

```
$ aws configure set cli_pager "" --profile integ
```

**[https://docs.aws.amazon.com/cli/latest/reference/configure/get.html](https://docs.aws.amazon.com/cli/latest/reference/configure/get.html)**  
You can retrieve any credentials or configuration settings you've set using `aws configure get`\. Specify the profile that you want to view or modify with the `--profile` setting\.   
For example, the following command retrieves the `region` setting in the profile named `integ`\.  

```
$ aws configure get region --profile integ
us-west-2
```
If the output is empty, the setting is not explicitly set and uses the default value\.

**[https://awscli.amazonaws.com/v2/documentation/api/latest/reference/configure/import.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/configure/import.html)**  
Import `CSV` credentials generated from the AWS web console\. A CSV file is imported with the profile name matching the IAM user name\. The CSV file must contain the following headers\.  
+ User Name
+ Access key ID
+ Secret access key
During initial key pair creation, once you close the **Download \.csv file** dialog box, you cannot access your secret access key after you close the dialog box\. If you need a `.csv` file, you'll need to create one yourself with the required headers and your stored key pair information\. If you do not have access to your key pair information, you need to create a new key pair\.

```
$ aws configure import --csv file://credentials.csv
```
For more information on key pairs, see [Access key ID and secret access key](cli-configure-quickstart.md#cli-configure-quickstart-creds)\.

**[https://docs.aws.amazon.com/cli/latest/reference/configure/list.html](https://docs.aws.amazon.com/cli/latest/reference/configure/list.html)**  
To list all configuration data, use the `aws configure list` command\. This command displays the AWS CLI name of all settings you've configured, their values, and where the configuration was retrieved from\.  

```
$ aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                <not set>             None    None
access_key     ****************ABCD  shared-credentials-file    
secret_key     ****************ABCD  shared-credentials-file    
    region                us-west-2             env    AWS_DEFAULT_REGION
```

**[https://docs.aws.amazon.com/cli/latest/reference/configure/list-profiles.html](https://docs.aws.amazon.com/cli/latest/reference/configure/list-profiles.html)**  
To list all your profile names, use the `aws configure list-profiles` command\.  

```
$ aws configure list-profiles
default
test
```

## Supported `config` file settings<a name="cli-configure-files-settings"></a>

**Topics**
+ [Global settings](#cli-configure-files-global)
+ [S3 Custom command settings](#cli-configure-files-s3)

The following settings are supported in the `config` file\. The values listed in the specified \(or default\) profile are used unless they are overridden by the presence of an environment variable with the same name, or a command line option with the same name\. For more information on what order settings take precendence, see [Configuration settings and precedence](cli-configure-quickstart.md#cli-configure-quickstart-precedence)

### Global settings<a name="cli-configure-files-global"></a>

* `aws\_access\_key\_id` *  
Specifies the AWS access key used as part of the credentials to authenticate the command request\. Although this can be stored in the `config` file, we recommend that you store this in the `credentials` file\.   
Can be overridden by the `AWS_ACCESS_KEY_ID` environment variable\. You can't specify the access key ID as a command line option\.  

```
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
```

*`aws\_secret\_access\_key`*  
Specifies the AWS secret key used as part of the credentials to authenticate the command request\. Although this can be stored in the `config` file, we recommend that you store this in the `credentials` file\.   
Can be overridden by the `AWS_SECRET_ACCESS_KEY` environment variable\. You can't specify the secret access key as a command line option\.  

```
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

*`aws_session_token`*  
Specifies an AWS session token\. A session token is required only if you manually specify temporary security credentials\. Although this can be stored in the `config` file, we recommend that you store this in the `credentials` file\.   
Can be overridden by the `AWS_SESSION_TOKEN` environment variable\. You can't specify the session token as a command line option\.  

```
aws_session_token = AQoEXAMPLEH4aoAH0gNCAPyJxz4BlCFFxWNE1OPTgk5TthT+FvwqnKwRcOIfrRh3c/LTo6UDdyJwOOvEVPvLXCrrrUtdnniCEXAMPLE/IvU1dYUg2RVAJBanLiHb4IgRmpRV3zrkuWJOgQs8IZZaIv2BXIa2R4Olgk
```

*`ca_bundle`*  
Specifies a CA certificate bundle \(a file with the `.pem` extension\) that is used to verify SSL certificates\.  
Can be overridden by the `AWS\_CA\_BUNDLE` environment variable or the `\-\-ca\-bundle` command line option\.   

```
ca_bundle = dev/apps/ca-certs/cabundle-2019mar05.pem
```

*`cli_auto_prompt`*  
Enables the auto\-prompt for the AWS CLI version 2\. There are two settings that can be used:  
+ **`on`** uses the full auto\-prompt mode each time you attempt to run an `aws` command\. This includes pressing **ENTER** after both a complete command or incomplete command\.

  ```
  cli_auto_prompt = on
  ```
+ **`on-partial`** uses partial auto\-prompt mode\. If a command is incomplete or cannot be run due to client\-side validation errors, auto\-prompt is used\. This mode is particular useful if you have pre\-existing scripts, runbooks, or you only want to be auto\-prompted for commands you are unfamiliar with rather than prompted on every command\.

  ```
  cli_auto_prompt = on-partial
  ```
You can override this setting by using the `aws\_cli\_auto\_prompt` environment variable or the `\-\-cli\-auto\-prompt` and `\-\-no\-cli\-auto\-prompt` command line parameters\.  
For information on the AWS CLI version 2 auto\-prompt feature, see [Having the AWS CLI prompt you for commands](cli-usage-parameters-prompting.md)\.

*`cli_binary_format`*  
Specifies how the AWS CLI version 2 interprets binary input parameters\. It can be one of the following values:  
+ **base64** – This is the default value\. An input parameter that is typed as a binary large object \(BLOB\) accepts a base64\-encoded string\. To pass true binary content, put the content in a file and provide the file's path and name with the `fileb://` prefix as the parameter's value\. To pass base64\-encoded text contained in a file, provide the file's path and name with the `file://` prefix as the parameter's value\.
+ **raw\-in\-base64\-out** – Provides backward compatibility with the AWS CLI version 1 behavior where binary values must be passed literally\. 
This entry does not have an equivalent environment variable\. You can specify the value on a single command by using the `--cli-binary-format raw-in-base64-out` parameter\.  

```
cli_binary_format = raw-in-base64-out
```
If you reference a binary value in a file using the `fileb://` prefix notation, the AWS CLI *always* expects the file to contain raw binary content and does not attempt to convert the value\.   
If you reference a binary value in a file using the `file://` prefix notation, the AWS CLI handles the file according to the current `cli_binary_format` setting\. If that setting's value is `base64` \(the default when not explicitly set\), the AWS CLI expects the file to contain base64\-encoded text\. If that setting's value is `raw-in-base64-out`, the AWS CLI expects the file to contain raw binary content\.

*`cli_history`*  
Disabled by default\. This setting enables command history for the AWS CLI\. After enabling this setting, the AWS CLI records the history of `aws` commands\.  

```
cli_history = enabled
```
You can list your history using the `aws history list` command, and use the resulting `command_ids` in the `aws history show` command for details\. For more information see [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/history/index.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/history/index.html) in the *AWS CLI reference guide*\.

*`cli_pager`*  
Specifies the pager program used for output\. By default, AWS CLI version 2 returns all output through your operating system’s default pager program\.  
Can be overridden by the AWS\_PAGER environment variable\.  

```
cli_pager=less
```
To disable all use of an external paging program, set the variable to an empty string as shown in the following example\.  

```
cli_pager=
```

*`cli_timestamp_format`*  
Specifies the format of timestamp values included in the output\. You can specify either of the following values:  
+ **iso8601** – The default value for the AWS CLI version 2\. If specified, the AWS CLI reformats all timestamps according to [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)\.

  ISO 8601 formatted timestamps look like the following examples\. The first example shows the time in [Coordinated Universal Time \(UTC\)](https://wikipedia.org/wiki/Coordinated_Universal_Time) by including a `Z` after the time\. The date and the time are separated by a `T`\.

  ```
  2019-10-31T22:21:41Z
  ```

  To specify a different time zone, instead of the `Z`, specify a `+` or `-` and the number of hours the desired time zone is ahead of or behind UTC, as a two\-digit value\. The following example shows the same time as the previous example but adjusted to Pacific Standard time, which is eight hours behind UTC\.

  ```
  2019-10-31T14:21:41-08
  ```
+ **wire** – The default value for the AWS CLI version 1\. If specified, the AWS CLI displays all timestamp values exactly as received in the HTTP query response\. 
This entry does not have an equivalent environment variable or command line option\.  

```
cli_timestamp_format = iso8601
```

*`credential\_process`*  
Specifies an external command that the AWS CLI runs to generate or retrieve authentication credentials to use for this command\. The command must return the credentials in a specific format\. For more information about how to use this setting, see [Sourcing credentials with an external process](cli-configure-sourcing-external.md)\.  
This entry does not have an equivalent environment variable or command line option\.  

```
credential_process = /opt/bin/awscreds-retriever --username susan
```

*`credential\_source`*  
Used within Amazon EC2 instances or containers to specify where the AWS CLI can find credentials to use to assume the role you specified with the `role_arn` parameter\. You cannot specify both `source_profile` and `credential_source` in the same profile\.  
This parameter can have one of three values:  
+ **Environment** – Specifies that the AWS CLI is to retrieve source credentials from environment variables\.
+ **Ec2InstanceMetadata** – Specifies that the AWS CLI is to use the IAM role attached to the [EC2 instance profile](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) to get source credentials\.
+ **EcsContainer** – Specifies that the AWS CLI is to use the IAM role attached to the ECS container as source credentials\.

```
credential_source = Ec2InstanceMetadata
```

*`duration_seconds`*  
Specifies the maximum duration of the role session, in seconds\. The value can range from 900 seconds \(15 minutes\) up to the maximum session duration setting for the role \(which can be a maximum of 43200\)\. This is an optional parameter and by default, the value is set to 3600 seconds\.

*`external\_id`*  
Specifies a unique identifier that is used by third parties to assume a role in their customers' accounts\. This maps to the `ExternalId` parameter in the `AssumeRole` operation\. This parameter is needed only if the trust policy for the role specifies a value for `ExternalId`\. For more information, see [How to use an External Gateway When Granting Access to Your AWS Resources to a Third Party](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user_externalid.html) in the *IAM User Guide*\.

*`max\_attempts`*  
Specifies a value of maximum retry attempts the AWS CLI retry handler uses, where the initial call counts toward the `max_attempts` value that you provide\.   
You can override this value by using the `AWS_MAX_ATTEMPTS` environment variable\.  

```
max_attempts = 3
```

*`mfa\_serial`*  
The identification number of an MFA device to use when assuming a role\. This is mandatory only if the trust policy of the role being assumed includes a condition that requires MFA authentication\. The value can be either a serial number for a hardware device \(such as `GAHT12345678`\) or an Amazon Resource Name \(ARN\) for a virtual MFA device \(such as `arn:aws:iam::123456789012:mfa/user`\)\.

*`output`*  
Specifies the default output format for commands requested using this profile\. You can specify any of the following values:  
+ **[`json`](cli-usage-output-format.md#json-output)** – The output is formatted as a [JSON](https://json.org/) string\.
+  **[`yaml`](cli-usage-output-format.md#yaml-output)** – The output is formatted as a [YAML](https://yaml.org/) string\.
+ **[`yaml-stream`](cli-usage-output-format.md#yaml-stream-output)** – The output is streamed and formatted as a [YAML](https://yaml.org/) string\. Streaming allows for faster handling of large data types\.
+ **[`text`](cli-usage-output-format.md#text-output)** – The output is formatted as multiple lines of tab\-separated string values\. This can be useful to pass the output to a text processor, like `grep`, `sed`, or `awk`\.
+ **[`table`](cli-usage-output-format.md#table-output)** – The output is formatted as a table using the characters \+\|\- to form the cell borders\. It typically presents the information in a "human\-friendly" format that is much easier to read than the others, but not as programmatically useful\.
Can be overridden by the `AWS_DEFAULT_OUTPUT` environment variable or the `--output` command line option\.  

```
output = table
```

*`parameter_validation`*  
Specifies whether the AWS CLI client attempts to validate parameters before sending them to the AWS service endpoint\.  
+ **true** – This is the default value\. If specified, the AWS CLI performs local validation of command line parameters\.
+ **false** – If specified, the AWS CLI does not validate command line parameters before sending them to the AWS service endpoint\.
This entry does not have an equivalent environment variable or command line option\.  

```
parameter_validation = false
```

*`region`*  
Specifies the AWS Region to send requests to for commands requested using this profile\.  
+ You can specify any of the Region codes available for the chosen service as listed in [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *Amazon Web Services General Reference*\.
+ `aws_global` enables you to specify the global endpoint for services that support a global endpoint in addition to Regional endpoints, such as AWS Security Token Service \(AWS STS\) and Amazon Simple Storage Service \(Amazon S3\)\.
You can override this value by using the `AWS_REGION` environment variable, `AWS_DEFAULT_REGION` environment variable, or the `--region` command line option\.  

```
region = us-west-2
```

*`retry\_mode`*  
Specifies which retry mode AWS CLI uses\. There are three retry modes available: legacy \(default\), standard, and adaptive\. For more information on retries, see [AWS CLI retries](cli-configure-retries.md)\.  
You can override this value by using the `AWS_RETRY_MODE` environment variable\.  

```
retry_mode = standard
```

*`role\_arn`*  
Specifies the Amazon Resource Name \(ARN\) of an IAM role that you want to use to run the AWS CLI commands\. You must also specify one of the following parameters to identify the credentials that have permission to assume this role:  
+ source\_profile
+ credential\_source

```
role_arn = arn:aws:iam::123456789012:role/role-name
```
The environment variable [`AWS_ROLE_ARN`](cli-configure-envvars.md#envvars-list-AWS_ROLE_ARN) overrides this setting\.  
For more information on using web identities, see [Assume role with web identity](cli-configure-role.md#cli-configure-role-oidc)\.

*`role\_session\_name`*  
Specifies the name to attach to the role session\. This value is provided to the `RoleSessionName` parameter when the AWS CLI calls the `AssumeRole` operation, and becomes part of the assumed role user ARN: ` arn:aws:sts::123456789012:assumed-role/role_name/role_session_name`\. This is an optional parameter\. If you do not provide this value, a session name is generated automatically\. This name appears in AWS CloudTrail logs for entries associated with this session\.  

```
role_session_name = maria_garcia_role
```
The environment variable [`AWS_ROLE_SESSION_NAME`](cli-configure-envvars.md#envvars-list-AWS_ROLE_SESSION_NAME) overrides this setting\.  
For more information on using web identities, see [Assume role with web identity](cli-configure-role.md#cli-configure-role-oidc)\.

*`source\_profile`*  
Specifies a named profile with long\-term credentials that the AWS CLI can use to assume a role that you specified with the `role_arn` parameter\. You cannot specify both `source_profile` and `credential_source` in the same profile\.  

```
source_profile = production-profile
```

*`sso\_account\_id`*  
Specifies the AWS account ID that contains the IAM role with the permission that you want to grant to the associated IAM Identity Center user\.  
This setting does not have an environment variable or command line option\.  

```
sso_account_id = 123456789012
```

*`sso\_region` *  
Specifies the AWS Region that contains the AWS access portal host\. This is separate from, and can be a different Region than the default CLI `region` parameter\.  
This setting does not have an environment variable or command line option\.  

```
aws_sso_region = us_west-2
```

*`sso\_role\_name` *  
Specifies the friendly name of the IAM role that defines the user's permissions when using this profile\.   
This setting does not have an environment variable or command line option\.  

```
sso_role_name = ReadAccess
```

*`sso\_start\_url`*  
Specifies the URL that points to the organization's AWS access portal\. The AWS CLI uses this URL to establish a session with the IAM Identity Center service to authenticate its users\. To find your AWS access portal URL, use one of the following:  
+ Open your invitation email, the AWS access portal URL is listed\.
+ Open the AWS IAM Identity Center \(successor to AWS Single Sign\-On\) console at [https://console\.aws\.amazon\.com/singlesignon/](https://console.aws.amazon.com/singlesignon/)\. The AWS access portal URL is listed in your settings\.
This setting does not have an environment variable or command line option\.   

```
sso_start_url = https://my-sso-portal.awsapps.com/start
```

*`web\_identity\_token\_file`*  
Specifies the path to a file that contains an OAuth 2\.0 access token or OpenID Connect ID token that is provided by an identity provider\. The AWS CLI loads the contents of this file and passes it as the `WebIdentityToken` argument to the `AssumeRoleWithWebIdentity` operation\.  
The environment variable [`AWS_WEB_IDENTITY_TOKEN_FILE`](cli-configure-envvars.md#envvars-list-AWS_WEB_IDENTITY_TOKEN_FILE) overrides this setting\.  
For more information on using web identities, see [Assume role with web identity](cli-configure-role.md#cli-configure-role-oidc)\.

*`tcp_keepalive`*  
Specifies whether the AWS CLI client uses TCP keep\-alive packets\.  
This entry does not have an equivalent environment variable or command line option\.  

```
tcp_keepalive = false
```

### S3 Custom command settings<a name="cli-configure-files-s3"></a>

Amazon S3 supports several settings that configure how the AWS CLI performs Amazon S3 operations\. Some apply to all S3 commands in both the `s3api` and `s3` namespaces\. Others are specifically for the S3 "custom" commands that abstract common operations and do more than a one\-to\-one mapping to an API operation\. The `aws s3` transfer commands `cp`, `sync`, `mv`, and `rm` have additional settings you can use to control S3 transfers\. 

All of these options can be configured by specifying the `s3` nested setting in your `config` file\. Each setting is then indented on its own line\. 

**Note**  
These settings are entirely optional\. You should be able to successfully use the `aws s3` transfer commands without configuring any of these settings\. These settings are provided to enable you to tune for performance or to account for the specific environment where you are running these `aws s3` commands\.

These settings are all set under a top\-level `s3` key in the `config` file, as shown in the following example for the `development` profile\.

```
[profile development]
s3 =
  max_concurrent_requests = 20
  max_queue_size = 10000
  multipart_threshold = 64MB
  multipart_chunksize = 16MB
  max_bandwidth = 50MB/s
  use_accelerate_endpoint = true
  addressing_style = path
```

The following settings apply to any S3 command in the `s3` or `s3api` namespaces\.

`addressing_style`  
Specifies which addressing style to use\. This controls whether the bucket name is in the hostname or is part of the URL\. Valid values are: `path`, `virtual`, and `auto`\. The default value is `auto`\.  
There are two styles of constructing an Amazon S3 endpoint\. The first is called `virtual` and includes the bucket name as part of the hostname\. For example: `https://bucketname.s3.amazonaws.com`\. Alternatively, with the `path` style, you treat the bucket name as if it is a path in the URI; for example, `https://s3.amazonaws.com/bucketname`\. The default value in the CLI is to use `auto`, which attempts to use the `virtual` style where it can, but will fall back to `path` style when required\. For example, if your bucket name is not DNS compatible, the bucket name cannot be part of the hostname and must be in the path\. With `auto`, the CLI will detect this condition and automatically switch to `path` style for you\. If you set the addressing style to `path`, you must then ensure that the AWS Region you configured in the AWS CLI matches the Region of your bucket\.

`payload_signing_enabled`  
Specifies whether to SHA256 sign sigv4 payloads\. By default, this is disabled for streaming uploads \(`UploadPart` and `PutObject`\) when using HTTPS\. By default, this is set to `false` for streaming uploads \(`UploadPart` and `PutObject`\), but only if a `ContentMD5` is present \(it is generated by default\) and the endpoint uses HTTPS\.  
If set to true, S3 requests receive additional content validation in the form of a SHA256 checksum which is calculated for you and included in the request signature\. If set to false, the checksum isn't calculated\. Disabling this can be useful to reduce the performance overhead created by the checksum calculation\. 

`use_dualstack_endpoint`  
Use the Amazon S3 dual IPv4 / IPv6 endpoint for all `s3` and `s3api` commands\. The default value is false\. This is mutually exclusive with the `use_accelerate_endpoint` setting\.  
If set to true, the AWS CLI directs all Amazon S3 requests to the dual IPv4 / IPv6 endpoint for the configured Region\. 

`use_accelerate_endpoint`  
Use the Amazon S3 Accelerate endpoint for all `s3` and `s3api` commands\. The default value is false\. This is mutually exclusive with the `use_dualstack_endpoint` setting\.   
If set to true, the AWS CLI directs all Amazon S3 requests to the `S3 Accelerate` endpoint at `s3-accelerate.amazonaws.com`\. To use this endpoint, you must enable your bucket to use `S3 Accelerate`\. All requests are sent using the virtual style of bucket addressing: `my-bucket.s3-accelerate.amazonaws.com`\. Any `ListBuckets`, `CreateBucket`, and `DeleteBucket `requests aren't sent to the S3 Accelerate endpoint as that endpoint doesn't support those operations\. This behavior can also be set if the `--endpoint-url` parameter is set to `https://s3-accelerate.amazonaws.com` or `http://s3-accelerate.amazonaws.com` for any `s3` or `s3api` command\.

The following settings apply only to commands in the `s3` namespace command set\.

`max_bandwidth`  
Specifies the maximum bandwidth that can be consumed for uploading and downloading data to and from Amazon S3\. The default is no limit\.  
This limits the maximum bandwidth that the S3 commands can use to transfer data to and from Amazon S3\. This value applies to only uploads and downloads; it doesn't apply to copies or deletes\. The value is expressed as bytes per second\. The value can be specified as:  
+ An integer\. For example, `1048576` sets the maximum bandwidth usage to 1 megabyte per second\. 
+ An integer followed by a rate suffix\. You can specify rate suffixes using: `KB/s`, `MB/s`, or `GB/s`\. For example, `300KB/s`, `10MB/s`\. 
In general, we recommend that you first try to lower bandwidth consumption by lowering `max_concurrent_requests`\. If that doesn't adequately limit bandwidth consumption to the desired rate, you can use the `max_bandwidth` setting to further limit bandwidth consumption\. This is because `max_concurrent_requests` controls how many threads are currently running\. If you instead first lower `max_bandwidth` but leave a high `max_concurrent_requests` setting, it can result in threads having to wait unnecessarily\. This can lead to excess resource consumption and connection timeouts\.

`max_concurrent_requests`  
Specifies the maximum number of concurrent requests\. The default value is 10\.  
The `aws s3` transfer commands are multithreaded\. At any given time, multiple Amazon S3 requests can be running\. For example, when you use the command `aws s3 cp localdir s3://bucket/ --recursive` to upload files to an S3 bucket, the AWS CLI can upload the files `localdir/file1`, `localdir/file2`, and `localdir/file3` in parallel\. The setting `max_concurrent_requests` specifies the maximum number of transfer operations that can run at the same time\.   
You might need to change this value for a few reasons:  
+ Decreasing this value – On some environments, the default of 10 concurrent requests can overwhelm a system\. This can cause connection timeouts or slow the responsiveness of the system\. Lowering this value makes the S3 transfer commands less resource intensive\. The tradeoff is that S3 transfers can take longer to complete\. Lowering this value might be necessary if you use a tool to limit bandwidth\. 
+ Increasing this value – In some scenarios, you might want the Amazon S3 transfers to complete as quickly as possible, using as much network bandwidth as necessary\. In this scenario, the default number of concurrent requests might not be sufficient to use all of the available network bandwidth\. Increasing this value can improve the time it takes to complete an Amazon S3 transfer\.

`max_queue_size`  
Specifies the maximum number of tasks in the task queue\. The default value is 1000\.  
The AWS CLI internally uses a model where it queues up Amazon S3 tasks that are then executed by consumers whose numbers are limited by `max_concurrent_requests`\. A task generally maps to a single Amazon S3 operation\. For example, a task could be a `PutObjectTask`, or a `GetObjectTask`, or an `UploadPartTask`\. The rate at which tasks are added to the queue can be much faster than the rate at which consumers finish the tasks\. To avoid unbounded growth, the task queue size is capped to a specific size\. This setting changes the value of that maximum number\.  
You generally don't need to change this setting\. This setting also corresponds to the number of tasks that the AWS CLI is aware of that need to be run\. This means that by default the AWS CLI can only see 1000 tasks ahead\. Increasing this value means that the AWS CLI can more quickly know the total number of tasks needed, assuming that the queuing rate is quicker than the rate of task completion\. The tradeoff is that a larger max\_queue\_size requires more memory\.

`multipart_chunksize`  
Specifies the chunk size that the AWS CLI uses for multipart transfers of individual files\. The default value is 8 MB, with a minimum of 5 MB\.  
When a file transfer exceeds the `multipart_threshold`, the AWS CLI divides the file into chunks of this size\. This value can be specified using the same syntax as `multipart_threshold`, either as the number of bytes as an integer, or by using a size and a suffix\.

`multipart_threshold`  
Specifies the size threshold the AWS CLI uses for multipart transfers of individual files\. The default value is 8 MB\.  
When uploading, downloading, or copying a file, the Amazon S3 commands switch to multipart operations if the file exceeds this size\. You can specify this value in one of two ways:  
+ The file size in bytes\. For example, `1048576`\.
+ The file size with a size suffix\. You can use `KB`, `MB`, `GB`, or `TB`\. For example: `10MB`, `1GB`\. 
**Note**  
S3 can impose constraints on valid values that can be used for multipart operations\. For more information, see the [S3 Multipart Upload documentation](https://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html) in the *Amazon Simple Storage Service User Guide*\.