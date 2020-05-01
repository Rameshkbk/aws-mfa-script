# aws-mfa-script
Script to fetch an MFA token for you to use temporary aws access credentials

I got this somewhere on github and made some changes to it to require 
less parameters and remember my MFA ARN.  

The profile name should be the name of the profile stanza in your 
`~/.aws/credentials` file as used by the aws-cli.

The ARN should be the ARN of your MFA device as specified in the AWS console.

The MFA code is the code your MFA device gives you.

# Installation
 1. Extract the files to your home directory `~/`
 2. Add `source ./alias.sh` to your `~/.bashrc`
 3. Copy `SAMPLE-mfa.cfg` to `~/mfa.cfg` 
 4. Add a profile name and MFA ARN for each aws cli profile you wish to use. The key should be the profile name and the value should be the ARN of the MFA to use for that profile. 
 
# Running the script
At a command prompt run the following command.

```
mfa <mfacode> <optional-aws-profile>
```
 
## Alias Note:
Scripts run in a subprocess of the calling shell.  This means that 
if you attempt to set the env vars in the script, they will only persist
inside that subprocess.  The `alias.sh` script sets an alias function to source the env vars into your main shell whenever you 
run the `mfa` command.

## Pre-req
```
brew install awscli
```

## Contents under ~/ folder
```javascript
file: mfa.cfg

default="arn:aws:iam::122423:mfa/john.doe"
mfa="arn:aws:iam::124121:mfa/john.doe"
```
mfa="arn:aws:iam::{aws-account-number}:mfa/john.doe"


## Contents under ~/.aws folder
```javascript
file: config

[default]
output = json
region = us-east-1

[mfa]
output = json
region = us-east-1
```
```javascript
file: credentials

[mfa]
aws_access_key_id =
aws_secret_access_key =

[default]
aws_access_key_id =
aws_secret_access_key =
aws_session_token =
aws_security_token =
```

- The [mfa] aws_access_key_id, aws_secret_access_key are the original creds from aws user account. This part never changes.
- The [default] section is the temp key, secret, session token and security token recieved using the [mfa] creds. This [default] part change everytime mfa is requested using ./mfa.sh <code> mfa.

- To verify that [mfa] sections is correct, run this command: aws iam get-user

## Manual Workflow
./mfa.sh <mfacode-from-google-authenticator> <optional-aws-profile>

./mfa.sh 12345 mfa
-- the above command create temporary access key, secret access key, session token, security token.
Copy these values into
~/.aws/credentials file under [default] profile

1. With existing access key, secret access key and passcode get the temporary access key, secret access key, session token and security token.
2. Set the temporary access key, secret access key, session token and security token as environment variable when starting the rest service in the local.
