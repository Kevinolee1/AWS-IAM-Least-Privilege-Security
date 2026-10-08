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

![Verify EC2 Read-Only Access](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/6bace71d0f853abb68640a4c39a37cb51dfc0950/Screenshot%202026-10-08%20131905.png)

**Figure 9 – Validating Authorized EC2 Resource Access:** I accessed the Amazon EC2 Dashboard in the US East (Ohio) region while authenticated as the restricted `cloud-security-analyst` IAM user.

The dashboard successfully displayed the account's EC2 resource inventory, including instances, security groups, Elastic IP addresses, and storage resources.

The results confirmed that no EC2 instances were running and that the account contained two security groups and one Elastic IP address.

This verified that the IAM user could view EC2 resource information through its inherited `ReadOnlyAccess` policy without requiring administrative permissions.

## Step 10 – Validate EC2 Resource Creation Restrictions

![EC2 Instance Launch Access Denied](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/84d18272fc7af882aec38b0ef32e975741647165/Screenshot%202026-10-08%20132544.png)

**Figure 10 – Validating EC2 Access Restrictions:** I attempted to launch an Amazon EC2 instance while authenticated as the restricted `cloud-security-analyst` IAM user.

AWS rejected the launch workflow because the account lacked the `ec2:CreateSecurityGroup` permission required by the selected configuration.

The authorization failure confirmed that the IAM user's inherited `ReadOnlyAccess` policy did not permit the security group creation operation.

This test demonstrated how AWS IAM prevents unauthorized infrastructure changes during EC2 provisioning.

Sensitive AWS account and resource identifiers have been redacted from the screenshot.

## Step 11 – Verify No Unauthorized EC2 Instance Was Created

![Verify No EC2 Instances](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/b441a935131c0801c0bbdc6bc3141b76bd2eeeca/Screenshot%202026-10-08%20134914.png)

**Figure 11 – Verifying EC2 Resource Creation Was Blocked:** After AWS denied the EC2 launch workflow, I returned to the Amazon EC2 Instances dashboard to verify that no instance had been provisioned.

The instance inventory displayed `No instances`, confirming that the attempted launch did not create an EC2 instance in the selected AWS region.

This follow-up verification established that the unauthorized provisioning attempt was blocked without leaving an active compute resource.

## Step 12 – Simulate IAM Permissions for EC2 Instance Launch

![IAM Policy Simulation](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/0fd208a645fb4390b3750ee727c5baf2314478d1/Screenshot%202026-10-08%20141242.png)

**Figure 12 – Validating EC2 Launch Permissions:** I used AWS CloudShell and the AWS CLI `simulate-principal-policy` command to evaluate whether the `cloud-security-analyst` IAM user was authorized to perform the `ec2:RunInstances` action.

The simulation returned `implicitDeny`, confirming that the user's effective IAM permissions did not grant EC2 instance launch authorization.

This provided additional verification of the least-privilege restrictions configured through the `CloudSecurity-ReadOnly` IAM group.

The AWS account ID has been redacted from the screenshot before publication.

## Step 13 – Validate Authorized S3 Access Using IAM Policy Simulation

![S3 IAM Policy Simulation](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/1e5a453335f703323d461f1dcf00b6c38b086dcf/Screenshot%202026-10-08%20141452.png)

**Figure 13 – Simulating Authorized S3 Read Access:** I used AWS CloudShell and the AWS CLI `simulate-principal-policy` command to evaluate the `s3:ListAllMyBuckets` permission assigned to the `cloud-security-analyst` IAM user.

The simulation returned `allowed`, confirming that the user's effective IAM permissions authorized listing Amazon S3 buckets.

This result complemented the previous EC2 simulation, which returned `implicitDeny` for `ec2:RunInstances`, demonstrating that the configured permissions permitted authorized read operations while restricting infrastructure modifications.

## Step 14 – Simulate Unauthorized S3 Bucket Creation

![S3 Bucket Creation Permission Simulation](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/435ee124ace10bf414ee26fdbb193e2871254eef/Screenshot%202026-10-08%20141641.png)

**Figure 14 – Validating S3 Resource Creation Restrictions:** I used AWS CloudShell and the AWS CLI `simulate-principal-policy` command to evaluate whether the `cloud-security-analyst` IAM user could perform the `s3:CreateBucket` action.

The simulation returned `implicitDeny`, confirming that no applicable IAM policy granted permission to create the specified S3 bucket.

This result reinforced the earlier S3 access-denied test and verified that the user's inherited read-only permissions prevented unauthorized storage resource creation.

The simulation evaluated IAM permissions without provisioning any AWS resources.

## Step 15 – Create a Custom Least-Privilege IAM Policy

![Custom IAM Policy Created](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/31731ac0353b3d6361973ff11fa23aa73b643954/Screenshot%202026-10-08%20142145.png)

