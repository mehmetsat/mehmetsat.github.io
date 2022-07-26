+++
title = "Amazon S3"
description = "AWS Notes for the Certification Exam III"
date = "2022-07-26"
author = "mehmet sat"
+++

- **Object-Based Storage** that allows you to upload files
- Files can be from **0b to 5TB**
- **Not** suitable for installing an **operating system** or a running a **database**
- The total volume of the data and the number of the objects are **unlimited.**

- S3 is a **universal namespace** which means that cannot exist 2 files with the same name.
- Example

```jsx
https://**{bucket-name}**.s3.**{Region}**.amazonaws.com/**{key-name}

# succesful CLI or API uploads will response HTTP 200 status code**
```

## Four s3 Object Tips

- Key → object-name
- Value → the data itself
- Version ID → allows multiple versions
- Metadata → Data about the data you are storing e.g. *content-type, last-modified*

## Securing your bucket with s3

- Buckets are **private by default** including the objects in it. You have to allow public access to access from public
- Object **ACLs(Access Control List) →**  You can manage access of individual objects
- Bucket Policies → You can make entire buckets accessible by bucket policies

## Hosting a static website with s3

- You should use bucket policies to make entire buckets public
- You can use s3 for static content only.(video is a static content)
- Automatic Scaling → s3 scales automatically on demand

## Versioning on s3

- All versions of an object stored in s3. This includes writes and even deletes.
- Versioning can be a great Backup tool
- Once enabled cannot be disabled — only suspended
- Can be integrated with Lifecycle Rules to move to another s3 Tier
- It supports MFA.

<aside style="border:solid; border-radius:10px; margin:15px; padding:10px">
💡 To prevent your data from accidental deletes you can activate versioning and activate MFA. By that way you have 2 step protection

</aside>



### 3 Tips for Lifecycle Management

- Automates your objects between different storage tiers.
- Can be used conjunction with versioning
- Can be applied current and previous versions

## S3 Object Lock and Glacier Vault Lock

- Use S3 Object Lock to store objects using **WORM(Write Once Read Many)** model
- Object Lock can be applied to individual object as well as the bucket as a whole
- Object Lock comes with two models:
    - **Governance Model** **→** Users can’t overwrite or delete an object version or alter its lock settings unless they have specific permissions
    - **Compliance Model →** A protected object can’t overwrite or delete by any user including the Root User
- **Glacier Vault Lock →** Allows you to easily deploy and enforce compliance controls. on individual Glacier Vaults. You can specify controls like WORM model, in a vault lock policy and lock the policy from future edits. Once locked policy can no longer be changed

## Encrypting Objects with S3

- **Encryption in transit**
    - SSL/TLS
    - HTTPS

 

- **Encryption at Rest(SSE)**
    - Server-side encryption
        - SSE-S3 (AES 256 bit) → standart
        - SSE-KMS → has limits
        - SSE-C → client handled the encryption

- **Client-side Encryption**
    - You encrypted your files before upload

- **Enforcing encryption with bucket policy** → By that way, the bucket denies all the PUT requests without z-amz-server-side-encryption in the header.

## Optimizing S3 Performance

- S3 by default can handle 3500 PUT/COPY/POST/DELETE and 5000 GET/HEAD per second, per **prefix**
- What is **Prefix?**
    
    <aside style="border:solid; border-radius:10px; margin:15px; padding:10px">
    💡 Prefix is basically the path of the object 
    For example:
    
   
    
    ```jsx
    mybucketname/folder1/subfolder1/myfile.jpg -> /folder1/subfolder1
    ```
     </aside>
    
- You can get better performance by increasing the number of prefixes

<aside style="border:solid; border-radius:10px; margin:15px; padding:10px">
⚠️ If you are using SSE-KMS to encrypt your objects in S3 you must keep in your mind it comes with limits:
Region-specific, its either 5500, 10000, 30000 requests per second (uploading and downloading count towards the KMS quota)
You cannot increase this limit by now

</aside>

- You can use multi-part uploads to increase uploading performance → should be used for any files bigger than 100mb but must be used for bigger than 5GB
- Use S3 byte-range-fetches to increase performance when downloading files from S3. It splits the data into multiple parts and parallelize the downloads

### Backing-up the data with S3 Replication

- You can replicate the objects from one bucket to another
- Objects in an **existing bucket** are **not** replicated automatically
- Source and destination bucket should be **version enabled**
- **Delete markers** are **not** replicated by default
- In a new feature you can replicate the existing objects by **s3 batch replication***

[NEW - Replicate Existing Objects with Amazon S3 Batch Replication | Amazon Web Services](https://aws.amazon.com/blogs/aws/new-replicate-existing-objects-with-amazon-s3-batch-replication/)