---
title: "Importing and Exporting Virtual Machine Images"
permalink: /aws/ec2/import-export
excerpt: "Getting things working with AWS IoT."
header:
  overlay_image: /assets/images/AWS/virtual-machine.png
  teaser: /assets/images/AWS/virtual-machine.png
  overlay_filter: 0.5
last_modified_at: 2018-05-05T15:59:07-04:00
toc: true
author: Jay
---
Some times you might need to use a preconfigured image in cloud environment as well as on-premise servers with virtualization. Amazon Web Services support importing VM to Amazon Machine Images and Exporting Imported instances.
Please note AWS only officially support for exporting previously imported instances. That means you will not be able to export a regular ec2 instance to Virtual Machine Image.

In this guide we will create a Virtual Machine image in Ova format in our local server and import it to AWS. 
There are few things that I have tried and failed.

I tried to use the official Virtual Image provided by Canonical and importing it in to AWS. But did not work.
Then I tried to download it (the VM image) to my local machine and to create a VM using virtual box. It did work either

## What you need

1. `aws cli` configured in your local computer
2. `VirtualBox`


## Create a virtual machine in your local computer

This is a pretty simple step. You just have to download `iso` image from ubuntu official repo and create a virtual machine with VirtualBox.
Then in VirtualBox menu go to `File->Export Appliance` and Export the VM to `ovf` format. I have exported my vm as `ubuntu_vmi.ova`
## Upload Ova Image to S3
Once the export is completed. Create a s3 bucket in AWS S3. I have created a bucket named `ubuntuvirtualmachineimages`. Then copy local `ubuntu_vmi.ova` to that bucket using `aws cli`.

```bash
aws s3 cp <PATH_TO_LOAL_VIRTUAL_MACHINE_IMAGE> s3://<YOUR_S3_BUCKET_NAME>
```

This would take some time to upload.

## Creating Amazon Machine Image from Ova Image

For this step I have followed [this](https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html) aws guide.

1. Create `trust-policy.json`

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": { "Service": "vmie.amazonaws.com" },
         "Action": "sts:AssumeRole",
         "Condition": {
            "StringEquals":{
               "sts:Externalid": "vmimport"
            }
         }
      }
   ]
}
```

You can do the same visually with IAM AWS Console, in Roles tab.

2. Create `role-policy.json`. You have to replace `<YOUR_S3_BUCKET_NAME>` with your bucket name.

```json
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket" 
         ],
         "Resource":[
            "arn:aws:s3:::<YOUR_S3_BUCKET_NAME>",
            "arn:aws:s3:::<YOUR_S3_BUCKET_NAME>/*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "ec2:ModifySnapshotAttribute",
            "ec2:CopySnapshot",
            "ec2:RegisterImage",
            "ec2:Describe*"
         ],
         "Resource":"*"
      }
   ]
}
```

3. Create a role using `trust-policy.json`

```bash
aws iam create-role --role-name vmimport --assume-role-policy-document file://trust-policy.json
```

4. Attache policy to role

```bash
aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json
```

5. Create `containers.json`

```json
[
  {
    "Description": "First disk",
    "Format": "ovf",
    "UserBucket": {
        "S3Bucket": "<YOUR_S3_BUCKET_NAME>",
        "S3Key": "<YOUR_IMAGE_NAME>"
    }
  }
]
```

6. Import the Image
```bash
aws ec2 import-image --description "MyVM" --license-type BYOL --disk-containers file://containers.json
```

7. Check Status of the task

```bash
aws ec2 describe-conversion-tasks --region
```

