# Cloud and AWS Interview Questions

## An EC2 instance needs secure access to S3. How do you avoid stored credentials?

Attach an IAM role to the EC2 instance through an instance profile and grant only the S3 actions and resources the application needs. The AWS SDK can obtain temporary credentials from the instance metadata service and refresh them; the app does not need a long-lived access key in a file or environment variable.

Use least privilege, protect access to instance metadata, and encrypt sensitive S3 data as required. See [IAM roles for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html).
