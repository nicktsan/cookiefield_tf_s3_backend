Prerequisites:
- backend in https://github.com/nicktsan/aws_backend/tree/main deployed
- The Terraform CLI (1.2.0+) installed.
- The AWS CLI installed.
- AWS account and associated credentials that allow you to create resources.
Read the Terraform/AWS setup tutorials at https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli and https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build for detailed instructions.

This project provisions an S3 bucket as well as a Dynamo DB table in AWS via terraform, as well as a dev workspace within that S3 bucket.

Prerequisites: 
- Terraform CLI (https://developer.hashicorp.com/terraform/downloads)
- Terraform Cloud account (https://app.terraform.io/app)
- A Terraform Cloud Organization with the same name as the one defined in s3/backend.tf
The name of the organization can be changed to fit your needs. Just make sure the names in Terraform cloud and s3/backend.tf match.

Navigate to backend/s3 directory

Use your IAM credentials to authenticate the Terraform AWS provider. Set the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY environment variables
Windows cmd prompt:
SETX AWS_ACCESS_KEY_ID <your iam access key id>
SETX AWS_SECRET_ACCESS_KEY <your iam secret access key>

Then set the AWS_PROFILE environment variable:
Windows cmd prompt:
SETX AWS_PROFILE <your iam profile name>

If you are using a new AWS account, change the workspace name in backend/s3/backend.tf to a new value

Ensure you have already configured Terraform CLI to work with Terraform Cloud. First, create a Terraform Cloud account at https://app.terraform.io/signup/account. Then use the command below to configure it:
terraform login

Then run: terraform init
This will create the .terraform folder in the directory where your .tf files are

Then run: 
terraform plan -out out.tfplan
This will save the output of the plan to a file and create the workspace in your Terraform organization.
Alternatively, if you want to use an input file to avoid manually inputting values for dynamodb_name and bucket_name, run:
terraform plan -var-file input.tfvars -out out.tfplan
where input.tfvars contains values for dynamodb_name and bucket_name

In Terraform Cloud settings, navigate to the newly created workspace's settings, and change Execution Mode to "Local" if you are running terraform locally. 
This can be found in https://app.terraform.io/app/<username>/workspaces/<workspace name>/settings/general
Then click "Save Settings"

Rerun "terraform init" and "terraform plan -var-file input.tfvars -out out.tfplan"

Once the file has been created, run the command below to build the statefile in Terraform Cloud and create the S3 bucket and DynamoDB table in AWS.
terraform apply out.tfplan

Navigate to mainproject directory

Run:
terraform init

Then create a dev workspace within the s3 bucket with
terraform workspace new <dev environment name>

Once the workspace is created, rerun:
terraform init

Once init is finished, run:
terraform plan -out out.tfplan

Once plan is successful, run the command below to deploy the dev environment within your S3 bucket: 
terraform apply out.tfplan

If you encounter the following error:
Error: creating Amazon S3 (Simple Storage) Bucket <bucket>: AuthorizationHeaderMalformed: The authorization header is malformed; the region 'us-east-1' is wrong; expecting 'eu-central-1'
Make sure to give your bucket in /mainproject/main.tf a different name, as the above errors occurs if it happens to have the same name as other buckets in other AWS accounts in the whole region.

A dev environment should have been created within the S3 bucket.