# 📝IAM - Identity Access Management

- [IAM Permissions](#iam-permissions)
- [IAM Account Security](#iam-account-security)
- [How a user can access AWS](#how-a-user-can-access-aws)
- [IAM Security Tools](#iam-security-tools)
- [IAM Best Practices](#iam-best-practices)
- [IAM Shared Responsibility Model](#iam-shared-responsibility-model)

Identity Access Management is a Global Service. So, there's no need to select a region like us-east-1 for this. 

- First we have our Root Account. This account should not be shared or even used. We must use only to Create another users, called IAM Users.
- Users are people in a organization and they can be grouped. e.g. A group "developer" contains the user who are developers.
- A user can also belong to multiple groups.
- The Groups only contain users. ( not the other groups).
- Groups and Users are needed, because AWS Services needs Permissions, and we create these permissions for each group or user.



### IAM Permissions

**Policies**

- Users and Groups can be assigned to JSON Documents that contains the permissions (allowing or denying).
- Policies define the permissions to users/groups.
- Apply the "Least Privilege Principle". Only give the needed permissions.
  
Policy content:

```
A JSON policy document includes these elements:

- Optional policy-wide information at the top of the document
- One or more individual statements

Each statement includes information about a single permission. The information in a statement is contained within a series of elements.

Version – Specify the version of the policy language that you want to use. As a best practice, use the latest 2012-10-17 version.

Statement – This is the main part of the policy. It contains the rules about what actions are allowed or denied.

Sid (Optional) – Include an optional statement ID to differentiate between your statements.

Effect – Use Allow or Deny to indicate whether the policy allows or denies access.

Principal (Required in only some circumstances) – If you create a resource-based policy, you must indicate the account, user, role, or federated user to which you would like to allow or deny access. If you are creating an IAM permissions policy to attach to a user or role, you cannot include this element. The principal is implied as that user or role.

Action – Include a list of actions that the policy allows or denies.

Resource (Required in only some circumstances) – If you create an IAM permissions policy, you must specify a list of resources to which the actions apply. If you create a resource-based policy, this element is optional. If you do not include this element, then the resource to which the action applies is the resource to which the policy is attached.

Condition (Optional) – Specify the circumstances under which the policy grants permission.
```

![IAM Policy Structure](https://github.com/user-attachments/assets/edb71b3e-9855-4d37-8df2-a32917865a3c)

[AWS Docs Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#access_policies-json)


**Policy types**

- Identity-based policies - Identity-based policies are JSON permissions policy documents that control what actions an identity (users, groups of users, and roles) can perform, on which resources, and under what conditions.
- Resource-based policies 
- Permissions boundaries
- Organizations SCPs
- Access control lists (ACLs)
- Session policies


**Inline Policy** - Policies that you add directly to a single user, group, or role. They are deleted when you delete the identity.   


![Identity-based Policies](https://github.com/user-attachments/assets/9d3f6a22-78bf-4724-adeb-0b9d2260da8a)

e.g. IAMReadOnlyAccess Policy 

![IAM Policy](https://github.com/user-attachments/assets/868f4b61-3c3e-4a0f-98be-cd8fd244bc63)

```{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iam:GenerateCredentialReport",
                "iam:GenerateServiceLastAccessedDetails",
                "iam:Get*",
                "iam:List*",
                "iam:SimulateCustomPolicy",
                "iam:SimulatePrincipalPolicy"
            ],
            "Resource": "*"
        }
    ]
}
```

- Access Level - Full:List Limited:Read
- "iam:Get*": Allows all actions that start with "Get" for IAM. For example, GetUser or GetPolicy. These actions typically retrieve information.
- "iam:List*": Allows all actions that start with "List" for IAM, such as ListUsers or ListGroups. These actions list resources or data.

**Roles**

- Are created for another Services, so they can access or execute a process inside another service.
- We need to create or attach policies to this Role to grant the permissions.
- Then we attach the role to the service, and the service will be able to perform actions in out behalf. These roles are just like a user but are not used by a physical people and are used by an AWS Services. 
- Roles are not directly attached to users or groups.
- Roles are used when you want an entity (like a user, application, or AWS service) to temporarily take on a set of permissions.
- CamelCaseNamed
- The most common roles: [EC2 Instance Roles, Lambda Function Roles, Roles for CloudFormation]

<p align="center" width="100%"><img src="assets/roles.jpg" alt="roles" width="300"/></p>




### IAM Account Security

AWS Provides more security allowing us to add protection mechanisms to our account.

**MFA (Multi Factor Authentication)**

- MFA = Password that you know + security device you own that generates or receives a special code. Examples include a text message with a code or an app like Google Authenticator that gives you a unique number.
- Main benefit of MFA: if a password is stolen or hacked, the account is not compromised.
- MFA Devices:
  - Virtual MFA Devices (Authy, Google Authenticator) - Apps that generate time-based codes for MFA on your smartphone or tablet (software).
  - Universal 2nd Factor (U2F) Security Key - This is a physical device (like a USB stick) that you plug into your computer for MFA (hardware).
  - Hardware Key Fob MFA Device - A small physical device that displays changing codes you enter during login (hardware).
  - Hardware Key Fob MFA Device for AWS GovCloud (US) - Similar to the standard key fob, but designed for use in AWS GovCloud, a secure cloud region for government use (hardware).

**Password Policy**

- Set a minimum password length.
- Require specific characters in the password: Upper/Lower, numbers, non-alphanumeric.
- Allow IAM users to change their passwords.
- Require password change each X days.
- Prevent password reuse.




### How a user can access AWS?

A user can access AWS by:

**AWS Management Console**

- Root or IAM user + password (+ MFA optional)

**AWS CLI**

- Command Line Interface, protected by Access Key and Secrets. If wants to use the CLI in the own computer, so the user needs to install the AWS CLI.

**AWS CloudShell (CLI)**

- is a CLI inside the AWS Console, the user don't need to install this one.
- CloudShell environments will preserve files stored in your home directory ($HOME) for up to 120 days from the last time you initiated a CloudShell session. This limit applies on a regional basis.
- You can download/upload a files to the home directory of your CloudShell instance through your browser from your local machine.
  


**AWS SDK**

- Software Development Kit, for coding, also protected by Access Key and Secrets.
- It enables you to access and manage AWS services programmatically. 

The access keys and secrets are generated through the AWS Console and must be kept in secret. The users manages their own keys.
Access Keys is similar to the username.
Secret Keys is similar to the password.





### IAM Security Tools

**IAM Credentials Report** [root-account level]

- The Credentials Report lists all your account's users and the status of their credentials.

**IAM Access Advisor** [iam-user level]

- The access advisor shows the services permissions granted to a user and when those services were last accessed.





### IAM Best Practices

- Don’t use the root account except for AWS account setup
- One physical user = One AWS user
- Assign users to groups and assign permissions to groups
- Create a strong password policy
- Use and enforce the use of Multi Factor Authentication (MFA)
- Create and use Roles for giving permissions to AWS services
- Use Access Keys for Programmatic Access (CLI / SDK)
- Audit permissions of your account with the IAM Credentials Report
- Never share IAM users & Access Keys





### IAM Shared Responsibility Model

AWS is responsible for the infrastructure and the user is responsible for how he/she uses this infrastructure.

**AWS Responsibility**

- Infrastructure (global network security)
- Configuration and vulnerability analysis
- Compliance validation

**User Responsibility**

- Users, Groups, Roles, Policies management and monitoring
- Enable MFA on all accounts
- Rotate all your keys often
- Use IAM tools to apply appropriate permissions
- Analyze access patterns & review permissions




### Summary

- Users: mapped to a physical user, has a password for AWS Console
- Groups: contains users only
- Policies: JSON document that outlines permissions for users or groups
- Roles: for EC2 instances or AWS services
- MFA + Password Policy
- AWS CLI: manage your AWS services using the command-line
- AWS SDK: manage your AWS services using a programming language
- Access Keys: access AWS using the CLI or SDK
- Audit: IAM Credential Reports & IAM Access Advisor
