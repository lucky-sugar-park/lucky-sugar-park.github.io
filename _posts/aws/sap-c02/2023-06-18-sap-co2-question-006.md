---
title: "Question-006"
last_modified_at: 2023-06-18T19:16:10-05:00
categories:
  - Blog
tags:
  - 
---

```
A retail company needs to provide a series of data files to another company, which is its business partner.
These files are saved in an Amazon S3 bucket under Account A, which belongs to the retail company.  

The business partner company wants one of its IAM users, User_DataProcessor, to access the files from its own AWS account (Account B).
Which combination of steps must the companies take so that User_DataProcessor can access the S3 bucket successfully? (Choose two)  
```

A. Turn on the cross-origin resource sharing (CORS) feature for the S3 bucket in Account A.  
<br/>
B. In Account A, set the S3 bucket policy to the following:  
```
{  
  "Effect": "Allow",  
  "Action": [  
    "s3:GetObject",  
    "s3:ListBucket"  
  ],  
  "Resource:" "arn:aws:s3:::AccountABucketName/*"
}
```
C. In Account A, set the S3 bucket policy to the following:  
```
{  
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::AccountB:user/User_DataProcessor"
  },  
  "Action": [  
    "s3:GetObject",  
    "s3:ListBucket"  
  ],  
  "Resource": [
   "arn:aws:s3:::AccountABucketName/*"
  ]
}
```
D: In Account B, set the permissions of User_DataProcessor to the following:  
```
{  
  "Effect": "Allow",
  "Action": [  
    "s3:GetObject",  
    "s3:ListBucket"  
  ],  
  "Resource": "arn:aws:s3:::AccountABucketName/*"
}
```
E: In Account B, set the permissions of User_DataProcessor to the following:  
```
{  
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::AccountB:user/User_DataProcessor"
  },  
  "Action": [  
    "s3:GetObject",  
    "s3:ListBucket"  
  ],  
  "Resource": [
   "arn:aws:s3:::AccountABucketName/*"
  ]
}
```

<details>
  <summary>정답</summary>
  C, D<br/>
  https://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/  
  https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access-example4.html  
  <br/>
  User_DataProcessor가 S3 bucket에 접근하도록 하기 위해서는 다음의 스텝으로 해야 한다  
  1. Account A에서 S3 bucket 정책을 Account B의 IAM 사용자가 접근할 수 있도록 허용해야 한다  
     -> bucket policy에 Account B에 필요한 엑션을 준다 (GetObject, ListBucket)  
  2. Account B에서 IAM 정책을 하나 만든다 (User_DataProcessor)가 필요한 엑션 (GetObject, ListBucket)을 수행하도록 허용하는 정책  
     -> 이 정책은 s3 bucket과 엑션에 대한 arn을 참조해야 한다  

  cross-origin resources sharing (CORS)은 이 시나리오에서는 불필요하다. 이것은 전형적으로 웹 브라우저가 다른 도메인에서의 접근을 허용할 때에 사용된다.  
</deatils>
