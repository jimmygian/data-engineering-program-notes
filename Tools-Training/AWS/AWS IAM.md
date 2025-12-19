
# AWS IAM

AWS IAM is a web service that helps you manage and securely control access to your AWS resources and services. With IAM, you can **centrally** manage who is authenticated in your Account and what resource permissions they have. Using IAM, you can share your resources without sharing your credentials, and you can select specific actions people can access at a granular level. It’s a global service available at no additional cost, meaning you can see and use your IAM configurations from any region in the AWS Management Console.

## **What is an IAM user?**

When you create an account on AWS, you begin with the “**root user**” identity, which has full access to all AWS resources and services in the account. It is strongly recommended that you don’t perform daily operations using this account. Instead, create an **admin** user for everyday tasks. Whether you’re the root or admin user, you can create other users in your account to allow people in your organization to access AWS resources.

IAM users are created under your AWS account, so you don't need separate accounts for IAM users. Each user could be a person or service that interacts with AWS resources. When you create a user, you define what resources the IAM user can access, and what actions they can perform. **AWS will then generate a set of credentials for that user.** 
The credentials could be
- a username and password for accessing the AWS management console, 
- or they it be an access keys for programmatic access to AWS resources. 
IAM **user credentials are long-term credentials** as they stay with the user until the admin rotates them. When you provide users with their own login credentials, you help prevent the sharing of credentials. You can add more users to your account, and all user activities are billed to your account.

By default, **a new user does not have any permission to access any AWS resources.** You can grant them access to AWS resources by **attaching policies** to them. A policy specifies what actions are allowed or denied for a given resource (read only, write only, full access). 

> 	*A policy can be attached to multiple users, and a user can have multiple policies.* 

You can choose 
- **AWS-managed policies** 
- or create your own **custom policies**. 
Whenever a user makes a request, AWS evaluates their policies to determine if that request is allowed.

For example, suppose you’re working with a data scientist and want to grant them read-only access to an S3 bucket to extract the training data set. The figures below show the steps to create this user.

More on IAM: https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html
