# AWS-IAM-Least-Privilege-Security
configure identities, groups, policies, and roles, then test authorized and denied actions to prove that least privilege works.

## Step 1 – Review the AWS IAM Dashboard

![AWS IAM Dashboard](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/f8b9648ae05da35deaccde4f8d48b6173768428f/Screenshot%202026-10-08%20005747.png)

**Figure 1 – Reviewing AWS IAM Security and Resources:** I accessed the AWS Identity and Access Management (IAM) dashboard to review the account's identity resources and security recommendations.

The dashboard showed no existing IAM users or user groups and identified four IAM roles. The security review also confirmed that the root account had no active access keys, while highlighting that multi-factor authentication (MFA) had not yet been configured for the root user.

This initial assessment established a baseline for configuring AWS identities, strengthening authentication, and implementing least-privilege access controls.

## Step 2 – Secure the AWS Root Account with MFA

![AWS Root Account MFA](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/8ef8bbc7370de8e442b20fb836f539133e4269b3/Screenshot%202026-10-08%20010217.png)

**Figure 2 – Configuring Root Account Multi-Factor Authentication:** I strengthened the AWS root account's authentication security by registering a Windows Hello passkey as a multi-factor authentication (MFA) device.

AWS confirmed the successful registration with the message `Passkey MFA device assigned`.

Using a passkey provides phishing-resistant authentication and reduces the risk of unauthorized access to the AWS root account.

Sensitive account information has been redacted from the screenshot before publication.

## Step 3 – Create an IAM Read-Only User Group

![IAM Read-Only User Group](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/71deb1343ad1138a8a1559ff46bb21d3bb4a0c6e/Screenshot%202026-10-08%20012302.png)

**Figure 3 – Configuring an IAM User Group:** I created an IAM user group named `CloudSecurity-ReadOnly` to establish a centralized permissions structure for read-only access.

I used AWS CloudShell and the AWS CLI to attach the AWS-managed `ReadOnlyAccess` policy to the group.

I then verified the configuration using `aws iam list-attached-group-policies`, which confirmed that the `ReadOnlyAccess` policy was successfully attached.

This configuration allows IAM users assigned to the group to inherit read-only permissions across supported AWS services without receiving general resource-modification privileges.

## Step 4 – Create a Restricted IAM User

![Restricted IAM User](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/13ba70d3fed9f2593a471fc92c75a180426cb01e/Screenshot%202026-10-08%20021552.png)

**Figure 4 – Creating and Assigning an IAM User:** I created an IAM user named `cloud-security-analyst` with AWS Management Console access and assigned it to the `CloudSecurity-ReadOnly` group.

The group membership verification confirmed that the user inherited the AWS-managed `ReadOnlyAccess` policy through the group rather than through directly attached permissions.

This configuration establishes centralized permission management and separates routine cloud access from the AWS root account.

The AWS account ID has been redacted from the screenshot before publication.

## Step 5 – Configure Multi-Factor Authentication for the IAM User

![IAM User MFA Configuration](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/bb37ea607a54fa595b10a694c38930bb69a6919a/Screenshot%202026-10-08%20120237.png)

**Figure 5 – Securing IAM User Authentication:** I configured multi-factor authentication (MFA) for the `cloud-security-analyst` IAM user using a Windows Hello passkey.

AWS confirmed the successful registration with the message `Passkey MFA device assigned`. The IAM user summary also displayed `Enabled with MFA`, confirming that the account had an MFA device configured.

This strengthens account authentication by adding phishing-resistant protection to the IAM user's AWS Management Console access.

The AWS account ID has been redacted from the screenshot before publication.

## Step 6 – Verify Inherited IAM Permissions

![Verify IAM Permissions](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/f95646c600751a1efc5f563d1a350a4ec49dace2/Screenshot%202026-10-08%20123156.png)

**Figure 6 – Verifying Group-Based IAM Permissions:** I reviewed the permissions assigned to the `cloud-security-analyst` IAM user to verify that access was inherited through the `CloudSecurity-ReadOnly` group.

The permissions summary confirmed that the AWS-managed `ReadOnlyAccess` policy was inherited through group membership rather than attached directly to the user.

The account also had the `IAMUserChangePassword` policy attached directly to support password management.

This verification confirmed that IAM group membership was functioning as intended and established a baseline for subsequent access-control testing.

## Step 7 – Validate Read-Only Access to Amazon S3

![Verify S3 Read-Only Access](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/69168bd2e8db18f886ef395422454412224cb577/Screenshot%202026-10-08%20130530.png)

**Figure 7 – Validating Authorized S3 Access:** I signed in to the AWS Management Console using the restricted `cloud-security-analyst` IAM user and accessed Amazon S3.

The S3 console successfully displayed the general-purpose bucket inventory, confirming that the user could list S3 buckets through the permissions inherited from the `CloudSecurity-ReadOnly` group.

The account contained no general-purpose S3 buckets at the time of testing. No access-denied errors occurred while retrieving the bucket listing.

This verified the user's ability to perform an authorized read operation without administrative access.

## Step 8 – Validate Least-Privilege Enforcement in Amazon S3

![S3 Bucket Creation Access Denied](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/3f01ec7ed15ec628207c994fcde9a02ba6c4106b/Screenshot%202026-10-08%20131214.png)

**Figure 8 – Validating Unauthorized S3 Bucket Creation:** I attempted to create an Amazon S3 bucket while authenticated as the restricted `cloud-security-analyst` IAM user.

AWS rejected the operation and displayed the message `Failed to create bucket`, indicating that the `s3:CreateBucket` permission was required.

This confirmed that the IAM user's inherited `ReadOnlyAccess` policy allowed S3 bucket listing but did not authorize bucket creation.

The test demonstrated that AWS IAM enforced the configured read-only access restrictions and prevented an unauthorized resource-creation operation.

## Step 9 – Validate Read-Only Access to Amazon EC2

![Verify EC2 Read-Only Access](images/09-verify-ec2-readonly-access.png)

**Figure 9 – Validating Authorized EC2 Resource Access:** I accessed the Amazon EC2 Dashboard in the US East (Ohio) region while authenticated as the restricted `cloud-security-analyst` IAM user.

The dashboard successfully displayed the account's EC2 resource inventory, including instances, security groups, Elastic IP addresses, and storage resources.

The results confirmed that no EC2 instances were running and that the account contained two security groups and one Elastic IP address.

This verified that the IAM user could view EC2 resource information through its inherited `ReadOnlyAccess` policy without requiring administrative permissions.

## Step 10 – Validate EC2 Resource Creation Restrictions

![EC2 Instance Launch Access Denied](images/10-ec2-launch-access-denied.png)

**Figure 10 – Validating EC2 Access Restrictions:** I attempted to launch an Amazon EC2 instance while authenticated as the restricted `cloud-security-analyst` IAM user.

AWS rejected the launch workflow because the account lacked the `ec2:CreateSecurityGroup` permission required by the selected configuration.

The authorization failure confirmed that the IAM user's inherited `ReadOnlyAccess` policy did not permit the security group creation operation.

This test demonstrated how AWS IAM prevents unauthorized infrastructure changes during EC2 provisioning.

Sensitive AWS account and resource identifiers have been redacted from the screenshot.
