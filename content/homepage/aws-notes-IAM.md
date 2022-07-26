+++
title = "AWS IAM"
description = "AWS Notes for the Certification Exam II"
date = "2022-07-27"
author = "mehmet sat"
+++

# IAM

- **4 Steps to Secure Your AWS Root Account:**
    - Enable multi-factor authentication(MFA) on the root account
    - Create an admin group for your administrators, and assign the appropriate permissions to this group
    - Create user accounts for your administrators
    - Add your users to the admin group
- Study the IAM Policy Documents
    - Example:
    
    ```jsx
    {
    	"version" : "2012-10-17",
      "Statement" : [
    				{
    				"Effect" : "Allow",
    				"Action" : "*",
    				"Resource" : "*"
    				}
    ]
    }
    ```
    

- **IAM is Universal: It does not differ for the regions**
- **The Root Account:** This is the account created when you first set up your AWS account and it has complete admin access. Secure it as soon as possible ad do not use it to login day to day.
- **New Users has no permissions when first created.**
- **Access Key ID and Secret Access Keys** are **not** the same as usernames and passwords. You cannot use them to log in to the console. You can connect to AWS CLI via the command line
- You only get to view these information once, if you lose them you have to regenerate them. Save them in a secure location
- Always set up password rotations: You can create and customize rotation policies.
- IAM Federation: You can combine your existing user account with AWS. For example when you log on to your PC(usually using Microsoft Active Directory), you can use the same credentials to log in to AWS if you set up fedeeation.
- Identity Federation: Uses the SAML standard, which is Active Directory.