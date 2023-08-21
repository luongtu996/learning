### 1. Using AWS CLI to create IAM user

#### 1.1 Create IAM group

`aws iam create-group --group-name Prod --profile awslearning`

**Output**
```console
{
    "Group": {
        "Path": "/",
        "GroupName": "Prod",
        "GroupId": "AGPASSWD33PD6RLQAG76Z",
        "Arn": "arn:aws:iam::177578171335:group/Prod",
        "CreateDate": "2023-07-23T03:08:32+00:00"
    }
}
```


#### 1.2 Create IAM user

`aws iam create-user --user-name Bob --profile awslearning`

**Output**
```console
{
    "User": {
        "Path": "/",
        "UserName": "Bob",
        "UserId": "AIDASSWD33PDQFGMEMJO5",
        "Arn": "arn:aws:iam::177578171335:user/Bob",
        "CreateDate": "2023-07-23T03:09:43+00:00"
    }
}
```

#### 1.3 Add user to the Prod group

`aws iam add-user-to-group --user-name Bob --group-name Prod --profile awslearning`


#### 1.4 Create Login Profile for User

- Generate login profile skeleton

`aws iam create-login-profile --generate-cli-skeleton > create-login-profile.json --profile awslearning`

- Edit information for user/password

```console
$ vi create-login-profile.json

# Edit User/pass information as following

{
    "UserName": "Bob",
    "Password": "123123aA@",
    "PasswordResetRequired": true
}
```

- Create Login Profile

`aws iam create-login-profile --cli-input-json file://create-login-profile.json --profile awslearning`

- Give the user programmatic access

`aws iam create-login-profile --user-name Emp1 --password 1231234@ --nopassword-reset-required --profile awslearning`

#### 1.5 Create Login Profile for User

- Generate  login profile skeleton

`aws iam create-login-profile --generate-cli-skeleton > create-login-profile.json`

- Edit information for user/password

```console
$ vi create-login-profile.json

# Edit User/pass information as following

{
    "UserName": "Bob",
    "Password": "123123aA@",
    "PasswordResetRequired": true
}
```

- Create Login Profile

`aws iam create-login-profile --cli-input-json file://create-login-profile.json --profile awslearning`

**Output**
```console
{
    "LoginProfile": {
        "UserName": "Bob",
        "CreateDate": "2023-07-23T03:16:51+00:00",
        "PasswordResetRequired": true
    }
}
```

- Give the user programmatic access

`aws iam create-access-key --user-name Bob --profile awslearning`

**Output**
```console
{
    "AccessKey": {
        "UserName": "Bob",
        "AccessKeyId": "AKIASSWD33PDSOMEWXUF",
        "Status": "Active",
        "SecretAccessKey": "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",
        "CreateDate": "2023-07-23T03:22:15+00:00"
    }
}
```

#### 1.6 Verify the user added to the group.

`aws iam get-group --group-name Prod --profile awslearning`

**Output**
```console
{
    "Users": [
        {
            "Path": "/",
            "UserName": "Bob",
            "UserId": "AIDASSWD33PDQFGMEMJO5",
            "Arn": "arn:aws:iam::177578171335:user/Bob",
            "CreateDate": "2023-07-23T03:09:43+00:00"
        }
    ],
    "Group": {
        "Path": "/",
        "GroupName": "Prod",
        "GroupId": "AGPASSWD33PD6RLQAG76Z",
        "Arn": "arn:aws:iam::177578171335:group/Prod",
        "CreateDate": "2023-07-23T03:08:32+00:00"
    }
}
```

#### 1.7 Create policy EC2FullAccess

- Create file `ec2-full-access-policy.json`

- Edit information for as following

```console
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "ec2:*",
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "elasticloadbalancing:*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "cloudwatch:*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "autoscaling:*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "iam:CreateServiceLinkedRole",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "iam:AWSServiceName": [
            "autoscaling.amazonaws.com",
            "ec2scheduled.amazonaws.com",
            "elasticloadbalancing.amazonaws.com",
            "spot.amazonaws.com",
            "spotfleet.amazonaws.com",
            "transitgateway.amazonaws.com"
          ]
        }
      }
    }
  ]
}
```

- Create policy

`aws iam create-policy --policy-name EC2FullAccess  --policy-document file://ec2-full-access-policy.json --profile awslearning`

**Output**
```console
{
    "Policy": {
        "PolicyName": "EC2FullAccess",
        "PolicyId": "ANPASSWD33PDZCG4RUUWI",
        "Arn": "arn:aws:iam::177578171335:policy/EC2FullAccess",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2023-07-23T04:00:48+00:00",
        "UpdateDate": "2023-07-23T04:00:48+00:00"
    }
}
```

#### 1.8 Attach EC2FullAccess policy to the group

`aws iam attach-group-policy --policy-arn arn:aws:iam::177578171335:policy/EC2FullAccess --group-name Prod --profile awslearning`

#### 1.9 Verify EC2FullAccess policy is attached to the group

`aws iam list-attached-group-policies --group-name Prod --profile awslearning`

**Output**
```console
{
    "AttachedPolicies": [
        {
            "PolicyName": "EC2FullAccess",
            "PolicyArn": "arn:aws:iam::177578171335:policy/EC2FullAccess"
        }
    ]
}
```