**Figure 15 – Creating a Custom IAM Security Policy:** I created an AWS customer-managed IAM policy named `CloudSecurity-CustomReadOnly` to establish more restrictive access controls than the AWS-managed `ReadOnlyAccess` policy.

The policy was configured to allow two read-only actions: `s3:ListAllMyBuckets` and `ec2:DescribeInstances`.

AWS displayed a successful policy creation confirmation. The next phase verifies the policy configuration before assigning it to the IAM security group.

## Step 16 – Attach the Custom IAM Policy to the Security Group

![Attach Custom IAM Policy](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/2a1c237a9e5396b7aa330be8efd5013c9715d486/Screenshot%202026-10-08%20144507.png)

**Figure 16 – Assigning a Custom Least-Privilege Policy:** I attached the customer-managed `CloudSecurity-CustomReadOnly` policy to the `CloudSecurity-ReadOnly` IAM group.

AWS confirmed that the policy was successfully attached. The group's permissions list displayed both the custom policy and the existing AWS-managed `ReadOnlyAccess` policy.

This established the custom policy before transitioning the group away from broader AWS-managed read-only permissions.

AWS account identifiers have been redacted from the screenshot.

## Step 17 – Enforce Custom Least-Privilege IAM Permissions

![Enforce Custom IAM Permissions](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/7c68c91052b561d7b71edebfd8587f3a0bd0095f/Screenshot%202026-10-08%20145150.png)

**Figure 17 – Enforcing Least-Privilege Access:** I removed the AWS-managed `ReadOnlyAccess` policy from the `CloudSecurity-ReadOnly` IAM group while retaining the customer-managed `CloudSecurity-CustomReadOnly` policy.

The updated permissions configuration confirmed that the group contained only one attached policy.

This reduced the group's resource permissions to two explicitly authorized actions: `s3:ListAllMyBuckets` and `ec2:DescribeInstances`.

The change demonstrated how replacing broad AWS-managed permissions with a narrowly scoped customer-managed policy can reduce unnecessary access.

## Step 18 – Verify Custom Least-Privilege Policy Permissions

![Verify Custom IAM Policy](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/092fd138340fbb5b2adb4e48cbb73520aca96a19/Screenshot%202026-10-08%20145445.png)

**Figure 18 – Validating Authorized IAM Permissions:** I used AWS CloudShell and the AWS CLI `simulate-principal-policy` command to verify the permissions assigned to the `cloud-security-analyst` IAM user after replacing the broader AWS-managed policy.

The simulation evaluated two actions: `s3:ListAllMyBuckets` and `ec2:DescribeInstances`.

Both actions returned `allowed`, confirming that the custom `CloudSecurity-CustomReadOnly` policy preserved the intended S3 and EC2 read permissions.

This verification demonstrated that the IAM user retained its explicitly authorized access after the least-privilege policy transition.

## Step 19 – Verify Unauthorized IAM Actions Are Denied

![Verify Denied IAM Actions](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/55713a8c3bc54b9d6efa0a26b41c3b4a0307a2ec/Screenshot%202026-10-08%20145613.png)

**Figure 19 – Validating Least-Privilege Restrictions:** I used AWS CloudShell and the AWS CLI `simulate-principal-policy` command to evaluate three unauthorized actions assigned to the `cloud-security-analyst` IAM user.

The simulation tested `ec2:RunInstances`, `s3:CreateBucket`, and `s3:ListBucket`.

All three actions returned `implicitDeny`, confirming that the user's effective IAM permissions did not grant authorization to launch EC2 instances, create S3 buckets, or list objects within individual S3 buckets.

These results demonstrated that the custom IAM policy restricted access to explicitly authorized operations while preventing additional infrastructure and storage actions.

## Step 20 – Final IAM Least-Privilege Security Verification

![Final IAM Security Verification](https://github.com/Kevinolee1/AWS-IAM-Least-Privilege-Security/blob/ae18405fd02a3c0fcb2001a009ffcbcbb80e2903/Screenshot%202026-10-08%20145748.png)

**Figure 20 – Final Verification of IAM Least-Privilege Controls:** I performed a final AWS IAM permission simulation using AWS CloudShell and the AWS CLI to validate the effective permissions assigned to the `cloud-security-analyst` IAM user.

The simulation evaluated five AWS actions across Amazon S3 and Amazon EC2.

Two authorized actions, `s3:ListAllMyBuckets` and `ec2:DescribeInstances`, returned `allowed`.

Three unauthorized actions, `ec2:RunInstances`, `s3:CreateBucket`, and `s3:ListBucket`, returned `implicitDeny`.

All five results matched the expected security configuration, confirming that the custom `CloudSecurity-CustomReadOnly` policy enforced the intended least-privilege access restrictions.

This completed the AWS IAM & Least-Privilege Security lab, including IAM user and group management, MFA configuration, customer-managed policies, access-denied testing, and IAM permission simulation.
