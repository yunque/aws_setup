# AWS Account

## Create an AWS account
[Register an account](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html) and make note of your account number (we will use this later when provisioning resources through the terminal).

You will be asked for your credit card details. In this tutorial we will be creating ["free tier"](https://aws.amazon.com/free/) eligible resources, so don't worry about being charged for now. Note however, that these allowances expire after 12 months, so remember to stop or terminate the resources you create once you're done with the tutorial!

## Create a user
Users are handled by AWS's Identity Access Management (IAM) system. IAM allows you to manage users' authorization to access the various AWS resources you will provision.

On the [Console](https://console.aws.amazon.com/vpc/), go to ```IAM >> Users >> Add User```

Select the following options:
```
Access type: Programmatic access
Access type: AWS Management Console access
Console password: Autogenerated password
Require password reset
```

Click `Next: Permissions`  
Click `Next: Review`  
Click `Create User`  
Click `Download .csv` and store in a safe place  

To create multiple users, programmatically, and create `credentials` files in the same format, run the following (loop at will):
```
user=<username>;
aws iam create-user --user-name $user;

# Generate access keys
create_access_key=$(aws iam create-access-key --user-name $user);
secret_access_key=$(echo $create_access_key | jq -r ".AccessKey.SecretAccessKey");
access_key_id=$(echo $create_access_key | jq -r ".AccessKey.AccessKeyId")

# Generate a password
pw=$(sha1sum <<< "$(date) $RANDOM")
login_profile=$(aws iam create-login-profile --user-name $user --password $pw --password-reset-required)

organization=dssg2019
echo "User name,Password,Access key ID,Secret access key,Console login link" >> ~/aws_console_credentials_$user.csv
echo "$user,$pw,$access_key_id,$secret_access_key,"https://"$organization".signin.aws.amazon.com/console" >> ~/aws_console_credentials_$user.csv
```

## Configure your AWS CLI
### Download
AWS provide a [Command Line Interface](https://aws.amazon.com/cli/) which allows you to manage and provision resources, permissions, services, etc. from the terminal. This is especially useful when you want to create easily reproducible infrastructure configurations, for example, if you want to replicate a setup for multiple concurrent projects.

Linux/Max:
```
pip install awscli
```

Windows:
```
https://aws.amazon.com/cli/
```

### Configure
In the terminal, run `aws configure` and enter the relevant fields given in the `credentials.csv` file (for example, in a hidden folder in your home directory, e.g. `~/.aws/credentials.csv`)

```
AWS Access Key ID: [...]
AWS Secret Access Key: [...]
Default region name: [...]
Default output format: json
```

For the region name, select the closest to your physical location (e.g. `eu-central-1`) from those listed here: https://docs.aws.amazon.com/general/latest/gr/rande.html

### Explore
To see what you can do with the AWS CLI, run `aws help`. **Available services** shows every resource you can manage from the terminal with this tool.

To see what you can do with a specific resource, e.g. EC2, run `aws ec2 help`. Use `grep` to see only the functions for a specific type of action (`aws ec2 help | grep create`) or service (`aws ec2 help | grep subnet`)
