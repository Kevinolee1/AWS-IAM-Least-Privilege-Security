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

![IAM User MFA Configuration](images/05-iam-user-mfa.png)

**Figure 5 – Securing IAM User Authentication:** I configured multi-factor authentication (MFA) for the `cloud-security-analyst` IAM user using a Windows Hello passkey.

AWS confirmed the successful registration with the message `Passkey MFA device assigned`. The IAM user summary also displayed `Enabled with MFA`, confirming that the account had an MFA device configured.

This strengthens account authentication by adding phishing-resistant protection to the IAM user's AWS Management Console access.

The AWS account ID has been redacted from the screenshot before publication.
